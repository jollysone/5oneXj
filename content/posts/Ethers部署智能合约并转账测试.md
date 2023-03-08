---
title: "Ethers部署智能合约并转账测试"
slug: web3-ethers-deploy-contract
date: 2021-02-05T20:15:10+08:00
draft: false
author: "5oneXj"

categories: ["基础知识", "Ethers.js"]
tags: ["基础知识", "ethers.js"]
---

## 部署智能合约

在以太坊上，智能合约的部署是一种特殊的交易：将编译智能合约得到的字节码发送到0地址。如果这个合约的构造函数有参数的话，需要利用`abi.encode`将参数编码为字节码，然后附在在合约字节码的尾部一起发送。

## 合约工厂

`ethers.js`创造了合约工厂`ContractFactory`类型，方便开发者部署合约。你可以利用合约`abi`，编译得到的字节码`bytecode`和签名者变量`signer`来创建合约工厂实例，为部署合约做准备。

```js
const contractFactory = new ethers.ContractFactory(abi, bytecode, signer);
```
**注意**：如果合约的构造函数有参数，那么在`abi`中必须包含构造函数。

在创建好合约工厂实例之后，可以调用它的`deploy`函数并传入合约构造函数的参数`args`来部署并获得合约实例：
```js
const contract = await contractFactory.deploy(args)
```

你可以利用下面两种命令，等待合约部署在链上确认，然后再进行交互。
```js
await contractERC20.deployed()
//或者 await contract.deployTransaction.wait()
```

## 部署ERC20代币合约

1. 创建`provider`和`wallet`变量。
    ```js
    import { ethers } from "ethers";

    // 利用Infura的rpc节点连接以太坊网络
    const INFURA_ID = '184d4c5ec78243c290d151d3f1a10f1d'
    // 连接Rinkeby测试网
    const provider = new ethers.providers.JsonRpcProvider(`https://rinkeby.infura.io/v3/${INFURA_ID}`)

    // 利用私钥和provider创建wallet对象
    const privateKey = '0x227dbb8586117d55284e26620bc76534dfbd2394be34cf4a09cb775d593b6f2b'
    const wallet = new ethers.Wallet(privateKey, provider)
    ```

2. 准备ERC20合约的字节码和ABI。因为ERC20的构造函数含有参数，因此我们必须把它包含在ABI中。合约的字节码可以从`remix`的编译面板中点击`Bytecode`按钮，把它复制下来，其中"object"字段对应的数据就是字节码。如果部署在链上的合约，你可以在etherscan的Contract页面的`Contract Creation Code`中找到。

    ```js
    // ERC20的人类可读abi
    const abiERC20 = [
        "constructor(string memory name_, string memory symbol_)",
        "function name() view returns (string)",
        "function symbol() view returns (string)",
        "function totalSupply() view returns (uint256)",
        "function balanceOf(address) view returns (uint)",
        "function transfer(address to, uint256 amount) external returns (bool)",
        "function mint(uint amount) external",
    ];
    // 填入合约字节码，在remix中，你可以在两个地方找到Bytecode
    // 1. 编译面板的Bytecode按钮
    // 2. 文件面板artifact文件夹下与合约同名的json文件中
    // 里面"bytecode"属性下的"object"字段对应的数据就是Bytecode，挺长的，608060起始
    // "object": "608060405260646000553480156100...
    const bytecodeERC20 = ""
    ```

    ![获取字节码](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081814148.png)
    ![](img/6-1.png)


3. 创建合约工厂`ContractFactory`实例。

    ```js
    const factoryERC20 = new ethers.ContractFactory(abiERC20, bytecodeERC20, wallet);
    ```

4. 调用工厂合约的`deploy()`函数并填入构造函数的参数（代币名称和代号），部署`ERC20`代币合约并获得合约实例。你可以利用：
    - `contract.address`获取合约地址，
    - `contract.deployTransaction`获取部署详情，
    - `contractERC20.deployed()`等待合约部署在链上确认。

    ```js
    // 1. 利用contractFactory部署ERC20代币合约
    console.log("\n1. 利用contractFactory部署ERC20代币合约")
    // 部署合约，填入constructor的参数
    const contractERC20 = await factoryERC20.deploy("WTF Token", "WTF")
    console.log(`合约地址: ${contractERC20.address}`);
    console.log("部署合约的交易详情")
    console.log(contractERC20.deployTransaction)
    console.log("\n等待合约部署上链")
    await contractERC20.deployed()
    console.log("合约已上链")
    ```

    ![部署合约](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081815706.png)

5. 在合约上链后，调用`name()`和`symbol()`函数打印代币名称和代号，然后调用`mint()`函数给自己铸造`10,000`枚代币。

    ```js
    // 打印合约的name()和symbol()，然后调用mint()函数，给自己地址mint 10,000代币
    console.log("\n2. 调用mint()函数，给自己地址mint 10,000代币")
    console.log(`合约名称: ${await contractERC20.name()}`)
    console.log(`合约代号: ${await contractERC20.symbol()}`)
    let tx = await contractERC20.mint("10000")
    console.log("等待交易上链")
    await tx.wait()
    console.log(`mint后地址中代币余额: ${await contractERC20.balanceOf(wallet.address)}`)
    console.log(`代币总供给: ${await contractERC20.totalSupply()}`)
    ```

    ![铸造代币](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081815484.png)

6. 调用`transfer()`函数，给V神转账`1,000`枚代币。

    ```js
    // 3. 调用transfer()函数，给V神转账1000代币
    console.log("\n3. 调用transfer()函数，给V神转账1,000代币")
    tx = await contractERC20.transfer("vitalik.eth", "1000")
    console.log("等待交易上链")
    await tx.wait()
    console.log(`V神钱包中的代币余额: ${await contractERC20.balanceOf("vitalik.eth")}`)
    ```

    ![转账](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081815183.png)
