---
title: let和const
date: 2016-12-20
categories: ES6
---

# 一、let关键字

* let 用来声明变量。
* let声明的变量会创建一个自己的块级作用域，且定义的变量只在自己的块级作用域内起作用
```javascript
  if(true){
  let str = "abc";
  }
  console.log(str);//报错，str未定义。因为let声明的变量会产生块级作用域
```
```javascript
//如果使用var声明i会出现以下结果
var arr = [];
for(var i=0;i<3;i++){
	arr[i] = function(){
		//当前的环境下没有i，所以就会向上查找，当找到i时，循环已经结束，此时i=3
		console.log(i);
	}
}
arr[0]();//3
arr[1]();//3
arr[2]();//3

//使用let声明i时，会出现想要的结果;此时则不用再使用闭包
var arr = [];
for(let i=0;i<3;i++){
	arr[i] = function(){
		console.log(i);
	}
}
arr[0]();//0
arr[1]();//1
arr[2]();//2
```
* let声明的变量不会变量提升
```javascript
console.log(n);//报错，n未定义
let n = 10;
```
* 在一个块级作用域内，不允许重复声明同一变量
* 在函数内，不能用let重复声明函数的形参
```javascript
var a = "abc";
let a = "aaa";//报错，a已经被声明

function fn(name){
	let name = "zhangsan";//报错，name已经被声明
}
fn("lisi");
```
# 二、const关键字
* **const实际上保证的是变量指向的内存地址不可改变**
* 声明一个常量：constant。常量的值是不可变的
* 试图修改常量的值会报错
* 在一个块级作用域内，不允许重复声明同一变量
* const声明的变量不会变量提升
* 声明后必须赋值
```javascript
console.log(name);//报错，常量不会进行变量声明提升
const name = "zhangsan";
name = "lisi";//报错，常量的值不可修改
var name = "lisi";//报错，该常量已经被声明

const age;//报错，常量声明必须被赋值
```
* 在使用const声明对象时，不可变的是对象的地址。
```javascript
const person = {
  name:"zhangsan",
  age:20
}
person.age = 22;
console.log(person.age);//22
//该程序正常运行，因为person代表的是一个地址，该地址不会变
```
