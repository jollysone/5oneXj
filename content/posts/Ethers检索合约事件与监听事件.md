---
title: "Ethers检索合约事件与监听事件"
slug: web3-ethers-listener-event
date: 2021-02-06T20:15:10+08:00
draft: false
author: "5oneXj"

categories: ["基础知识", "Ethers.js"]
tags: ["基础知识", "ethers.js"]
---

## 事件 Event

智能合约释放出的事件存储于以太坊虚拟机的日志中。日志分为两个主题`topics`和数据`data`部分，其中事件哈希和`indexed`变量存储在`topics`中，作为索引方便以后搜索；没有`indexed`变量存储在`data`中，不能被直接检索，但可以存储更复杂的数据结构。

以ERC20代币中的`Transfer`转账事件为例，在合约中它是这样声明的：

```solidity
event Transfer(address indexed from, address indexed to, uint256 amount);
```

它共记录了3个变量`from`，`to`和`amount`，分别对应代币的发出地址，接收地址和转账数量，其中`from`和`to`前面带有`indexed`关键字。转账时，`Transfer`事件会被记录，可以在`etherscan`中[查到](https://rinkeby.etherscan.io/tx/0x8cf87215b23055896d93004112bbd8ab754f081b4491cb48c37592ca8f8a36c7)。

![Transfer事件](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303092102696.png)

从上图中可以看到，`Transfer`事件被记录到了EVM的日志中，其中`Topics`包含3个数据，分别对应事件哈希，发出地址`from`，和接收地址`to`；而`Data`中包含一个数据，对应转账数额`amount`。

## 检索事件

我们可以利用`Ethers`中合约类型的`queryFilter()`函数读取合约释放的事件。

```js
const transferEvents = await contract.queryFilter('事件名', 起始区块, 结束区块)
```

`queryFilter()`包含3个参数，分别是事件名（必填），起始区块（选填），和结束区块（选填）。检索结果会以数组的方式返回。

**注意**：要检索的事件必须包含在合约的`abi`中。

## 例子：检索`WETH`合约中的`Transfer`事件

1. 创建`provider`。
    ```js
    import { ethers } from "ethers";
    // 利用Infura的rpc节点连接以太坊网络
    // 准备Infura API Key
    const INFURA_ID = '184d4c5ec78243c290d151d3f1a10f1d'
    // 连接rinkeby测试网
    const provider = new ethers.providers.JsonRpcProvider(`https://rinkeby.infura.io/v3/${INFURA_ID}`)
    ```

2. 创建包含检索事件的`abi`。
    ```js
    // WETH ABI，只包含我们关心的Transfer事件
    const abiWETH = [
        "event Transfer(address indexed from, address indexed to, uint amount)"
    ];
    ```

3. 声明`WETH`合约实例。

    ```js
    // 测试网WETH地址
    const addressWETH = '0xc778417e063141139fce010982780140aa0cd5ab'
    // 声明合约实例
    const contract = new ethers.Contract(addressWETH, abiWETH, provider)
    ```

4. 获取过去10个区块内的`Transfer`事件，并打印出1个。我们可以看到，`topics`中有3个数据，对应事件哈希，`from`，和`to`；而`data`中只有一个数据`amount`。另外，`ethers`还会根据`ABI`自动解析事件，结果显示在`args`成员中。
    ```js
    // 得到当前block
    const block = await provider.getBlockNumber()
    console.log(`当前区块高度: ${block}`);
    console.log(`打印事件详情:`);
    const transferEvents = await contract.queryFilter('Transfer', block - 10, block)
    // 打印第1个Transfer事件
    console.log(transferEvents[0])
    ```

    ![打印事件](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303092103084.png)

5. 读取事件的解析结果。

    ```
    // 解析Transfer事件的数据（变量在args中）
    console.log("\n2. 解析事件：")
    const amount = ethers.utils.formatUnits(ethers.BigNumber.from(transferEvents[0].args["amount"]), "ether");
    console.log(`地址 ${transferEvents[0].args["from"]} 转账${amount} WETH 到地址 ${transferEvents[0].args["to"]}`)
    ```

    ![解析事件](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303092104131.png)

## 监听合约事件

### `contract.on`
在`ethersjs`中，合约对象有一个`contract.on`的监听方法，让我们持续监听合约的事件：

```js
contract.on("eventName", function)
```
`contract.on`有两个参数，一个是要监听的事件名称`"eventName"`，需要包含在合约`abi`中；另一个是我们在事件发生时调用的函数。

### `contract.once`

合约对象有一个`contract.once`的监听方法，让我们只监听一次合约释放事件，它的参数与`contract.on`一样：

```js
contract.once("eventName", function)
```

## 监听`USDT`合约

1. 声明`provider`：Alchemy是一个免费的ETH节点提供商。

    ```js
    import { ethers } from "ethers";
    // 准备 Alchemy API  
    const ALCHEMY_MAINNET_URL = 'https://eth-mainnet.g.alchemy.com/v2/oKmOQKbneVkxgHZfibs-iFhIlIAl6HDN';
    // 连接主网 provider
    const provider = new ethers.providers.JsonRpcProvider(ALCHEMY_MAINNET_URL);
    ```

2. 声明合约变量：我们只关心`USDT`合约的`Transfer`事件，把它填入到`abi`中就可以。如果你关心其他函数和事件的话，可以在[etherscan](https://etherscan.io/address/0xdac17f958d2ee523a2206206994597c13d831ec7#code)上找到。

    ```js
    // USDT的合约地址
    const contractAddress = '0xdac17f958d2ee523a2206206994597c13d831ec7'
    // 构建USDT的Transfer的ABI
    const abi = [
        "event Transfer(address indexed from, address indexed to, uint value)"
    ];
    // 生成USDT合约对象
    const contractUSDT = new ethers.Contract(contractAddress, abi, provider);
    ```

3. 利用`contract.once()`函数，监听一次`Transfer`事件，并打印结果。

    ```js
        // 只监听一次
        console.log("\n1. 利用contract.once()，监听一次Transfer事件");
        contractUSDT.once('Transfer', (from, to, value)=>{
        // 打印结果
        console.log(
            `${from} -> ${to} ${ethers.utils.formatUnits(ethers.BigNumber.from(value),6)}`
        )
        })
    ```

    ![只监听一次](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303092105704.png)

4. 利用`contract.on()`函数，持续监听`Transfer`事件，并打印结果。

    ```js
        // 持续监听USDT合约
        console.log("\n2. 利用contract.on()，持续监听Transfer事件");
        contractUSDT.on('Transfer', (from, to, value)=>{
        console.log(
        // 打印结果
        `${from} -> ${to} ${ethers.utils.formatUnits(ethers.BigNumber.from(value),6)}`
        )
        })
    ```

    ![持续监听](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303092106881.png)

## 总结

`ethers`中最简单的链上监听功能，`contract.on()`和`contract.once()`。通过上述方法，可以你可以监听指定合约的指定事件。**以及要注意的一点**：要检索的事件必须包含在合约`abi`中。