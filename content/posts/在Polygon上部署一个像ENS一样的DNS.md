---
title: "在Polygon上部署一个像ENS一样的DNS"
slug: solidity-polygon-dns-ens
date: 2022-06-27T11:12:14+08:00
draft: false
author: "5oneXj"

categories: ["Solidity 应用"]
tags: ["solidity", "polygon", "ENS"]
---

## 初始化项目

一个名为 Hardhat 的工具，它可以让我们快速编译智能合约并在本地进行测试。

```shell
mkdir cool-domains
cd cool-domains

npm init -y
```

### 安装`hardhat`
使用`hardhat`创建例子项目，并删除不需要的示例文件。

```shell
npm install --save-dev hardhat@latest
npx hardhat
```

选择`“Create a JavaScript project”`选项。然后一直回车同意下一步或选择 `”Y“`。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303061657221.png)

### 安装`hardhat-toolbox`
示例项目将要求安装`“@nomicfoundation/hardhat-toolbox”`。 这些是我们稍后会用到的其他好东西:)。

### 安装`chai`和`ethers`

继续安装其他依赖项，以防它没有自动执行。

```shell
npm install --save-dev @nomicfoundation/hardhat-chai-matchers chai @nomiclabs/hardhat-ethers ethers
```

### 安装`OpenZeppelin`

还需要安装一个名为 `OpenZeppelin` 的东西，这是另一个经常用于开发安全智能合约的库。

```shell
npm install @openzeppelin/contracts
```

### 测试编译和运行

确保一切都在运作，运行：

```shell
npx hardhat compile
npx hardhat test
```

你看到的应该是这样的界面:

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303061659271.png)

### 删除示例文件

继续并删除 `test` 下的文件 `Lock.js`。 另外，删除 `scripts` 下的 `deploy.js`。 然后，删除 `contracts` 下的 `Lock.sol`。 不要删除实际的文件夹！因为这些都是不需要的示例代码。

1. 在 `contracts` 目录下创建一个名为 `Domains.sol` 的文件。 使用 Hardhat 时文件结构非常重要，**所以，请注意！**

Solidity 中的字符串很奇怪，所以我们需要一个自定义函数来检查它们的长度。此函数首先将它们转换为字节，`从而提高 gas 效率！`对于新导入，将其放入合同文件夹中名为`“libraries”`的新文件夹中。

```solidity
// SPDX-License-Identifier: MIT

pragma solidity >= 0.8.10;

library StringUtils {
    /**
     * @dev Returns the length of a given string
     *
     * @param s The string to measure the length of
     * @return The length of the input string
     */
    function strlen(string memory s) internal pure returns (uint) {
        uint len;
        uint i = 0;
        uint bytelength = bytes(s).length;
        for(len = 0; i < bytelength; len++) {
            bytes1 b = bytes(s)[i];
            if(b < 0x80) {
                i += 1;
            } else if (b < 0xE0) {
                i += 2;
            } else if (b < 0xF0) {
                i += 3;
            } else if (b < 0xF8) {
                i += 4;
            } else if (b < 0xFC) {
                i += 5;
            } else {
                i += 6;
            }
        }
        return len;
    }
}
```

```solidity
// SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.10;

import { StringUtils } from "./libraries/StringUtils.sol";
import "hardhat/console.sol";

contract Domains {
  // 顶级域名类型
  string public tld;
  // 域名名称 => 所属者地址
  mapping(string => address) public domains;
  // 域名名称 => 解析记录
  mapping(string => string) public records;

  constructor(string memory _tld) payable {
    tld = _tld;
    console.log("%s name service deployed", _tld);
  }

  // 计算域名价格
  function price(string calldata name) public pure returns(uint) {
    uint len = StringUtils.strlen(name);
    require(len > 0);

    if (len == 3) {
        // 5 MATIC = 5 000 000 000 000 000 000 (18 decimals). 
        // 0.5 Matic
        return 5 * 10**17;
    } else if (len == 4) {
        // To charge smaller amounts, reduce the decimals. 
        // This is 0.3
        return 3 * 10**17;
    } else {
        return 1 * 10**17;
    }
  }

  // 注册域名
  function register(string calldata name) public {
    require(domains[name] == address(0));

    uint _price = price(name);
    require(msg.value >= _price, "Not enough Matic paid");

    domains[name] = msg.sender;
    console.log("%s has registered a domain!", msg.sender);
  }

  // 获得域名所有者地址
  function getAddress(string calldata name) public view returns (address) {
    return domains[name];
  }

  // 设置域名的解析记录
  function setRecord(string calldata name, string calldata record) public {
    require(domains[name] == msg.sender);
    records[name] = record;
  }

  // 获得域名的解析记录
  function getRecord(string calldata name) public view returns(string memory) {
    return records[name];
  }
}
```

2. 进入 `scripts` 目录并创建一个名为 `run.js` 的文件。 这就是 `run.js` 里面的内容，可以进行测试等。

