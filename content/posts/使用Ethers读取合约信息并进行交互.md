---
title: "使用Ethers读取合约信息并进行交互"
slug: web3-ethers-read-contract
date: 2021-02-03T20:15:10+08:00
draft: false
author: "5oneXj"

categories: ["基础知识", "Ethers.js"]
tags: ["基础知识", "ethers.js"]
---


## Contract类

在`ethers`中，`Contract`类是部署在以太坊网络上的合约（`EVM`字节码）的抽象。通过它，开发者可以非常容易的对合约进行读取`call`和交易`transcation`，并可以获得交易的结果和事件。以太坊强大的地方正是合约，所以对于合约的操作要熟练掌握。

## 创建Contract变量

### 只读和可读写Contract

`Contract`对象分为两类，只读和可读写。只读`Contract`只能读取链上合约信息，执行`call`操作，即调用合约中`view`和`pure`的函数，而不能执行交易`transaction`。创建这两种`Contract`变量的方法有所不同：

- 只读`Contract`：参数分别是合约地址，合约`abi`和`provider`变量（只读）。

    ```javascript
    const contract = new ethers.Contract(`address`, `abi`, `provider`);
    ```

- 可读写`Contract`：参数分别是合约地址，合约`abi`和`signer`变量。`Signer`签名者是`ethers`中的另一个类，用于签名交易。

    ```javascript
    const contract = new ethers.Contract(`address`, `abi`, `signer`);
    ```

**注意** `ethers`中的`call`指的是只读操作，与`solidity`中的`call`不同。

## 读取合约信息

### 1. 创建Provider

我们使用Infura节点的API Key创建`Provider`（见[第2讲：Provider](../02_Provider/readme.md)）：
```javascript
import { ethers } from "ethers";
// 利用Infura的rpc节点连接以太坊网络
// 准备Infura API Key
const INFURA_ID = ''
// 连接以太坊主网
const provider = new ethers.providers.JsonRpcProvider(`https://mainnet.infura.io/v3/${INFURA_ID}`)
```

### 2. 创建只读Contract实例

创建只读Contract实例需要填入`3`个参数，分别是合约地址，合约`abi`和`provider`变量。合约地址可以在网上查到，`provider`变量上一步我们已经创建了，那么`abi`怎么填？

`ABI` (Application Binary Interface) 是与以太坊智能合约交互的标准。`ethers`支持两种`abi`填法：

- **方法1.**  直接输入合约`abi`。你可以从`remix`的编译页面中复制，在本地编译合约时生成的`artifact`文件夹的`json`文件中得到，或者从`etherscan`开源合约的代码页面得到。我们用这个方法创建`WETH`的合约实例：

```javascript
// 第1种输入abi的方式: 复制abi全文
// WETH的abi可以在这里复制：https://etherscan.io/token/0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2#code
const abiWETH = '[{"constant":true,"inputs":[],"name":"name","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view",...太长后面省略...';
const addressWETH = '0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2' // WETH Contract
const contractWETH = new ethers.Contract(addressWETH, abiWETH, provider)

```

![在Etherscan得到ABI](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081748884.png)



- **方法2.** 由于`abi`可读性太差，`ethers`创新的引入了`Human-Readable Abi`（人类可读abi）。开发者可以通过`function signature`和`event signature`来写`abi`。我们用这个方法创建稳定币`DAI`的合约实例：

```javascript
// 第2种输入abi的方式：输入程序需要用到的函数，逗号分隔，ethers会自动帮你转换成相应的abi
// 人类可读abi，以ERC20合约为例
const abiERC20 = [
    "function name() view returns (string)",
    "function symbol() view returns (string)",
    "function totalSupply() view returns (uint256)",
    "function balanceOf(address) view returns (uint)",
];
const addressDAI = '0x6B175474E89094C44Da98b954EedeAC495271d0F' // DAI Contract
const contractDAI = new ethers.Contract(addressDAI, abiERC20, provider)
```

### 3. 读取`WETH`和`DAI`的链上信息

我们可以利用只读`Contract`实例调用合约的`view`和`pure`函数，获取链上信息：

```javascript
const main = async () => {
    // 1. 读取WETH合约的链上信息（WETH abi）
    const nameWETH = await contractWETH.name()
    const symbolWETH = await contractWETH.symbol()
    const totalSupplyWETH = await contractWETH.totalSupply()
    console.log("\n1. 读取WETH合约信息")
    console.log(`合约地址: ${addressWETH}`)
    console.log(`名称: ${nameWETH}`)
    console.log(`代号: ${symbolWETH}`)
    console.log(`总供给: ${ethers.utils.formatEther(totalSupplyWETH)}`)
    const balanceWETH = await contractWETH.balanceOf('vitalik.eth')
    console.log(`Vitalik持仓: ${ethers.utils.formatEther(balanceWETH)}\n`)

    // 2. 读取DAI合约的链上信息（IERC20接口合约）
    const nameDAI = await contractDAI.name()
    const symbolDAI = await contractDAI.symbol()
    const totalSupplDAI = await contractDAI.totalSupply()
    console.log("\n2. 读取DAI合约信息")
    console.log(`合约地址: ${addressDAI}`)
    console.log(`名称: ${nameDAI}`)
    console.log(`代号: ${symbolDAI}`)
    console.log(`总供给: ${ethers.utils.formatEther(totalSupplDAI)}`)
    const balanceDAI = await contractDAI.balanceOf('vitalik.eth')
    console.log(`Vitalik持仓: ${ethers.utils.formatEther(balanceDAI)}\n`)
}

