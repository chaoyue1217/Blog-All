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
# 暂时性死区（TDZ）

理解暂时性死区，可以先理解一下js的解析编译机制：
如 `var a=2`
在编译上述代码时，会经过以下两个步骤：
- 遇到 `var a`，编译器会询问作用域是否已经有一个该名称变量存在于同一作用域中。如果存在，编译器会忽略该声明，继续编译；否则，会要求作用域在当前作用域集合中声明一个新的变量，并命名为a。
- 接下来编译器会为引擎声明运行时所需的代码，处理赋值操作。引擎运行时会首先询问作用域，当前作用于中是否存在一个a变量，存在的话，引擎就会使用这个变量，不存在，引擎就会继续向父级作用域查找。

**总结：**变量的赋值操作会执行两个动作，首先编译器会在当前作用域中声明一个变量(如果之前没有声明)，然后在运行时引擎会在作用域中查找该变量，如果能找到就对他赋值。

**使用let和const声明的变量，在到达声明处之前都是无法访问的，试图访问会报错。**
JS引擎在解析代码时，遇到var声明的变量，会先将其放至作用域的集合中，也就是提升至当前作用域的顶端。
而遇到let和const时，会将其放入暂时性死区中，只有在执行到该变量的声明语句时，变量才会从暂时性死区中移除并使用。

# 使用let比var的优点

- let只在自己的块级作用域内有效，没有变量提升，不会在内存创建多余变量
  如：
```js
if(condition){
  let value = 123;
}
```
只有在condition为true的情况下，才会创建value变量。
如果使用的是var声明，会进行变量提升，不论condition是否为true，都会存在一个value变量在内存中。

- 不会污染全局变量
```js
let RegExp = "hello";
window.RegExp == RegExp; //false
```
使用let或const声明的全局变量，只是屏蔽了全局属性，而没有对其修改。
如果使用var声明，则是直接修改了全局变量，因为var声明的属性是默认挂载到window上的。

- 循环内使用，能正确获取到想要的值