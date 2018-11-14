---
title : node模块化
date : 2016-11-04
categories: node
---

# node模块化

- Node 中的模块化采用了 CommonJS 模块规范，CommonJS 不仅仅是模块规范，它为 JavaScript 制定了很多服务端的所谓的规范（API），例如二进制操作、网络操作等。

- require()函数中的参数是模块标识符

- exports是module.exports的引用，即`exports=module.exports`，一旦给module.exports和exports重新赋值，两者之间的联系就断开了

## 核心模块
*   核心模块由node环境平台本身提供
*   node把绝大多数的API都封装到了一个具名的核心模块中，如“fs”，“http”等
*   要使用核心模块必须先加载
```javascript
var http = require("模块标识名称");
```
### 第三方模块
- 由社区（第三方）提供，可以是公司也可以是个人
- 例如：moment  时间处理库
- 在node中使用第三方包
  + 通过npm下载
  + 通过require函数引包
  + 查看第三方包的API文档

### 自定义模块
- 在node中，也提供了一种所谓的JavaScript模块化解决方案（类似于requirejs）
- require是一个函数，可以在node中的任何脚本文件中使用
- 在node中，一个文件就是一个模块
- 模块天生就是私有的作用域

## 模块加载机制
- 核心模块
- 先看缓存中是否存在，有则直接拿过来
- 没有则直接加载
- 自定义模块
  + 以 `./` 或者 `../` 或者 `/` 或者 `c:/xxx` 类似于这样的标识路径
  + `/` 是当前文件模块所属磁盘根目录
  + `c:/xxx` 就是绝对路径
- 第三方模块：包  （以moment为例）
  + 先基于当前文件模块所属目录找 node_modules 目录
  + 如果找到，则去该目录中找 moment 目录
  + 如果找到 moment 目录，则找该目录中的 package.json 文件
  + 如果找到 package.json 文件，则找该文件中的 main 属性
  + 如果找到 main 属性，则拿到该属性对应的文件路径
  + 如果找到 moment 目录之后
* 发现没有 package.json 
* 或者 有 package.json 没有 main 属性
* 或者 有 main 属性，但是指向的路径不存在
* 则 node 会默认去看一下 moment 目录中有没有 index.js index.node index.json 文件
  + 如果找不到 index 或者找不到 moment 或者找不到 node_modules
  + 则进入上一级目录找 node_moudles 查找规则同上
  + 如果上一级还找不到，继续向上，一直到当前文件所属磁盘根目录，
  + 如果最后到磁盘根目录还找不到，最后报错：`can not find module xxx`