```js
const main = async () => {
  // Hardhat自动生成部署所需的钱包地址，通过以下可进行查看
  const [owner, randomPerson] = await hre.ethers.getSigners();
  // 生成合约工厂并部署
  const domainContractFactory = await hre.ethers.getContractFactory('Domains');
  // 设置注册顶级域名 .blog
  const domainContract = await domainContractFactory.deploy('blog');
  // 等待合约被矿工部署到区块链
  await domainContract.deployed();

  console.log("Contract deployed to:", domainContract.address);
  console.log("Contract deployed by:", owner.address);
  
  // 调用函数来注册名称“5oneXj”
  // const txn = await domainContract.register("5oneXj"); // 无需发送费用
  let txn = await domainContract.register("5oneXj",  {value: hre.ethers.utils.parseEther('0.1')});
  await txn.wait();

  // 调用函数来查看“5oneXj”的所有者
  const domainOwner = await domainContract.getAddress("5oneXj");
  console.log("Owner of domain:", domainOwner);

  // 查询合约的余额
  const balance = await hre.ethers.provider.getBalance(domainContract.address);
  console.log("Contract balance:", hre.ethers.utils.formatEther(balance));

  // 测试使用其它用户地址对无权限函数操作
  txn = await domainContract.connect(randomPerson).setRecord("5oneXj", "5oneXj`s blog!");
  await txn.wait();
}

const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.log(error);
    process.exit(1);
  }
};

runMain();
```

3. 进入 `scripts` 目录并创建一个名为 `deploy.js` 的文件，正式部署到测试网络。

```js
const main = async () => {
    const domainContractFactory = await hre.ethers.getContractFactory('Domains');
    const domainContract = await domainContractFactory.deploy("blog");
    await domainContract.deployed();
  
    console.log("Contract deployed to:", domainContract.address);
  
    let txn = await domainContract.register("5oneXj",  {value: hre.ethers.utils.parseEther('0.1')});
    await txn.wait();
    console.log("Minted domain 5oneXj.blog");
  
    txn = await domainContract.setRecord("5oneXj", "5oneXj's blog");
    await txn.wait();
    console.log("Set record for 5oneXj.blog");
  
    const address = await domainContract.getAddress("5oneXj");
    console.log("Owner of domain 5oneXj:", address);
  
    const balance = await hre.ethers.provider.getBalance(domainContract.address);
    console.log("Contract balance:", hre.ethers.utils.formatEther(balance));
  }
  
  const runMain = async () => {
    try {
      await main();
      process.exit(0);
    } catch (error) {
      console.log(error);
      process.exit(1);
    }
  };
  
  runMain();
```

4. 在运行此之前，请务必在您的 `hardhat.config.js` 中将合约版本更改为与合约匹配的版本，`solidity: "0.8.10"`。

```shell
npx hardhat run scripts/run.js
```

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303061711435.png)

### 前端与合约交互

在这些代码块中，您会经常注意到我们使用了hre.ethers，但是从来没有在任何地方导入过hre？ 这是什么类型的魔法？

直接来自 Hardhat 文档本身，您会注意到这一点：

> The Hardhat Runtime Environment, or HRE for short, is an object containing all the functionality that Hardhat exposes when running a task, test or script. In reality, Hardhat is the HRE.

> 那么这是什么意思？ 每次运行以  npx hardhat  开头的终端命令时，您都会使用代码中指定的  hardhat.config.js  即时构建这个 hre 对象！ 这`意味着您永远不必实际对文件进行某种导入`，例如：
> const hardhat = require("hardhat")

**`注意：`Hardhat 将为我们创建一个本地以太坊网络，但只是为了这个合约。 然后在脚本完成后，它将破坏该本地网络。**

## 添加Ploygon测试网络

由于默认情况下 Polygon 网络不在 Metamask 中，因此您需要添加它们。

1. 使用安装了 Metamask 的浏览器进入 [Polygonscan Mumbai](https://mumbai.polygonscan.com/) 网站；

2. 一直滚动到底部 3.点击右下角的“添加孟买网络”按钮；

3. 要获得一些假 MATIC，请在此处访问 Polygon 的[水龙头系统](https://faucet.polygon.technology/)。


## 部署前配置

我们需要从我们的 `hardhat.config.js` 文件开始。 您可以在智能合约项目的根目录中找到它。 在这里，我们将添加我们正在使用的网络和一些超级密钥。

```js
require("@nomiclabs/hardhat-waffle");

module.exports = {
  solidity: "0.8.10",
  networks: {
    mumbai: {
      url: "YOUR_QUICKNODE_MUMBAI_URL",
      accounts: ["YOUR_TEST_WALLET_PRIVATE_KEY"],
    }
  }
};
```

## 运行测试脚本

```shell
npx hardhat run scripts/run.js
```

## 运行部署脚本

一旦您完成配置设置，我们就可以使用我们之前编写的部署脚本进行部署。`从根目录运行命令`。

```shell
npx hardhat run scripts/deploy.js --network mumbai
```

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303111109090.png)

## 在 OpenSea 上查看

前往 [testnets.opensea.io](https://testnets.opensea.io/zh-CN)。 搜索你的合约地址，也就是我们部署到的地址。

如果您不想等待，或者 OpenSea 无法正常工作，请前往 [testnets.dev](https://www.testnets.dev/)。

