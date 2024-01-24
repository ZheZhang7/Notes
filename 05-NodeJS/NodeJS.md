# NodeJS

node.JS 是 js 在服务端的运行环境

## 常见模块

1. buffer 缓冲区，用于存储二进制数据，操作方法与数组类似
2. fs 文件系统模块，主要使用异步，文件较大时多次读取
3. path 路径模块
4. http 模块，创建 web 服务

## 模块化

[参考链接](https://juejin.cn/post/6844903576309858318?searchId=202308112200235D11EBBE1155E12E0DCD)

1. commonJS
2. AMD
3. CMD
4. ES6

## require import 区别

1. require(AMD)--> module.exports/exports(commonJS)--> 运行时调用
2. import(ES6)--> export(ES6)--> 编译时调用

module.exports/exports 输出值的 copy
export 输出值的引用

export = {} module.exports = xxx exports.xxx = {}

## module.exports/exports

module.exports 和 exports 都是对外暴露成员的

exports 本质是变量，它是 module.exports 的地址引用

module.exports 和 exports 指向的是同一个对象，但是以 module.exports 的暴露结果为准，若使用 exports = obj 形式，意味着 exports 指向新对象，但是 module.exports 没有挂载时，会导出空对象
