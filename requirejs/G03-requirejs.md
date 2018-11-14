---
title: requireJS
date: 2016-05-12
categories: AMD和CMD
---
# requireJs
**AMD（  Asynchronous Module Definition）异步加载模块定义**
* 当一个网页要引入多个外部文件，且文件之间的依赖关系较强的时候，必须保证文件的加载顺序，并且当浏览器加载外部文件的时候就会停止网页的渲染，加载的文件越多，网页失去响应的时间越长，代码的编写和维护也会很麻烦。
* requirejs实现了js 文件的异步加载，避免网页失去响应
* 管理模块之间的依赖性，便于代码的编写和维护
* 解决了命名冲突问题（不同模块中的同名变量不会有冲突）
## requirejs的基本使用
**注意 : **
* 必须注意各模块之间的路径问题
### 主模块
* 如果主模块不依赖于任何模块，其中的js代码可以正常编写​
```html
<!-- data-main的作用是指定网页程序的主模板，即main.js。这个文件会第一个被require.js加载。main是省略了后缀名js -->
<script src="require.js" data-main="js/main"></script>
```
```javascript
//main.js
console.log("abc");
```
* 当主模块依赖于其他模块时，需要使用require函数
   * 第一个参数["module1","module2","module3"]代表的是该模块所依赖的三个模块，第二个参数是回调函数，当前面的模块加载完毕后，会调用该函数，加载的模块会以参数的形式依次传入该回调函数，就可以在回调函数内使用这些模块
   * 可以直接使用require(["module"]),表示在当前文件中加载模块
```javascript
require(["module1","module2","module3"],function(m1,m2,m3){
  	console.log(m1);
  	console.log(m2);
  	console.log(m3);
});
```
### 自定义模块加载 require.config()
* baseUrl: 显示的设置模块查找的根路径，即paths中设置的文件路径从public/js/开始
  * 如果不设置baseUrl时，根路径默认为data-main属性设置的入口文件所在的路径
* paths: 指定各个模块的加载路径，并为其路径设置一个名字 
```javascript
require.config({
  baseUrl:"public/js",
  paths:{
    jquery:"jquery/jquery.min",
    echarts:"echarts/echarts.min"
  }
});
```
### 定义模块
* 使用define();
```javascript
//这是一个js文件：sum.js
define(function(){
  function sum(a,b){
    return a+b;
  } 
  return {
    sum:sum
  }
});
```
> main函数加载时用法
```javascript
//main.js
require(["sum"],function(obj){
  var sum = obj.sum(1,2);
  console.log(sum);//3
});
```
### 非规范的模块处理
* 使用require.config()设置
```javascript
//  js/foo.js
//非规范的模块,即没有使用define定义模块
var n = 3;
function fn(n){
	console.log(n);  
}
```
> 处理方式
```javascript
//  main.js
require.config({
  shim:{
  
  	// 找到模块
    "js/foo":{
    
    	// 设置该模块所依赖的模块
    	deps:["jquery","common"],
    	//导出非规范模块中的成员(无论是变量还是函数)，此处是定义的fn函数
      	exports:"fn"
    }
  }
});
```
# CMD
**Sea.js 中，所有 JavaScript 模块都遵循 CMD（Common Module Definition） 模块定义规范。** 	
* 在CMD规范中，一个模块就是一个文件
## 基本使用
* denfine() ： 定义模块，是一个全局函数  define(factory)
* 接收factory参数，可以是函数，对象，也可以是字符串。
* define()也可以接收三个参数。define(id,deps,factory)
  * id : 模块名称
  * deps : 模块依赖
  * factory : 模块的构造方法，处理模块代码 
* 当factory为函数时，接收三个参数
  * require: 是一个函数，接收一个参数，为模块名称，用来获取其他模块提供的接口
    * 若要异步加载模块 : require.async(id, [callback:可选])
  * exports : 是一个对象，用来提供对外的模块接口。
  * module :　是一个对象，存储着与当前模块相关的一些属性和方法
    * module.url: 根据模块系统的路径解析规则得到的模块绝对路径。 
    * 一般情况下（没有在 define 中写 id 参数时），module.id 的值就是 module.uri，两者完全相同。
    * module.dependencies : 是一个数组，表示当前模块的依赖
    * module.exports : exports 是对该对象的一个引用。如果module.exports的指向改变了，那么exports就不在等于module.exports
```javascript
define(function(require, exports, module) {
  // 模块代码
  require("./model");
  exports.foo = function(){
  }
  console.log(module.exports==exports);//true
  module.exports = new Person();
  console.log(module.exports==exports);//false
});
```