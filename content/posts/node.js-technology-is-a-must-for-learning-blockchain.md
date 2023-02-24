---
title: "区块链开发中必学的 Node.js 技术"
date: 2018-03-31T14:06:11+08:00
draft: false
author: "5oneXj"

categories: ["基础知识"]
tags: ["基础知识", "Node.js"]
---

# 1 基础语法
## 1.1 定义变量
1. var 允许对同一个变量重新定义，覆盖原有值。
    ```javascript
    var a = 1;
    var a = 2;
    ```

2. let 允许修改，但不允许重新定义
    ```javascript
    let b = 1;
    b = 2;
    ```

3. const 初始化后不允许修改
    ```javascript
    const c = 100;
    ```

## 1.2 解构赋值
### 1.2.1 数组的解构赋值
```javascript
let arr = [0,1,2,3,4,5,6]
let [a, b, c, d] = arr

console.log(a, b, c)  // 0 1 2
```

### 1.2.2 对象的解构赋值
```javascript
const person = {
  name: '5oneXj', age: 18
}
let {name, age} = person
console.log(name, age)  // 5oneXj 18

// 如果自动推导需要同名，否则需要指定 
let {name:name1, age:age1} = person
console.log(name1, age1)  // 5oneXj 18
```

### 1.2.3 函数参数的解构赋值
```javascript
const person = {
  name: '5oneXj’, age: 18
}

function printPerson({name, age}){
  console.log(`姓名：${name}，年龄：${age}`)
}

printPerson(person) // 姓名：5oneXj，年龄：18
```
## 1.3 箭头函数
```javascript
// 只适合用于普通函数
// 不要用在构造函数、不要用在成员函数、不要用在原型函数

let add = (a, b) => {
  return a + b
}

// 函数内一句可简写
let add = (a, b) => a + b

console.log(add(1, 2)) // 3
```

## 1.4 Class
```javascript
// `函数式语言，所以一切皆函数，所有对象都是从函数原型继承而来`。
class Person {
  constructor(name, age){
    this.name = name;
    this.age = age
  }

  // 注意，函数没有function
  method(){
    console.log('Person method')
  }
}

// 继承
class Man extends Person {
  constructor(name, age){
    super(name, age)
  }

  // 重写父类方法
  method(){
    console.log('Override Person method')
  }
}

let p = new Person('5oneXj', 27)
p.method() // Person method

let m = new Man('sxj', 27)
m.method() // Override Person method
```

# 2 进阶用法
## 2.1 同步调用（阻塞）
```javascript
var fs = require("fs")
var data = fs.readFileSync("test.text", 'utf-8')
console.log( data.toString() )
```

## 2.2 异步调用（非阻塞）
```javascript
var fs = require("fs")
fs.readFile("test.text", 'utf-8', function (err, data){
  if(err) return console.error(err);
  console.log( data.toString() );
})
```

## 2.3 模块导入导出
```javascript
// exports.js
functoin say(){
  return true;
}

functoin hear(){
  return true;
}

// 导出单个
module.exports = say

// 导出多个， 可以使用点函数，例如 test.say()
module.exports = test = {
  say, hear
}

// 导出多个需要指定对应导出方法, let {say, hear} = require(‘./exports’)
module.exports = {
  say, hear
}

let test = require(‘./exports’)
test.say()
test.hear()
```

## 2.4 path模块

```javascript
let path = require('path')
let p = '/Users/jollysone/Downloads/tracks.csv'
path.basename(p) // 返回路径的最后一部分，tracks.csv
path.dirname(p) // 返回路径的目录名，/Users/jollysone/Downloads/
path.extname(p) // 返回路径的后缀，.csv

 // 拼接路径，自动过滤多余斜线，最后一个不过滤，不知道是文件还是目录
path.join('/Users/jollysone/', '/Downloads/', 'tracks.csv/')
// 将一个路径正常化
path.normalize('/Users//jollysone//Downloads//tracks.csv')
// 基于当前node的执行目录与路径名字拼接
path.resolve('tracks.csv') //  /home/tracks.csv
```

