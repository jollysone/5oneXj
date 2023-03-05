---
title: "DAO工具之Gnosis Safe多签钱包"
slug: solidity-multisig-wallet-gnosis-safe
date: 2022-12-25
draft: false
author: "5oneXj"

categories: ["基础知识", "Solidity 应用"]
tags: ["solidity", "basic"]
---

## 什么是Gnosis Safe多签钱包?

自 2017 年以来，`Gnosis`一直在开发基于区块链的资金管理解决方案。 Gnosis Safe 是 Gnosis 开发的多重签名钱包。它是在以太坊上管理数字资产最值得信赖的平台，管理着近 200 亿美元的资金。许多DAO使用它来管理资金。

## Gnosis Safe 的主要特点

- **多重签名：** 用户可以自定义他们的团队管理加密资产的方式，并可选择要求预定义数量的签名来确认交易。比如10个成员一起控制钱包，每笔交易至少需要8个成员签名。
- **安全性：** 合同经过审计和实战测试。
- **Apps 支持：** 用户可以直接在 Gnosis 开发Safe Apps平台上与DeFi 协议进行交互，支持 uniswap、Curve 等应用。
- **WalletConnect：** Gnosis Safe 支持 WalletConnect，方便用户与 Web3 应用程序交互。
- **多链支持：** Gnosis Safe 支持 11 条链，包括 Ethereum、Polygon 和 BSC。

## 如何使用 Gnosis Safe

### 1. 创建Gnosis Safe账户

1. 前往[Gnosis Safe 官网](https://gnosis-safe.io) ，点击左上角的`“Open App”`。单击`Create new Safe`。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303052359694.png)

2. 连接您的钱包并选择您要使用的链。这里我选择`Polygon`链进行演示说明（`低gas费用`）。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303052359353.png)

3. 输入钱包名称。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303060001707.png)

4. 添加多重签名持有者，并选择每笔交易所需的签名者数量。就我而言，`2 个多重签名持有者和每笔交易都需要由 2 个持有者签名`。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303060026567.png)

5. 确认钱包信息。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303060028090.png)

6. 您成功创建了一个 `Gnosis Safe` 钱包。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303060002572.png)

**注意：**此多重签名钱包仅在选定的链上有效。就我而言，polygon链。请不要将其他链的资产发送到此地址。

### 2. 转移资金

**接收资金：**点击左上角“show QR”或“Copy”复制Safe钱包地址。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303060004791.png)

将token发送到复制的地址。转入的token会显示在钱包余额里。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303060005673.png)

要发送token，请单击“New transaction”按钮，然后单击“Send funds”。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303060005295.png)

输入接收人地址，选择token，然后输入发送数量。就我而言，1 Matic。点击“Review”。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303060007792.png)

用一个钱包（metamask）确认，confirm。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303060007655.png)

另一个多重签名持有者的钱包确认，confirm。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303060008168.png)

提交交易，submit。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303060008046.png)

token发送成功！

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303060009595.png)

## 与 Dapp 互动

Gnosis Safe 钱包集成了许多 DApp，例如 DEX 和Yeild协议。此外，Safe钱包支持 WalletConnect，这使您可以将钱包连接到任何支持 WalletConnect 的 Dapp。

![](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303060009389.png)

## 与合约交互

安全钱包支持直接与智能合约交互。您可以点击`“New transaction”`按钮，选择`“Contract interaction”`。然后填写合约地址、合约ABI、函数名、函数值。之后的操作与发送令牌相同。很方便！

## 总结

`Gnosis Safe` 多签钱包是一种去中心化钱包，许多 DeFi 协议和 DAO 都采用它来管理他们的资金。

**优点：** 通过拥有多个持有者来分散，安全，易于使用，支持多链。

**缺点：** 不完全去中心化，因为多重签名持有者的数量通常远小于项目的成员数量。此外，多重签名持有者比非多重签名持有者具有优势。

