---
title: Promise原理浅析
date: 2019-01-06
categories: ES6
---

# Promise

从一个例子开始：

```js
var promise = new Promise(function(resolve, reject){
  resolve("成功了!");
});
promise.then(function(value){
  console.log(value);
}, function(reason){
  console.log(reason);
});
```
很明显，上面的代码会打印出来` 成功了！`。那，具体实现就要慢慢来看了。

## 构造函数
```js
// 首先在构造函数的上面有一些工具和变量，暂时可以不用管
var asap = require('asap/raw');
function noop() {}
var LAST_ERROR = null;
var IS_ERROR = {};
// 该函数用于获取对象的then属性
function getThen(obj) {
  try {
    return obj.then;
  } catch (ex) {
    LAST_ERROR = ex;
    return IS_ERROR;
  }
}
// ... 省略了几个函数，为了方便理解代码，会在合适的地方出现

function Promise(fn) {
	// 当前实例必须为对象
  if (typeof this !== 'object') {
    throw new TypeError('Promises must be constructed via new');
  }
  // 传入的参数必须为函数
  if (typeof fn !== 'function') {
    throw new TypeError('Promise constructor\'s argument is not a function');
  }
  this._deferredState = 0; // 记录延迟对象的状态，即个数
  this._state = 0; // promise的状态， 有0 1 2 3四种
  this._value = null; // 完成或拒绝函数的值
  this._deferreds = null; // 延迟对象
  if (fn === noop) return; //noop对象已在上面定义
  doResolve(fn, this);
}
// 定义一些静态属性
Promise._onHandle = null;
Promise._onReject = null;
Promise._noop = noop;
```

从`Promise`实例化的过程中可以看到，最后主要的任务是`doResolve`方法完成的。

##  具体实现过程

**doResolve**
```js
// 该函数位于构造函数的上面，为了方面读代码，挪至此处
function tryCallTwo(fn, a, b) {
  try {
    fn(a, b);
  } catch (ex) {
    LAST_ERROR = ex;
    return IS_ERROR;
  }
}
/* 该方法接收两个参数，
 * 一个是Promise构造函数的值，也就是一个函数。
 * 一个是当前实例对象
 * */
function doResolve(fn, promise) {
// done是一个标志，为了确保onFulfilled和onRejected只执行一次
  var done = false;
  var res = tryCallTwo(fn, function (value) {
    if (done) return;
    done = true;
    resolve(promise, value);
  }, function (reason) {
    if (done) return;
    done = true;
    reject(promise, reason);
  });
  if (!done && res === IS_ERROR) {
    done = true;
    reject(promise, LAST_ERROR);
  }
}
```
纵观全局`doResolve`方法，目的就是执行`reslove`或`reject`方法。先看看`tryCallTwo`干了什么，只有一件事：执行`fn(a,b)`, `fn`即构造函数的参数：
```js
// fn的两个参数a和b就是resolve和reject
function(resolve, reject){
  resolve("成功了!");
}
```
由于例子中执行了`resolve`，即执行`fn`中的`a`函数，也就是`tryCallTwo`方法的第二个参数。
```js
// 相当于
function(a, b){
  a("成功了!");
}
function (value) { // value是 "成功了!"
    if (done) return;
    done = true;
    // resolve方法接收两个参数： 当前实例对象，和成功函数的参数
    resolve(promise, value);
}
```
接下来看`resolve`函数做了什么。
一开始有个判断：
```js
function resolve(self, newValue){
  if (newValue === self) {
    return reject(
      self,
      new TypeError('A promise cannot be resolved with itself.')
    );
  }
  //...
}
```
当`newValue`为当前实例对象时，会调用`reject`并抛出一个错误，下面模拟一下符合该条件的`promise`代码
```js
var res = new Promise(function(resolve,reject){
	setTimeout(function(){
		resolve(res);
	}, 10)
});
res.then(function(value){
	console.log(value)
}, function(reason){
	console.log(reason); //TypeError: A promise cannot be resolved with itself.
})
```
由此我们可以知道，**`resolve`完成函数不能传入自身实例对象**。

再往下也是一个判断：
```js
// 当newValue存在，并且是一个对象或函数时
if (newValue 
	&&(typeof newValue === 'object' || typeof newValue === 'function')
	) {
	// 判断newValue是否有then属性
    var then = getThen(newValue);
    // 没有then属性，则reject
    if (then === IS_ERROR) {
      return reject(self, LAST_ERROR);
    }
    if (
      then === self.then &&
      newValue instanceof Promise
    ) {
    // newValue是Promise的实例
      self._state = 3; // 设置状态值为3
      self._value = newValue;
      finale(self);
      return;
    } else if (typeof then === 'function') {
    	// newValue存在，但不是promise的实例
      doResolve(then.bind(newValue), self);
      return;
    }
  }
```
从例子中可以看出,并不符合以上条件,因此继续执行:
```js
self._state = 1; // 设置状态为1
self._value = newValue; // 设置value
finale(self);
```
`finale`方法：
```js
function finale(self) {
 // 以下两个条件均不符合，没有做任何处理
  if (self._deferredState === 1) {
    handle(self, self._deferreds);
    self._deferreds = null;
  }
  if (self._deferredState === 2) {
    for (var i = 0; i < self._deferreds.length; i++) {
      handle(self, self._deferreds[i]);
    }
    self._deferreds = null;
  }
}
```
至此，`resolve`方法执行完毕。`doResolve`还剩最后一段：
```js
// 此时done为true
if (!done && res === IS_ERROR) {
    done = true;
    reject(promise, LAST_ERROR);
}
```
`doResolve`结束，即`new Promise()`执行完成。
再回顾一下例子:
```js
var promise = new Promise(...);
```
最后， `promise`为：
```js
{
  _state: 1,
  _value: "成功了！",
  _deferreds: null,
  _deferredState: 0
}
```