main()
```

可以看到，用两种方法创建的合约实例都能成功与链上交互。V神的钱包里有`0.05 WETH`及`555508 DAI`，见下图。

![成功读取V神WETH和DAI持仓](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081751275.png)

**说明**
我们可以通过[以太坊浏览器](https://etherscan.io/token/0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2#readContract) 验证V神钱包里的`WETH`余额, 是否与通过`Contract`读取的一致。 通过[ENS](https://app.ens.domains/name/vitalik.eth/details) 查到V神钱包地址是`0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045`,然后通过合约方法`balanceOf`得到余额正好是`0.05 WETH`, 结论是一致！

![V神WETH余额](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081753180.png)


## 合约交互
### 创建可写`Contract`变量

声明可写的`Contract`变量的规则：
```js
const contract = new ethers.Contract(address, abi, signer)
```

其中`address`为合约地址，`abi`是合约的`abi`接口，`signer`是`wallet`对象。注意，这里你需要提供`signer`，而在声明可读合约时你只需要提供`provider`。

你也可以利用下面的方法，将可读合约转换为可写合约：

```js
const contract2 = contract.connect(signer)
```


读取合约信息不需要`gas`。这里我们介绍写入合约信息，你需要构建交易，并且支付`gas`。该交易将由整个网络上的每个节点以及矿工验证，并改变区块链状态。

你可以用下面的方法进行合约交互：

```js
// 发送交易
const tx = await contract.METHOD_NAME(args [, overrides])
// 等待链上确认交易
await tx.wait() 
```

其中`METHOD_NAME`为调用的函数名，`args`为函数参数，`[, overrides]`是可以选择传入的数据，包括：
- gasPrice：gas价格
- gasLimit：gas上限
- value：调用时传入的ether（单位是wei）
- nonce：nonce

**注意：** 此方法不能获取合约运行的返回值，如有需要，要使用`Solidity`事件记录，然后利用交易收据去查询。

### 与测试网`WETH`合约交互

`WETH` (Wrapped ETH)是`ETH`的带包装版本，将以太坊原生代币用智能合约包装成了符合`ERC20`的代币。

1. 创建`provider`，`wallet`变量。

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
2. 创建可写`WETH`合约变量，我们在`ABI`中加入了3个我们要调用的函数：
    - `balanceOf()`：查询地址的`WETH`余额。
    - `deposit()`：将转入合约的`ETH`转为`WETH`。
    - `transfer()`：转账。

    ```js
    // WETH的ABI
    const abiWETH = [
        "function balanceOf(address) public view returns(uint)",
        "function deposit() public payable",
        "function transfer(address, uint) public returns (bool)",
        "function withdraw(uint) public",
    ];
    // WETH合约地址（Rinkeby测试网）
    const addressWETH = '0xc778417e063141139fce010982780140aa0cd5ab' // WETH Contract

    // 声明可写合约
    const contractWETH = new ethers.Contract(addressWETH, abiWETH, wallet)
    ```

3. 读取账户`WETH`余额，可以看到余额为`1.001997`。

    ```js
    const address = await wallet.getAddress()
    // 读取WETH合约的链上信息（WETH abi）
    console.log("\n1. 读取WETH余额")
    const balanceWETH = await contractWETH.balanceOf(address)
    console.log(`存款前WETH持仓: ${ethers.utils.formatEther(balanceWETH)}\n`)
    ```

    ![读取WETH余额](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081808487.png)

4. 调用`WETH`合约的`deposit()`函数，将`0.001 ETH`转换为`0.001 WETH`，打印交易详情和余额。`deposit()`函数没有参数，可以看到余额变为`1.002997`。

    ```js
        console.log("\n2. 调用desposit()函数，存入0.001 ETH")
        // 发起交易
        const tx = await contractWETH.deposit({value: ethers.utils.parseEther("0.001")})
        // 等待交易上链
        await tx.wait()
        console.log(`交易详情：`)
        console.log(tx)
        const balanceWETH_deposit = await contractWETH.balanceOf(address)
        console.log(`存款后WETH持仓: ${ethers.utils.formatEther(balanceWETH_deposit)}\n`)
    ```

    ![调用deposit](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081808534.png)

5. 调用`WETH`合约的`transfer()`函数，给V神转账`0.001 WETH`，并打印余额。可以看到余额变为`1.001997`。

    ```js
        console.log("\n3. 调用transfer()函数，给vitalik转账0.001 WETH")
        // 发起交易
        const tx2 = await contractWETH.transfer("vitalik.eth", ethers.utils.parseEther("0.001"))
        // 等待交易上链
        await tx2.wait()
        const balanceWETH_transfer = await contractWETH.balanceOf(address)
        console.log(`转账后WETH持仓: ${ethers.utils.formatEther(balanceWETH_transfer)}\n`)
    ```

    ![给V神转WETH](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081809247.png)

**注意**：观察`deposit()`函数和`balanceOf()`函数，为什么他们的返回值不一样？为什么前者返回一堆数据，而后者只返回确定的值？

> 这是因为对于钱包的余额，它是一个只读操作，读到什么就是什么。而对于一次函数的调用，并不知道数据何时上链，所以只会返回这次交易的信息。总结来说，就是对于非`pure`/`view`函数的调用，会返回交易的信息。如果想知道函数执行过程中合约变量的变化，可以在合约中使用`emit`输出事件，并在返回的`transaction`信息中读取事件信息来获取相应的值。