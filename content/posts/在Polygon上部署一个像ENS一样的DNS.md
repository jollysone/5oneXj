---
title: "在Polygon上部署一个像ENS一样的DNS"
slug: solidity-polygon-dns-ens
date: 2022-06-27T11:12:14+08:00
draft: false
author: "5oneXj"

categories: ["基础知识", "Solidity 应用"]
tags: ["solidity", "basic", "polygon", "ENS"]
---

## 设置本地工具

一个名为 Hardhat 的工具，它可以让我们快速编译智能合约并在本地进行测试。

```shell
mkdir cool-domains
cd cool-domains
npm init -y
npm install --save-dev hardhat@latest
```

## 示例项目

选择`“Create a JavaScript project”`选项。然后一直选择 `”Yes“`。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303061657221.png)

```shell
npx hardhat
```
示例项目将要求安装`“@nomicfoundation/hardhat-toolbox”`。 这些是我们稍后会用到的其他好东西:)。

继续安装其他依赖项，以防它没有自动执行。

```shell
npm install --save-dev @nomicfoundation/hardhat-chai-matchers chai @nomiclabs/hardhat-ethers ethers
```

还需要安装一个名为 `OpenZeppelin` 的东西，这是另一个经常用于开发安全智能合约的库。

```shell
npm install @openzeppelin/contracts
```

确保一切都在运作，运行：

```shell
npx hardhat compile
npx hardhat test
```

你看到的应该是这样的界面:

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303061659271.png)

继续并删除 `test` 下的文件 `Lock.js`。 另外，删除 `scripts` 下的 `deploy.js`。 然后，删除 `contracts` 下的 `Lock.sol`。 不要删除实际的文件夹！因为这些都是不需要的示例代码。

在 `contracts` 目录下创建一个名为 `Domains.sol` 的文件。 使用 Hardhat 时文件结构非常重要，**所以，请注意！**

```solidity
// SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.10;

import "hardhat/console.sol";

contract Domains {
  // A "mapping" data type to store their names
  mapping(string => address) public domains;

  constructor() {
      console.log("THIS IS MY DOMAIN CONTRACT. NICE.");
  }

  // A register function that adds their names to our mapping
  function register(string calldata name) public {
      domains[name] = msg.sender;
      console.log("%s has registered a domain!", msg.sender);
  }

  // This will give us the domain owners' address
  function getAddress(string calldata name) public view returns (address) {
      return domains[name];
  }
}
```

进入 `scripts` 目录并创建一个名为 `run.js` 的文件。 这就是 `run.js` 里面的内容：

```js
const main = async () => {
  // 实际上会编译我们的合约，并在 artifacts 目录下生成我们使用合约所需的必要文件。
  const domainContractFactory = await hre.ethers.getContractFactory('Domains');
  const domainContract = await domainContractFactory.deploy();
  await domainContract.deployed();
  console.log("Contract deployed to:", domainContract.address);
};

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

在运行此之前，请务必在您的 `hardhat.config.js` 中将solidity: "0.8.4", 更改为solidity: "0.8.10"。

```shell
npx hardhat run scripts/run.js
```

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303061711435.png)

### Hardhat和 HRE

在这些代码块中，您会经常注意到我们使用了hre.ethers，但是从来没有在任何地方导入过hre？ 这是什么类型的魔法？

直接来自 Hardhat 文档本身，您会注意到这一点：

> The Hardhat Runtime Environment, or HRE for short, is an object containing all the functionality that Hardhat exposes when running a task, test or script. In reality, Hardhat is the HRE.

> 那么这是什么意思？ 每次运行以  npx hardhat  开头的终端命令时，您都会使用代码中指定的  hardhat.config.js  即时构建这个  hre 对象！ 这意味着您永远不必实际对文件进行某种导入，例如：
> const hardhat = require("hardhat")

是时候再次运行了，但是 run.js 需要在我们这样做之前进行更改！

```js
const main = async () => {
  const [owner, randomPerson] = await hre.ethers.getSigners();
  const domainContractFactory = await hre.ethers.getContractFactory('Domains');
  const domainContract = await domainContractFactory.deploy();
  await domainContract.deployed();
  console.log("Contract deployed to:", domainContract.address);
  console.log("Contract deployed by:", owner.address);
  
  const txn = await domainContract.register("doom");
  await txn.wait();

  const domainOwner = await domainContract.getAddress("doom");
  console.log("Owner of domain:", domainOwner);
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

像往常一样运行脚本：

```shell
npx hardhat run scripts/run.js
```