## 补充reject
上面提到了`reject`方法，其实就是处理拒绝的，代码很简单
```js
function reject(self, newValue) {
// 将状态值设置为2
  self._state = 2;
  // 设置value
  self._value = newValue;
  if (Promise._onReject) {
    Promise._onReject(self, newValue);
  }
  finale(self);
}
```

##  then

实例创建完成了，接下来就是`then`方法的执行了。
```js
promise.then(function(value){
  console.log(value);
}, function(reason){
})
```

在源码中，`then`方法位于`Promise`的原型上：
```js
Promise.prototype.then = function(onFulfilled, onRejected) {
// 判断实例的构造函数是否是Promise，当前例子中构造函数是Promise
  if (this.constructor !== Promise) {
    return safeThen(this, onFulfilled, onRejected);
  }
  // 创建一个新的promise，并做为返回值
  var res = new Promise(noop);
  handle(this, new Handler(onFulfilled, onRejected, res));
  return res;
};
// safeThen是为了在this指向改变后，依然能安全的执行then函数
function safeThen(self, onFulfilled, onRejected) {
  return new self.constructor(function (resolve, reject) {
    var res = new Promise(noop);
    res.then(resolve, reject);
    handle(self, new Handler(onFulfilled, onRejected, res));
  });
}
```

**handle与Handler**
首先看`Handler`:
```js
// 构造函数接收3个参数：成功的处理函数、失败的处理函数、一个promise实例
function Handler(onFulfilled, onRejected, promise){
  this.onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : null;
  this.onRejected = typeof onRejected === 'function' ? onRejected : null;
  this.promise = promise;
}
```

```js
// handle接收两个参数： then方法的调用对象，Handler实例
function handle(self, deferred) {
// 在例子中的Promise实例化之后，_state值为1
  while (self._state === 3) {
    self = self._value;
  }
   // 不会进入判断
  if (Promise._onHandle) {
    Promise._onHandle(self);
  }
  // 不会进入判断
  if (self._state === 0) {
    if (self._deferredState === 0) {
      self._deferredState = 1;
      self._deferreds = deferred;
      return;
    }
    if (self._deferredState === 1) {
      self._deferredState = 2;
      self._deferreds = [self._deferreds, deferred];
      return;
    }
    self._deferreds.push(deferred);
    return;
  }
  //由于上述条件都不符合，因此执行handleResolve方法
  handleResolved(self, deferred);
}
```
**handleResolve**
```js
function tryCallOne(fn, a) {
  try {
    return fn(a);
  } catch (ex) {
    LAST_ERROR = ex;
    return IS_ERROR;
  }
}
function handleResolved(self, deferred) {
// asap是一个异步执行参数的方法，可以简单的认为是 asap(fn){ fn() }
  asap(function() {
  	// _state为1时，执行onFulfilled，否则，执行onRejected
    var cb = self._state === 1 ? deferred.onFulfilled : deferred.onRejected;
    // cb为null时，也就是then方法接收的参数为null或者参数不是function类型时
    if (cb === null) {
      if (self._state === 1) {
        resolve(deferred.promise, self._value);
      } else {
        reject(deferred.promise, self._value);
      }
      return;
    }
    // 此栗子中，cb为function(value){console.log(value)}
    // 调用tryCallOne，即执行回调函数cb
    var ret = tryCallOne(cb, self._value);
    // 由于cb没有任何返回值，则ret为undefined
    if (ret === IS_ERROR) {
      reject(deferred.promise, LAST_ERROR);
    } else {
    // 进入else，执行resolve方法， resolve方法在上面已经讲过，主要是根据传入的value值来对promise进行状态设置的
    // 由于ret为undefined，因此deferred.promise为
    /*
    {
      _state: 1,
      _value: 0,
      _deferreds: null,
      _deferredState: 0,
    }
    */
    // deferred.promise与then中返回的res为同一引用
      resolve(deferred.promise, ret);
    }
  });
}
```
`then`方法最后返回了一个`_state`为1的`promise`实例。

**总结state**
`_state`有四种状态：
+ 0 ：初始（挂起）状态
+ 1 ：成功状态
+ 2 ：拒绝状态
+ 3 ：采纳了另一个promise

下面制造一个`_state`为3的`promise`。
在`resolve`方法中可以看到，由产生3的条件
```js
then === self.then && newValue instanceof Promise
```
表示，返回值必须是一个对象或函数，且有`then`方法，与`Promise`原型上的`then`为同一引用，且必须是一个`Promise`实例，因此代码如下：
```js
var res = new Promise(function(resolve,reject){
	resolve(123);
});
var ret = res.then(function(value){
	console.log(value)
	// 返回一个Promise实例
	return new Promise(function(f,r){
      f(456);
	})
}, function(reason){
	console.log(reason)
})
```
通过以上方式，得到的`ret`为：
```js
{
  _state: 3,
  _value: Promise{
    _state: 1,
    _value: 456,
    ...
  },
  ...
}
```