## 2.5 文件操作模块(同步异步)

```javascript
var fs = require('fs')

// 同步读取
var data = fs.readFileSync("test.txt", 'utf-8')
// 异步读取
fs.readFile("test.txt", 'utf-8', function (err, data){
  if(err) return console.error(err);
  console.log( data.toString() );
})

// 同步写入
fs.writeFileSync("output.txt", data.toString())
// 异步写入
fs.writeFile("output.txt", data.toString(), (res) => {
  if(res){
    console.log('异步写入失败！');
    return
  }
  console.log('异步写入成功！');
})

// 同步读取文件的状态和信息，可以调取里面的成员变量信息
let stat = fs.statSync("output.txt")
console.log(stat.isFile); // 是否文件
console.log(stat.isDirectory); // 是否目录

// 同步删除文件
fs.unlinkSync("output.txt")
```

## 2.6 Promise

```javascript
// 解决大量缩进的回调函数不易阅读的 callback hell 问题。
// 解决大量 异步调异步的嵌套。
new Promise(回调函数);

// 异步读取文件封装成 Promise
var fs = require('fs')
let filePromise = new Promise(function (success, failed){
  // 异步读取
  fs.readFile("test.txt", 'utf-8', function (err, data){
    if(err){
      failed(err);
    }
    
    // ...
    success(data); // 成功调用
  })
});

filePromise.then(res => {
  console.log('data:', res);
}).catch(err => {
  console.log(err);
})
```

## 2.7 async/await

```javascript
// 接上面 Promise
// 直接将 Promise 异步代码变为同步写法，但是仍然是异步执行的。

async function demo(){
  // 相当于 Promise.then 部分
  try {
    let data = await filePromise
  } catch (e){
    console.log(e);
  }
}

demo();
console.log('我是同步代码');
```

## 2.8 Promise实际应用

```javascript
// 将每个回调函数都封装成一个 Promise，然后用 async/await 将异步代码变为同步写法。

// 异步读取 Promise
let readfilePromise = () => {
  return Promise((success, failed) => {
    fs.readFile("test.txt", 'utf-8', function (err, data){
      if(err){
        failed(err);
      }
      success(data);
    })
  })
}

// 异步写入 Promise
let writefilePromise = (data) => {
  return Promise((success, failed) => {
    fs.writeFile("test.txt", data, function (err){
      if(err){
        failed(err);
      }
      success(‘异步写入成功’);
    })
  })
}

// 异步查询文件状态 Promise
let statfilePromise = () => {
  return Promise((success, failed) => {
    fs.stat("test.txt", function (err, stat){
      if(err){
        failed(err);
      }
      success(stat);
    })
  })
}

// 异步读取 => 异步写入 => 异步查询
let demo = async () => {
  try {
    let data = await readfilePromise();
    let res = await writefilePromise(data);
    console.log('写入结果', res);
    let stat = await statfilePromise();
    console.log('stat:', stat);
  } catch (e){
    console.log(e);
  }
}
```

# 3 NPM使用

```shell
# 装淘宝镜像使用 cnpm 命令
$ npm install cnpm
$ npm i cnpm # 简写

# 在项目初始化生成 package.json 文件，npm 项目都有这个文件
$ npm init

# 跳过向导，快速生成 package.json 文件
$ npm init -y

# 安装指定依赖包 --save 参数安装同时会更新 package.json 文件中依赖项
# npm5 以后就不需要添加
$ npm install 包名 --save

# 一次性安装 package.json 中的依赖项
$ npm install

# 安装指定依赖包
$ npm install 包名
$ npm install —global 包名
$ npm install 包名 包名 包名 包名
$ npm install 包名@版本号

# 卸载指定依赖包
$ npm uninstall 包名

# 查看依赖包信息
$ npm view 包名

# 查看帮助
$ npm 命令 --help

# 查看 npm 配置信息
$ npm config list

# 设置镜像源
$ npm config set registry https://registry.npm.taobao.org

# 清理缓存
$ npm cache clean --force
```