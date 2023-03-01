---
title: "Solidity基础中的数值类型、引用类型、映射类型和函数类型"
slug: solidity-1
date: 2021-01-22T11:12:14+08:00
draft: false
author: "5oneXj"

categories: ["基础知识", "Solidity 基础"]
tags: ["solidity", "basic"]
---

{{< admonition quote "Solidity中的变量类型" >}}

1. **数值类型(Value Type)**：包括布尔型，整数型等等，这类变量赋值时候直接传递数值。

2. **引用类型(Reference Type)**：包括数组和结构体，这类变量`占空间大`，赋值时候直接传递地址（类似指针）。

3. **映射类型(Mapping Type)**: Solidity里的哈希表。

4. **函数类型(Function Type)**：Solidity文档里把函数归到数值类型，但我觉得他跟其他类型差别很大，所以单独分一类。

{{< /admonition >}}

## 数值类型
### 1. 布尔型
布尔型是二值变量，取值为`true`或`false`。
```solidity
    bool public _bool = true; // 布尔值
```
布尔值的运算符，包括：

- `!` （逻辑非）
- `&&` （逻辑与， "and" ）
- `||` （逻辑或， "or" ）
- `==` （等于）
- `!=` （不等于）

代码：
```solidity
    // 布尔运算
    bool public _bool1 = !_bool; //取非
    bool public _bool2 = _bool && _bool1; //与
    bool public _bool3 = _bool || _bool1; //或
    bool public _bool4 = _bool == _bool1; //相等
    bool public _bool5 = _bool != _bool1; //不相等
```

上面的代码中：变量`_bool`的取值是`true`；`_bool1`是`_bool`的非，为`false`；`_bool && _bool1`为`false`；`_bool || _bool1`为`true`；`_bool == _bool1`为`false`；`_bool != _bool1`为`true`。

**值得注意的是：**`&&` 和 `||`运算符遵循短路规则，这意味着，假如存在`f(x) || g(y)`的表达式，如果`f(x)`是`true`，`g(y)`不会被计算，即使它和`f(x)`的结果是相反的

### 2. 整型
整型是`solidity`中的整数，最常用的包括
```solidity
    // 整型
    int public _int = -1; // 整数，包括负数
    uint public _uint = 1; // 正整数
    uint256 public _number = 20220330; // 256位正整数
```
常用的整型运算符包括：

- **比较运算符（返回布尔值）**： `<=`， `<`， `==`， `!=`， `>=`， `>` 
- **算数运算符**： `+`， `-`， `*`， `/`， `%`（取余），`**`（幂）

代码：
```solidity
    // 整数运算
    uint256 public _number1 = _number + 1; // +，-，*，/
    uint256 public _number2 = 2**2; // 指数
    uint256 public _number3 = 7 % 2; // 取余数
    bool public _numberbool = _number2 > _number3; // 比大小
```

### 3. 地址类型
地址类型(address)有两类：
- **普通地址（address）**: 存储一个 20 字节的值（以太坊地址的大小）。
- **payable address**: 比普通地址多了`transfer`和`send`两个成员方法，用于接收转账。

代码
```solidity
    // 地址
    address public _address = 0x7A58c0Be72BE218B41C608b7Fe7C5bB630736C71;
    address payable public _address1 = payable(_address); // payable address，可以转账、查余额
    // 地址类型的成员
    uint256 public balance = _address1.balance; // balance of address
```

### 4. 定长字节数组
字节数组`bytes`分两种:

- 定长: 属于数值类型，根据每个元素存储数据的大小分为 `byte`, `bytes8`, `bytes32` 等类型，每个元素最多存储 32 bytes数据。数组长度在声明之后不能改变。
- 不定长: 属于引用类型，数组长度在声明之后可以改变，包括 `bytes` 等，之后的章节会详细介绍。

代码：
```solidity
    // 固定长度的字节数组
    bytes32 public _byte32 = "MiniSolidity"; 
    bytes1 public _byte = _byte32[0]; 
```
上面代码中，`MiniSolidity`变量以字节的方式存储进变量`_byte32`。如果把它转换成`16进制`为，就是：`0x4d696e69536f6c69646974790000000000000000000000000000000000000000`

`_byte`变量的值为`_byte32`的第一个字节，即`0x4d`。

### 5. 枚举 enum
枚举（`enum`）是`solidity`中用户定义的数据类型。它主要用于为`uint`分配名称，使程序易于阅读和维护。它与`C语言`中的`enum`类似，使用名称来代替从`0`开始的`uint`：
```solidity
    // 用enum将uint 0， 1， 2表示为Buy, Hold, Sell
    enum ActionSet { Buy, Hold, Sell }
    // 创建enum变量 action
    ActionSet action = ActionSet.Buy;
```
它可以显式的和`uint`相互转换，并会检查转换的正整数是否在枚举的长度内，不然会报错：
```solidity
    // enum可以和uint显式的转换
    function enumToUint() external view returns(uint){
        return uint(action);
    }
```
`enum`的一个比较冷门的变量，几乎没什么人用。

