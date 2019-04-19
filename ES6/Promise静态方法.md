---
title: Promise静态方法
date: 2019-01-
---

我们知道`Promise`除了使用`new`，还可以直接像`Promise.resolve`这样调用。
`Promise.resolve`用来创建一个立即完成的`Promise`，例如
```js
let promise = Promise.resolve(42);
promise.then(function(value) {
	console.log(value); // 42
});
```
静态方法位于`es6-extensions.js`文件中，下面看一下具体实现。
```js
var TRUE = valuePromise(true);
var FALSE = valuePromise(false);
var NULL = valuePromise(null);
var UNDEFINED = valuePromise(undefined);
var ZERO = valuePromise(0);
var EMPTYSTRING = valuePromise('');
// 生成一个完成态的Promise
function valuePromise(value) {
  var p = new Promise(Promise._noop);
  p._state = 1;
  p._value = value;
  return p;
}
```

# resolve

```js
Promise.resolve = function (value) {
 // 如果传入的value是一个Promise，则直接返回
  if (value instanceof Promise) return value;
  // 下面是根据value的值，返回对应的完成态Promise
  if (value === null) return NULL;
  if (value === undefined) return UNDEFINED;
  if (value === true) return TRUE;
  if (value === false) return FALSE;
  if (value === 0) return ZERO;
  if (value === '') return EMPTYSTRING;
// 当value是对象或函数时
  if (typeof value === 'object' || typeof value === 'function') {
    try {
     // 获取then
      var then = value.then;
      // 当then是一个函数时，返回一个以then为参数的promise
      if (typeof then === 'function') {
        return new Promise(then.bind(value));
      }
    } catch (ex) {
      return new Promise(function (resolve, reject) {
        reject(ex);
      });
    }
  }
  // 其他情况返回对应value的完成态promise
  return valuePromise(value);
};
```

# reject
`reject`就很简单了，返回一个失败状态的`promise`
```js
Promise.reject = function (value) {
  return new Promise(function (resolve, reject) {
    reject(value);
  });
};
```

# all

`all`方法接收单个参数：可迭代对象，如数组， 返回一个`Promise`。这个可迭代对象的元素都是 Promise ，只有在它们都完成后，所返回的 Promise 才会被完成。例如：
```js
let p1 = new Promise(function(resolve, reject) {
	resolve(42);
});
let p2 = new Promise(function(resolve, reject) {
	resolve(43);
});
let p3 = new Promise(function(resolve, reject) {
	resolve(44);
});
let p4 = Promise.all([p1, p2, p3]);
p4.then(function(value) {
  console.log(Array.isArray(value)); // true
  console.log(value[0]); // 42
  console.log(value[1]); // 43
  console.log(value[2]); // 44
});
```

下面看具体代码：
```js
Promise.all = function (arr) {
 // Array.prototype.slice.call(arguments)能将具有length属性的对象转成数组
  var args = Array.prototype.slice.call(arr);
  return new Promise(function (resolve, reject) {
  // 如果长度为0，则直接表示该promise已完成
    if (args.length === 0) return resolve([]);
    var remaining = args.length;
    // 定义一个函数，处理传入的promise，并在所有的promise都为完成态时执行resolve
    // res参数：数组元素的索引，数组元素即promise
    function res(i, val) {
    // 如果val存在，并且是对象或函数
      if (val && (typeof val === 'object' || typeof val === 'function')){
      // val为promise时，根据状态进行相应的处理
        if (val instanceof Promise && val.then ===      							Promise.prototype.then) {
          while (val._state === 3) {
            val = val._value;
          }
          if (val._state === 1) return res(i, val._value);
          // 只要有一个是拒绝态的函数，则执行reject
          if (val._state === 2) reject(val._value);
         // 递归处理promise完成
          val.then(function (val) {
            res(i, val);
          }, reject);
          return;
        } else {
        // val不是Promise时
          var then = val.then;
          if (typeof then === 'function') {
            var p = new Promise(then.bind(val));
            p.then(function (val) {
              res(i, val);
            }, reject);
            return;
          }
        }
      }
      // 当val不存在或者不是对象或数组时,也就是每当一个promise完成时
      // 会将异步请求的结果存入args数组中
      args[i] = val;
      // 对数组计数，当所有的promise都处理完成后，返回完成函数
      if (--remaining === 0) {
        resolve(args);
      }
    }
    for (var i = 0; i < args.length; i++) {
      res(i, args[i]);
    }
  });
};
```

# race

```js
Promise.race = function (values) {
  return new Promise(function (resolve, reject) {
    values.forEach(function(value){
      Promise.resolve(value).then(resolve, reject);
    });
  });
};
```
`race`方法返回的是第一个被处理的promise。
在书中有一句话很形象：
> 传递给Promise.race() 的 Promise 在进行赛跑，看哪一个首先被解决。若胜出的 Promise 是被完成，则返回的新 Promise 也会被完成；而胜出的 Promise 若是被拒绝，则新 Promise 也会被拒绝。

例如：
```js
et p1 = new Promise(function(resolve, reject) {
	reject("error");
});
let p2 = new Promise(function(resolve, reject) {
	resolve(43);
});
let p3 = new Promise(function(resolve, reject) {
	resolve(44);
});
let p4 = Promise.race([p1, p2, p3]);
p4.then(function(value) {
console.log(value); 
}, function(reason){
  console.log(resaon);// "error"
});
```

**补充 catch**
```js
// catch根本上也是调用了then
Promise.prototype['catch'] = function (onRejected) {
  return this.then(null, onRejected);
};

```

















