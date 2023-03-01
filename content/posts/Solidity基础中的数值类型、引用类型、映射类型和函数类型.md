---
title: "Solidity基础中的数值类型、引用类型、映射类型和函数类型"
slug: solidity-basic
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

## 1 数值类型(Value Type)
### 布尔型
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

### 整型
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

### 地址类型
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

### 定长字节数组
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

### 枚举 enum
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

## 2 引用类型(Reference Type)

包括数组（array），结构体（struct）和映射（mapping），这类变量占空间大，赋值时候直接传递地址（类似指针）。由于这类变量比较复杂，占用存储空间大，我们在使用时必须要声明数据存储的位置。

### 数组 array
数组（`Array`）是`solidity`常用的一种变量类型，用来存储一组数据（整数，字节，地址等等）。数组分为固定长度数组和可变长度数组两种：

- 固定长度数组：在声明时指定数组的长度。用`T[k]`的格式声明，其中`T`是元素的类型，`k`是长度，例如：

    ```solidity
        // 固定长度 Array
        uint[8] array1;
        bytes1[5] array2;
        address[100] array3;
    ```

- 可变长度数组（动态数组）：在声明时不指定数组的长度。用`T[]`的格式声明，其中`T`是元素的类型，例如：

    ```solidity
        // 可变长度 Array
        uint[] array4;
        bytes1[] array5;
        address[] array6;
        bytes array7;
    ```

    **注意**：`bytes`比较特殊，是数组，但是不用加`[]`。另外，不能用`byte[]`声明单字节数组，可以使用`bytes`或`bytes1[]`。`bytes` 比 `bytes1[]` 省gas。

#### 创建数组的规则
在solidity里，创建数组有一些规则：

- 对于`memory`修饰的`动态数组`，可以用`new`操作符来创建，但是必须声明长度，并且声明后长度不能改变。例子：

    ```solidity
        // memory动态数组
        uint[] memory array8 = new uint[](5);
        bytes memory array9 = new bytes(9);
    ```

- 数组字面常数(Array Literals)是写作表达式形式的数组，用方括号包着来初始化array的一种方式，并且里面每一个元素的type是以第一个元素为准的，例如`[1,2,3]`里面所有的元素都是`uint8`类型，因为在solidity中如果一个值没有指定type的话，默认就是最小单位的该type，这里 `uint` 的默认最小单位类型就是`uint8`。而`[uint(1),2,3]`里面的元素都是`uint`类型，因为第一个元素指定了是`uint`类型了，我们都以第一个元素为准。

- 下面的例子中，如果没有对传入 `g()` 函数的数组进行 `uint` 转换，是会报错的。

    ```solidity
    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract C {
        function f() public pure {
            g([uint(1), 2, 3]);
        }
        function g(uint[3] memory) public pure {
            // ...
        }
    }
    ```

- 如果创建的是动态数组，你需要一个一个元素的赋值。

    ```solidity
        uint[] memory x = new uint[](3);
        x[0] = 1;
        x[1] = 3;
        x[2] = 4;
    ```

#### 数组成员
- `length`: 数组有一个包含元素数量的`length`成员，`memory`数组的长度在创建后是固定的。
- `push()`: `动态数组`拥有`push()`成员，可以在数组最后添加一个`0`元素，并返回该元素的引用。
- `push(x)`: `动态数组`拥有`push(x)`成员，可以在数组最后添加一个`x`元素。
- `pop()`: `动态数组`拥有`pop()`成员，可以移除数组最后一个元素。

### 结构体 struct
`Solidity`支持通过构造结构体的形式定义新的类型。结构体中的元素可以是原始类型，也可以是引用类型；结构体可以作为数组或映射的元素。创建结构体的方法：
```solidity
    // 结构体
    struct Student{
        uint256 id;
        uint256 score; 
    }

    Student student; // 初始一个student结构体
```

给结构体赋值的四种方法：

```solidity
    //  给结构体赋值
    // 方法1:在函数中创建一个storage的struct引用
    function initStudent1() external{
        Student storage _student = student; // assign a copy of student
        _student.id = 11;
        _student.score = 100;
    }
```

```solidity
     // 方法2:直接引用状态变量的struct
    function initStudent2() external{
        student.id = 1;
        student.score = 80;
    }
```

```solidity
    // 方法3:构造函数式
    function initStudent3() external {
        student = Student(3, 90);
    }
```

```solidity
     // 方法4:key value
    function initStudent4() external {
        student = Student({id: 4, score: 60});
    }
```

## 3 映射类型(Mapping Type)

### 映射Mapping
在映射中，人们可以通过键（`Key`）来查询对应的值（`Value`），比如：通过一个人的`id`来查询他的钱包地址。

