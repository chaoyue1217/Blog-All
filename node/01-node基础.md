---
title : 初始node
date : 2016-11-03
categories: node
---

# 初识node

- node是一个构建于Chrome V8引擎之上的一个Javascript运行环境
- JavaScript是一个动态脚本语言
- JavaScript之前的解析和执行一直停留在浏览器环境
  + 通过编译解析引擎：解析和执行JavaScript脚本代码
  + Chrome V8引擎是所有浏览器里面解析和执行JS代码最快的
  + Node是将V8引擎移植出来
  + 又基于V8引擎之上构建了一系列的API
- 在node中没有界面，不再需要关心HTML和CSS。和DOM、BOM、style等没有关系
  + 没有window和document
  + 提供了文件操作和网络操作等的API
  + 让JS代码有了服务端的能力
- node特点
  + 事件驱动
  + 异步编程（非阻塞IO模型）
    * 异步：回调
  + 跨平台
  + 单线程
- npm是世界上最大的开源库生态系统
  + 在npm上发布的第三方包已经接近35万个
## 文件操作fs
### 文件读取readFile
* readFile有两个参数
* 第一个：文件路径
* 第二个：回调函数
  * 如果读取失败，则error是一个错误对象，data的undefined
  * 如果读取成功，error 是null,data 是读取的数据
```javascript
var fs = require("fs");
fs.readFile("./00-helloworld.js",function(error,data){

	if(error){
		console.log("读取文件失败");
	}else{
		console.log(data.toString());
	}
});

```
### 文件写入writeFile
* write有三个参数
* 第一个：文件路径，如果没有则自动创建
* 第二个：写入的内容
* 第三个：回调函数
  * 回调函数接收一个参数，error 
  * 成功的时候，error是null
  * 失败的时候error是错误对象
```javascript
var fs = require("fs");

fs.writeFile("a.txt","你好，nodejs",function(error){
	
	if(error){
		return console.log(err);
	}
	console.log("写入文件成功");

});
```
###文件操作的同步和异步
- 异步操作都是通过一个回调函数来获取异步操作的结果
- 异步操作一般会在回调函数的第一个参数提供一个error
  - 如果操作成功：error就是null
  - 如果操作失败：error就是错误对象
  - 因此在异步操作中，应该优先判断error是否存在
- 在异步操作中无法使用try-catch 来捕获异常
- 同步操作时，需要定义变量接收操作结果
- 同步操作使用try-catch来捕获异常
- 异步例子
```javascript
var fs = require("fs");
console.log(111);
fs.readFile("./data/a.txt",function(err,data){
	console.log(222);
	if(err){
		throw err;
	}
	console.log(data.toString);
})
console.log(333);
//输出111 333 222 文件数据
```

- 同步例子
```javascript
var fs = require("fs");
console.log(111);
var data = fs.readFileSync("./data/a.txt");
console(222);
console(data);
console.log(333);
//输出111 222 文件内容 333   正常的解析顺序输出
```

## http服务
- 简单的http服务例子
```javascript
var http = require("http");
// 创建服务器
var server = http.createServer();
//回调函数接收两个参数  一个是request，一个是response
// request是所有请求的入口
// request：请求对象  接收客户端请求的一些数据信息
// response：响应对象  给本地请求发送响应数据
server.on("request",function(req,res){
	console.log("收到请求");
	res.write("hello");
	res.write("world");
	//发送响应数据后，必须结束响应
	//否则浏览器会一直等待
	res.end();
});

// IP地址：定位一台计算机
// 端口号：定位具体的应用程序

// 监听端口号 启动服务
server.listen(3000,function(){
	console.log("服务启动成功了");
});
```
* 地址栏输入文件路径，读取文件中的数据
```javascript
var http = require("http");
var fs = require("fs");

var server = http.createServer();

server.on("request",function(req,res){
	//获取请求的路径，从“/”开始
  	// ip地址后没有写任何东西时，浏览器默认会加上/	
	var url = req.url;
	var filePath = "."+url;
	fs.readFile(filePath,function(err,data){
		if(err){
			return res.end("404 Not Found");
		}
		res.end(data);
	});
});
server.listen(3000,function(){
	console.log("running.......");
});
```

## 问题
* 为什么在终端的任何目录下输入`node`命令，执行的都是node.exe这个可执行文件？   环境变量

