---
title: "Ethers中的Provider提供器"
slug: web3-ethers-provider
date: 2021-02-01T19:15:10+08:00
draft: false
author: "5oneXj"

categories: ["基础知识", "Ethers.js"]
tags: ["基础知识", "ethers.js"]
---

{{< admonition quote "引言" >}}

可以使用ethers.js的`Provider`类，然后利用它连接上Infura节点，读取链上的信息。

{{< /admonition >}}


## Provider类

`Provider`类是对以太坊网络连接的抽象，为标准以太坊节点功能提供简洁、一致的接口。在`ethers`中，`Provider`不接触用户私钥，只能读取链上信息，不能写入，这一点比`web3.js`要安全。

除了默认提供者`defaultProvider`以外，`ethers`中最常用的是`jsonRpcProvider`，可以让用户连接到特定节点服务商的节点。

## jsonRpcProvider

### 创建节点服务商的API Key

首先，需要去节点服务商`Infura`或者`Alchemy`的网站注册并创建`API Key`。

![Infura API Key](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081736468.png)

### 连接Infura节点

这里，我们用Infura节点作为例子。在创建好Infura API Key之后，就可以利用`ethers.provider.JsonRpcProvider()`方法来创建`Provider`变量。`JsonRpcProvider()`以节点服务的`url`作为参数。

在下面这个例子中，我们分别创建连接到`ETH`主网和`Goerli`测试网的`provider`：

```javascript
// 利用Infura的rpc节点连接以太坊网络，并填入Infura API Key
const INFURA_ID = ''
// 连接以太坊主网
const providerETH = new ethers.providers.JsonRpcProvider(`https://mainnet.infura.io/v3/${INFURA_ID}`)
// 连接Goerli测试网
const providerGoerli = new ethers.providers.JsonRpcProvider(`https://goerli.infura.io/v3/${INFURA_ID}`)
```

### 利用`Provider`读取链上数据

`Provider`类封装了一些方法，可以便捷的读取链上数据：

**1.** 利用`getBalance()`函数读取主网和测试网V神的`ETH`余额：

```javascript
    // 1. 查询vitalik在主网和Goerli测试网的ETH余额
    console.log("1. 查询vitalik在主网和Goerli测试网的ETH余额");
    const balance = await providerETH.getBalance(`vitalik.eth`);
    const balanceGoerli = await providerGoerli.getBalance(`vitalik.eth`);
    // 将余额输出在console（主网）
    console.log(`ETH Balance of vitalik: ${ethers.utils.formatEther(balance)} ETH`);
    // 输出Goerli测试网ETH余额
    console.log(`Goerli ETH Balance of vitalik: ${ethers.utils.formatEther(balanceGoerli)} ETH`);
```

![查询vitalik在主网和Goerli测试网的ETH余额](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081739480.png)

**2.** 利用`getNetwork()`查询`provider`连接到了哪条链，`homestead`代表`ETH`主网：

```javascript
    // 2. 查询provider连接到了哪条链
    console.log("\n2. 查询provider连接到了哪条链")
    const network = await providerETH.getNetwork();
    console.log(network);
```

![查询provider连接到了哪条链](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081740154.png)

**3.** 利用`getBlockNumber()`查询当前区块高度：

```javascript
    // 3. 查询区块高度
    console.log("\n3. 查询区块高度")
    const blockNumber = await providerETH.getBlockNumber();
    console.log(blockNumber);
```

![查询区块高度](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081740261.png)

**4.** 利用`getGasPrice()`查询当前`gas price`，返回的数据格式为`BigNumber`，可以用`BigNumber`类的`toNumber()`或`toString()`方法转换成数字和字符串。

```javascript
    // 4. 查询当前gas price
    console.log("\n4. 查询当前gas price")
    const gasPrice = await providerETH.getGasPrice();
    console.log(gasPrice);
```

![查询当前gas price](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081741899.png)


**5.** 利用`getFeeData()`查询当前建议的`gas`设置，返回的数据格式为`BigNumber`。

```javascript
    // 5. 查询当前建议的gas设置
    console.log("\n5. 查询当前建议的gas设置")
    const feeData = await providerETH.getFeeData();
    console.log(feeData);
```

![查询当前建议的gas设置](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081741030.png)

**6.** 利用`getBlock()`查询区块信息，参数为要查询的区块高度：

```javascript
    // 6. 查询区块信息
    console.log("\n6. 查询区块信息")
    const block = await providerETH.getBlock(0);
    console.log(block);
```

![查询区块信息](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081742378.png)

**7.** 利用`getCode()`查询某个地址的合约`bytecode`，参数为合约地址，下面例子中用的主网`WETH`的合约地址：

```javascript
    // 7. 给定合约地址查询合约bytecode，例子用的WETH地址
    console.log("\n7. 给定合约地址查询合约bytecode，例子用的WETH地址")
    const code = await providerETH.getCode("0xc778417e063141139fce010982780140aa0cd5ab");
    console.log(code);
```

![给定合约地址查询合约字节码](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303081743565.png)





