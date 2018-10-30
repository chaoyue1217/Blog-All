---
title: Promise
date: 2016-12-30
categories: ES6
---
# Promise
- 用来解决回调函数层层嵌套（回调地狱）的形式存在的缺点。
* 使用方法： 创建实例对象
* Promise是全局对象，创建实例对象的时候需要参数，参数是一个匿名函数，匿名函数有两个参数：resolve和reject，两个参数都是方法。
* resolve用于解决异步操作成功后的业务，reject用于解决异步操作失败后的业务
##  Promise对象的三个状态
* pending ： 刚创建实例的时候，表示初始状态
* resolved ：resolve方法调用的时候，表示操作成功
* rejected ：reject方法调用的时候，表示操作失败
* **状态只能在初始-->成功；或者初始-->失败之间转化。不能逆向转化，成功和失败之间不能转化**
## 实例方法
### then()方法
* 第一个参数：用于处理操作成功后的业务
* 第二个参数：用于处理操作失败后的业务
### catch()方法
* 用于处理操作异常（失败）后的业务。参数是一个回调函数
```javascript
var promise = new Promise(function(resolve,reject){
	//此时是pending状态
	if(false){
		resolve("成功");
		//此时是resolved状态
	}else{
		reject("失败");
		//此时是rejected状态
	}
	
	//js会根据promise的状态选择执行哪个函数
}).then(function(res){//为resolved状态时执行
	//此处的参数是resolve的参数，传递给了处理操作成功业务的函数
	console.log(res);
}).catch(function(error){//为rejected状态时执行
	//此处的参数是reject函数的参数，传递给了处理操作失败业务的函数
	console.log(error);
});
```
**多级嵌套，并且有依赖的回调函数的处理**
```javascript
/*
* 假设foo2的执行依赖foo1，foo3的执行依赖foo2
*/
var promise = new Promise(function(resolve,reject){
	if(true){
		resolve("成功了");

	}else{
		reject("失败");
	}
});
//可以使用链式编程，因为每个函数内部都返回了当前实例对象
promise.then(foo1)
.then(foo2)
.then(foo3)
.catch(function(res){
	console.log(res);
});
function foo1(){
	console.log("foo1执行了");
	return "foo1";
}
function foo2(res){
	console.log("foo2执行了");
	//foo2接收到的参数是foo1函数的返回值，不必使用函数嵌套就很好的解决了依赖的问题
	console.log("foo1的返回值是:" + res);
	return "foo2";
}
function foo3(res){
	console.log("foo3执行了");
	//foo3接收到的参数是foo2的返回值
	console.log("foo2的返回值是:" + res);
}
```

__当then中返回的数据是promise对象时，下一个then中接收到的数据是promise对象中的resolved对象__

## 对象方法   
### Promise.all()
- 参数是一个数组，数组的元素是Promise的实例。当数组中的每个实例对象的状态都为resolved时，该函数才有返回。
- 如果一个操作需要依赖多个接口请求回来的数据的支持，并且每个接口之间互不依赖，可以使用该方法
- Promise.all和Promise.race()方法会返回一个Promise的实例，保存着promise的状态。所以可以使用实例方法then和catch
```javascript
var pro1 = new Promise(function(resolve,reject){
	setTimeout(function(){
		if(true){
			resolve("1成功了");
		}
	},3000);
	
});
var pro2 = new Promise(function(resolve,reject){
	if(true){
		resolve("2成功了");
	}
});
var pro3 = new Promise(function(resolve,reject){
	if(true){
		resolve("3成功了");
	}
});
//只有等到所有的实例的状态都为resolved后，才会执行成功的操作，即then中的第一个回调函数。函数的参数是resolve函数的参数构成的数组
Promise.all([pro1,pro2,pro3]).then(function(res){
	console.log(res);//3s后输出  ["1成功了", "2成功了", "3成功了"]
}).catch(function(error){
	//只要有一个失败就执行该操作，返回第一个失败的结果
  	console.log(error);
});
```
### Promise. race()
- 参数是由实例组成的数组，只要有一个实例发生改变就会触发该方法
```javascript
var pro1 = new Promise(function(resolve,reject){
	setTimeout(function(){
		if(true){
			resolve("1成功了");
		}
	},3000);
});

var pro2 = new Promise(function(resolve,reject){
	if(false){
		resolve("2成功了");
	}else{
		reject("2失败了");
	}
});
Promise.race([pro1,pro2]).then(function(res){
	console.log(res);
}).catch(function(error){
	console.log(error);//输出 2失败了
});
```