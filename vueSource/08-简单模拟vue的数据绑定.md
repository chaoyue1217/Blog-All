---
title:  简单模拟vue的数据绑定
date: 2019-04-01
categories: vue-SourceCode
---

vue中使用了双向数据绑定的方式，简单来说双向绑定其实就是在单项绑定的基础上给可输入元素添加了监测事件(change，input等)。

vue中双向绑定的原理是采用发布者-订阅者模式，通过`Object.defineProperty()`来劫持各个属性的`setter`，`getter`，在数据变动时发布消息给订阅者，触发相应的监听回调。

下面，简单模拟一下vue的响应原理：
在不考虑模板解析的情况下，思路大致如下：
1、发布者observe：能够拦截数据的改变，并在数据改变时拿到最新的值通知订阅者
2、订阅者watcher：能够订阅并收到每个数据变化的通知，从而执行相应的回调函数并更新视图
3、依赖收集器Dep：收集每个数据对应的依赖，即数据变化时要进行的操作，此处为watcher实例

- 首先实现一个观察函数，能够监测数据的改变

```js
// observe函数监测数据
function observe(data){
  for(var key in data){
  	defineReactive(data,key, data[key])
  }
}
			
function defineReactive(data, key, value){
	// dep对象用来收集依赖
	var dep = new Dep();
	// 劫持数据的getter和setter
	Object.defineProperty(data, key,{
		get: function(){
			// 在getter中收集依赖
			Dep.target&&dep.addSub(Dep.target);
            return value;
		},
		set: function(newVal){
            console.log("变了");
            if(newVal===value) return;
            // 当数据变化时，触发依赖
            dep.notify();
            value = newVal;
    	}
   	})
}
```

- 依赖收集

```js
function Dep(){
	// subs是依赖数组，数组中每一个元素都是一个watcher
	this.subs = [];
}
// 添加依赖
Dep.prototype.addSub = function(sub){
	this.subs.push(sub);
}
// 通知更新，触发watcher更新
Dep.prototype.notify = function(){
	this.subs.forEach(function(sub, index){
		sub.update();
	})
}
```

- watcher

```js
function Watcher(key, cb){
    this.key = key;
    this.cb = cb;
    // 此处是为了触发getter，从而能够收集依赖
    this.value = this.get(key);
}
Watcher.prototype={
	get: function(key){
      	Dep.target = this; // 将target指向自身
      	var value = data[this.key]; // 触发getter
      	Dep.target = null; // 收集完依赖，将target置空
      	return value;
	},
	update: function(){ // 更新方法
		this.run();
	},
	run: function(){
		var value = this.get(); // 获取最新值
		var oldValue = this.value;
		if(value !== oldValue){
			this.value = value;
			this.cb(); // 当值变化后，执行相应的回调函数
		}
	}
}
```

测试：
```js
var data = {
	name: 123,
    age: 234,
}
observe(data);
new Watcher("name", function(){
	console.log("我是订阅者1");
});
new Watcher("name", function(){
	console.log("我是订阅者2");
});
data.name =234;
//当name属性改变时，会打印出   我是订阅者1   我是订阅者2
```