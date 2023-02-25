# 区块链“跨链”是什么？为什么要“跨链”？


# 1 跨链概念
跨链是通过连接相对独立的区块链系统，实现资产、数据等的跨链互操作，跨链的主要实现形式包括跨链资产互换和跨链资产转移。

# 跨链技术
2013年之前，区块链的发展主要集中在单一区块链。2013年以来，跨链技术蓬勃发展，跨链的几种主要模式相继被提出。发展到现在，包含`公证人机制`、`侧链/中继`、`哈希锁定`、`分布式私钥控制`这四种主流的跨链技术。

实现跨链的两个关键问题分别是跨链交易的原子性和跨链交易验证。哈希锁定模式利用哈希锁和时间锁能够保证跨链交易的原子性，即只有满足一定的时间条件和哈希条件交易才能够完成，从而实现跨链资产互换。对于相互独立的两条区块链而言，为实现跨链资产转移，不得不依赖于外部第三方进行信息交互，根据第三方的工作范围可以分为公证人机制和中继模式。在公证人机制下，第三方负责数据收集和交易验证；在中继模式下，第三方仅负责数据收集，交易验证由目标链完成。

&gt; 总体而言，跨链技术在过去几年间得到了迅速发展，相关的项目层出不穷。现有的跨链相关项目中，基于侧链/中继模式的项目占比最高；基于哈希锁定的闪电网络自主网上线以来节点数量、通道数量和网络容量不断增长，技术可行性得到了较好的验证；通信协议簇（通过规定一系列通信数据格式与协议规范等实现区块链接入）类项目未来能否成为主流跨链方案一定程度上取决于业界对于相关标准规范的接受度。但是目前跨链技术尚未完全成熟和广泛应用,仍有较大的提升空间。此外除了跨链本身的技术形态演进，跨链技术未来的发展也与跨链技术的应用模式密切相关。

# 2 跨链代表
早期以Interledger与btc Relay为代表的跨链技术更多关注的是资产转移，现有跨链技术更关注跨链基础设施的落地，强调智能合约和去中心化，并接入各种跨链应用，侧重商业落地。目前跨链项目众多，最为大家所熟知的莫过于跨链双雄：`Polkadot` 和 `Cosmos`。

`Polkadot是一种异构多链技术`：主要由中继链、平行链和跨链桥组成。中继链相当于一个有很多插槽的插座，平行链就是各类家用电器，这些家电插上电，就能获得电力并运行。而平行链要与Polkadot之外的公链进行沟通的话就需要通过跨链桥来搭建在中继链上。

`Cosmos是一个支持跨链交互的异构网络`：它的目标是创建一个区块链互联网，允许大量自主且易开发的区块链互相扩展和交互。Cosmos的技术特点主要集中于三方面：一是Tendermint共识协议，属于拜占庭容错类的共识算法，在拜占庭节点数不超过 1/3的情况下，能够保证共识的安全性。二是Cosmos SDK，将共识算法和网络模块封装起来，形成一套开箱即用的区块链开发框架，开发者可以在其基础上快速定制开发适合自己需求的公链；三是IBC，也就是区块链间通信协议，它允许不同的区块链实现相互通信，也是Cosmos跨链的核心。

# 总结
随着区块链技术的不断发展与创新，大量区块链项目如雨后春笋般问世，万链共存是未来的趋势。跨链技术`使不同领域不同功能链之间能够更好地实现互联互通与价值流动`，同时还`极大地降低了传输成本`。当资产能够简单、安全的自由流通时，区块链生态将迎来大爆炸式增长。