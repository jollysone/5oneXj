---
title: "Ethers中的BigNumber和单位转换"
slug: web3-ethers-units
date: 2021-02-07T10:15:10+08:00
draft: false
author: "5oneXj"

categories: ["基础知识", "Ethers.js"]
tags: ["基础知识", "ethers.js"]
---


## `BigNumber`

以太坊中，许多计算都对超出`JavaScript`整数的安全值（js中最大安全整数为`9007199254740991`）。因此，`ethers.js`使用`BigNumber`类安全地对任何数量级的数字进行数学运算。在`ethers.js`中，大多数需要返回值的操作将返回`BigNumber`，而接受值的参数也会接受它们。

### 创建`BigNumber`实例

你可以利用`ethers.BigNumber.from()`函数将`string`，`number`，`BigNumber`等类型转换为`BigNumber`。

代码参考：https://docs.ethers.io/v5/api/utils/bignumber/#BigNumber

**注意**，超过js最大安全整数的数值将不能转换。
```js
const oneGwei = ethers.BigNumber.from("1000000000"); // 从十进制字符串生成
console.log(oneGwei)
console.log(ethers.BigNumber.from("0x3b9aca00")) // 从hex字符串生成
console.log(ethers.BigNumber.from(1000000000)) // 从数字生成
// 不能从js最大的安全整数之外的数字生成BigNumber，下面代码会报错
// ethers.BigNumber.from(Number.MAX_SAFE_INTEGER);
console.log("js中最大安全整数：", Number.MAX_SAFE_INTEGER)
```

![BigNumber](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303092127830.png)

### `BigNumber`运算

`BigNumber`支持很多运算，例如加减乘除、取模`mod`，幂运算`pow`，绝对值`abs`等运算：

```js
// 运算
console.log("加法：", oneGwei.add(1).toString())
console.log("减法：", oneGwei.sub(1).toString())
console.log("乘法：", oneGwei.mul(2).toString())
console.log("除法：", oneGwei.div(2).toString())
// 比较
console.log("是否相等：", oneGwei.eq("1000000000"))
```

![BigNumber运算](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303092127292.png)

## 单位转换

在以太坊中，`1 ether`等于`10^18 wei`。下面列出了一些常用的单位：

![常用单位](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303092128606.png)

在应用中，我们经常将数值在用户可读的字符串（以`ether`为单位）和机器可读的数值（以`wei`为单位）之间转换。例如，钱包可以为用户界面指定余额（以`ether`为单位）和`gas`价格（以`gwei`为单位），但是在发送交易时，两者都必须转换成以`wei`为单位的数值。`ethers.js`提供了一些功能函数，方便这类转换。

- `formatUnits(变量, 单位)`：格式化，小单位转大单位，比如`wei` -> `ether`，在显示余额时很有用。参数中，单位填位数（数字）或指定的单位（字符串）。

    ```js
    //代码参考：https://docs.ethers.io/v5/api/utils/display-logic/#utils-parseUnits
    console.group('\n2. 格式化：小单位转大单位，formatUnits');
    console.log(ethers.utils.formatUnits(oneGwei, 0));
    // '1000000000'
    console.log(ethers.utils.formatUnits(oneGwei, "gwei"));
    // '1.0'
    console.log(ethers.utils.formatUnits(oneGwei, 9));
    // '1.0'
    console.log(ethers.utils.formatUnits(oneGwei, "ether"));
    // `0.000000001`
    console.log(ethers.utils.formatUnits(1000000000, "gwei"));
    // '1.0'
    console.log(ethers.utils.formatEther(oneGwei));
    // `0.000000001` 等同于formatUnits(value, "ether")
    console.groupEnd();
    ```

    ![formatUnits](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303092128517.png)

- `parseUnits`：解析，大单位转小单位，比如`ether` -> `wei`，在将用户输入的值转为`wei`为单位的数值很有用。参数中，单位填位数（数字）或指定的单位（字符串）。

    ```js
    // 3. 解析：大单位转小单位
    // 例如将ether转换为wei：parseUnits(变量, 单位),parseUnits默认单位是 ether
    // 代码参考：https://docs.ethers.io/v5/api/utils/display-logic/#utils-parseUnits
    console.group('\n3. 解析：大单位转小单位，parseUnits');
    console.log(ethers.utils.parseUnits("1.0").toString());
    // { BigNumber: "1000000000000000000" }
    console.log(ethers.utils.parseUnits("1.0", "ether").toString());
    // { BigNumber: "1000000000000000000" }
    console.log(ethers.utils.parseUnits("1.0", 18).toString());
    // { BigNumber: "1000000000000000000" }
    console.log(ethers.utils.parseUnits("1.0", "gwei").toString());
    // { BigNumber: "1000000000" }
    console.log(ethers.utils.parseUnits("1.0", 9).toString());
    // { BigNumber: "1000000000" }
    console.log(ethers.utils.parseEther("1.0").toString());
    // { BigNumber: "1000000000000000000" } 等同于parseUnits(value, "ether")
    console.groupEnd();
    ```

    ![parseUnits](https://raw.githubusercontent.com/jollysone/Picture-Library/master/blog/202303092128687.png)