声明映射的格式为`mapping(_KeyType => _ValueType)`，其中`_KeyType`和`_ValueType`分别是`Key`和`Value`的变量类型。例子：

```solidity
    mapping(uint => address) public idToAddress; // id映射到地址
    mapping(address => address) public swapPair; // 币对的映射，地址到地址
```  

### 映射的规则
- **规则1**：映射的`_KeyType`只能选择`solidity`默认的类型，比如`uint`，`address`等，不能用自定义的结构体。而`_ValueType`可以使用自定义的类型。下面这个例子会报错，因为`_KeyType`使用了我们自定义的结构体：

    ```solidity
        // 我们定义一个结构体 Struct
        struct Student{
            uint256 id;
            uint256 score; 
        }
        mapping(Student => uint) public testVar;
    ```

- **规则2**：映射的存储位置必须是`storage`，因此可以用于合约的状态变量，函数中的`storage`变量，和library函数的参数（见[例子](https://github.com/ethereum/solidity/issues/4635)）。不能用于`public`函数的参数或返回结果中，因为`mapping`记录的是一种关系 (key - value pair)。

- **规则3**：如果映射声明为`public`，那么`solidity`会自动给你创建一个`getter`函数，可以通过`Key`来查询对应的`Value`。

- **规则4**：给映射新增的键值对的语法为`_Var[_Key] = _Value`，其中`_Var`是映射变量名，`_Key`和`_Value`对应新增的键值对。例子：

    ```solidity
        function writeMap (uint _Key, address _Value) public{
            idToAddress[_Key] = _Value;
        }
    ```

### 映射的原理

- **原理1**: 映射不储存任何键（`Key`）的资讯，也没有length的资讯。

- **原理2**: 映射使用`keccak256(abi.encodePacked(key, slot))`当成offset存取value，其中`slot`是映射变量定义所在的插槽位置。

- **原理3**: 因为Ethereum会定义所有未使用的空间为0，所以未赋值（`Value`）的键（`Key`）初始值都是各个type的默认值，如uint的默认值是0。

## 4 函数类型(Function Type)

### Solidity中的函数
Solidity官方文档里把函数归到数值类型，但我觉得差别很大，所以单独分一类。我们先看一下solidity中函数的形式：
```solidity
    function <function name>(<parameter types>) {internal|external|public|private} [pure|view|payable] [returns (<return types>)]
```
看着些复杂，咱们从前往后一个一个看（方括号中的是可写可不写的关键字）：

1. `function`：声明函数时的固定用法，想写函数，就要以function关键字开头。

2. `<function name>`：函数名。

3. `(<parameter types>)`：圆括号里写函数的参数，也就是要输入到函数的变量类型和名字。

4. `{internal|external|public|private}`：函数可见性说明符，一共4种。

    - `public`: 内部外部均可见。
    - `private`: 只能从本合约内部访问，继承的合约也不能用。
    - `external`: 只能从合约外部访问（但是可以用`this.f()`来调用，`f`是函数名）。
    - `internal`: 只能从合约内部访问，继承的合约可以用。

    **Note 1**: 合约中定义的函数需要明确指定可见性，它们没有默认值。
    
    **Note 2**: `public|private|internal` 也可用于修饰状态变量。 `public`变量会自动生成同名的`getter`函数，用于查询数值。
    
    **Note 3**: 没有标明可见性类型的状态变量，默认为`internal`。

5. `[pure|view|payable]`：决定函数权限/功能的关键字。`payable`（可支付的）很好理解，带着它的函数，运行的时候可以给合约转入`ETH`。`pure`和`view`的介绍见下一节。

6. `[returns ()]`：函数返回的变量类型和名称。

### 到底什么是`Pure`和`View`？
我刚开始学`solidity`的时候，一直不理解`pure`跟`view`关键字，因为别的语言没有类似的关键字。`solidity`加入这两个关键字，我认为是因为`gas fee`。合约的状态变量存储在链上，`gas fee`很贵，如果不改变链上状态，就不用付`gas`。包含`pure`跟`view`关键字的函数是不改写链上状态的，因此用户直接调用他们是不需要付gas的（合约中非`pure`/`view`函数调用它们则会改写链上状态，需要付gas）。

在以太坊中，以下语句被视为修改链上状态：

1. 写入状态变量。
2. 释放事件。
3. 创建其他合约。
4. 使用`selfdestruct`.
5. 通过调用发送以太币。
6. 调用任何未标记`view`或`pure`的函数。
7. 使用低级调用（low-level calls）。
8. 使用包含某些操作码的内联汇编。

- `pure`，中文意思是“纯”，在`solidity`里理解为“纯函数”。包含`pure`关键字的函数，不能读取也不能写入存储在链上的状态变量。

- `view`，“看”，在`solidity`里理解为“看客”。包含`view`关键字的函数，能读取但也不能写入状态变量。

- 不写`pure`也不写`view`，函数既可以读取也可以写入状态变量。

### 代码示例
#### 1. pure v.s. view

我们在合约里定义一个状态变量 `number = 5`。
```solidity
    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.4;
    contract FunctionTypes{
        uint256 public number = 5;
    }
```
定义一个`add()`函数，每次调用，每次给`number + 1`。
```solidity
    // 默认
    function add() external{
        number = number + 1;
    }
```

如果`add()`包含了`pure`关键字，例如 `function add() pure external`，就会报错。因为`pure`（纯函数）是不配读取合约里的状态变量的，更不配改写。那`pure`函数能做些什么？举个例子，你可以给函数传递一个参数 `_number`，然后让他返回 `_number+1`。

```solidity
    // pure: 纯函数
    function addPure(uint256 _number) external pure returns(uint256 new_number){
        new_number = _number + 1;
    }
```

如果`add()`包含`view`，比如`function add() view external`，也会报错。因为`view`能读取，但不能够改写状态变量。可以稍微改写下方程，让他不改写`number`，而是返回一个新的变量。

```solidity
    // view: 只能读
    function addView() external view returns(uint256 new_number) {
        new_number = number + 1;
    }
```

#### 2. internal v.s. external
```solidity
    // internal: 内部
    function minus() internal {
        number = number - 1;
    }

    // 合约内的函数可以调用内部函数
    function minusCall() external {
        minus();
    }
```
我们定义一个`internal`的`minus()`函数，每次调用使得`number`变量减1。由于是`internal`，只能由合约内部调用，而外部不能。因此，我们必须再定义一个`external`的`minusCall()`函数，来间接调用内部的`minus()`。


#### 3. payable
```solidity
    // payable: 递钱，能给合约支付eth的函数
    function minusPayable() external payable returns(uint256 balance) {
        minus();    
        balance = address(this).balance;
    }
```
我们定义一个`external payable`的`minusPayable()`函数，间接的调用`minus()`，并且返回合约里的`ETH`余额（`this`关键字可以让我们引用合约地址)。
我们可以在调用`minusPayable()`时，往合约里转入1个`ETH`。我们可以在返回的信息中看到，合约的余额是1 ETH。

