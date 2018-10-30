---
title: string
date: 2016-12-26
categories: ES6
---
# 字符串新特性
## 模板字符串
- 使用反引号，即数字1左边的按键
- 变量使用`${变量}`表示
```javascript
var str1 = "hello";
var str2 = "world";
var str = `javascript say ${str1} to ${str2}`;
console.log(str);//javascript hello say world 
```
* 可以多行定义字符串，字符串中的换行、缩进和空格都会保留在输出中
```javascript
var str = `my name is
=======`;
console.log(str); 
//my name is
//=======
```
* ${}中可以放任意的js表达式
```javascript
var n1 = 11;
var n2 = 22;
//可以是运算表达式
var sum = `sum is ${n1+n2}`;
console.log(sum);//sum is 33

var obj = {n1:2,n2:3};
//可以是逻辑表达式
var ret = `n1是否大于n2: ${obj.n1>obj.n2}`;
console.log(ret);//n1是否大于n2: false

var ret = `返回值是: ${foo()}`;
//可以是函数调用
console.log(ret);//返回值是: 我是返回值
```
## 标签模板
* 模板是指字符串模板；标签是指一个函数，一个专门处理模板字符串的函数
* 函数调用时不用写()，直接在函数名后面使用模板字符串即可
* 标签函数的第一个参数是一个以变量为分隔的数组
* 第二个及以后的参数代表出传递的变量
```javascript
var name = "zhangsan";
var age = 18;
tagFun`我是${name},今年${age}岁了`;
function tagFun(arr,n,a){
	console.log(arr);//["我是", ",今年", "岁了"]
	console.log(n);//zhangsan
	console.log(a);//18
}
```
## 函数扩展
* repeat(n) : 将目标字符串重复n次，返回一个新的字符串，不影响原始字符串
* includes() : 判断字符串中是否含有指定字符串，第二个参数可选，表示开始搜索的位置
* startsWith() : 判断指定字符串是否出现在目标字符串的开始位置。 第二个参数可选，表示开始搜索的位置
* endsWith() : 判断指定字符串是否出现在目标字符串的末尾位置。 第二个参数可选，表示针对前N个字符
* codePrintAt() : 用于处理四个字节的字符，返回该字符码点的十进制数
* String.fromCodePrint() : 根据码点的十进制数找到对应的字符
* String.raw() : 返回字符串的原始的样子，不识别任何转义字符。常用作模板字符串处理函数
```javascript
var str = "my name is Tom";
var newStr = str.repeat(3);
console.log(newStr);//my name is Tommy name is Tommy name is Tom

console.log(str.includes("n"));//true
console.log(str.includes("n",5));//false

console.log(str.startsWith("n",3));//true
console.log(str.endsWith("m"));//true

console.log(String.raw`hello\nword`); //hello\nword

```