### 函数返回值
#### return和returns
`Solidity`有两个关键字与函数输出相关：`return`和`returns`，他们的区别在于：
- `returns`加在函数名后面，用于声明返回的变量类型及变量名；
- `return`用于函数主体中，返回指定的变量。

```solidity
    // 返回多个变量
    function returnMultiple() public pure returns(uint256, bool, uint256[3] memory){
            return(1, true, [uint256(1),2,5]);
        }
```
上面这段代码中，我们声明了`returnMultiple()`函数将有多个输出：`returns(uint256, bool, uint256[3] memory)`，接着我们在函数主体中用`return(1, true, [uint256(1),2,5])`确定了返回值。

#### 命名式返回
我们可以在`returns`中标明返回变量的名称，这样`solidity`会自动给这些变量初始化，并且自动返回这些函数的值，不需要加`return`。

```solidity
    // 命名式返回
    function returnNamed() public pure returns(uint256 _number, bool _bool, uint256[3] memory _array){
        _number = 2;
        _bool = false; 
        _array = [uint256(3),2,1];
    }
```
在上面的代码中，我们用`returns(uint256 _number, bool _bool, uint256[3] memory _array)`声明了返回变量类型以及变量名。这样，我们在主体中只需要给变量`_number`，`_bool`和`_array`赋值就可以自动返回了。

当然，你也可以在命名式返回中用`return`来返回变量：
```solidity
    // 命名式返回，依然支持return
    function returnNamed2() public pure returns(uint256 _number, bool _bool, uint256[3] memory _array){
        return(1, true, [uint256(1),2,5]);
    }
```
#### 解构式赋值
`solidity`使用解构式赋值的规则，支持读取函数的全部或部分返回值。
- 读取所有返回值：声明变量，并且将要赋值的变量用`,`隔开，按顺序排列。

    ```solidity
            uint256 _number;
            bool _bool;
            uint256[3] memory _array;
            (_number, _bool, _array) = returnNamed();
    ```

- 读取部分返回值：声明要读取的返回值对应的变量，不读取的留空。下面这段代码中，我们只读取`_bool`，而不读取返回的`_number`和`_array`：

    ```solidity
            (, _bool2, ) = returnNamed();
    ```
