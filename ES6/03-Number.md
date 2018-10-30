---
title: number
date: 2016-12-25
categories: ES6
---
# 数值的扩展

## Number.isFinite(), Number.isNaN() 
- ES6在Number对象上，新提供了Number.isFinite()和Number.isNaN()两个方法。
- Number.isFinite()用来检查一个数值是否为有限的（finite）。
```js
Number.isFinite(15); // true
Number.isFinite(NaN); // false
Number.isFinite(Infinity); // false
Number.isFinite('foo'); // false
Number.isFinite(true); // false
```

- ES5可以通过下面的代码，部署Number.isFinite方法。
```js
(function (global) {
  var global_isFinite = global.isFinite;

  Object.defineProperty(Number, 'isFinite', {
    value: function isFinite(value) {
      return typeof value === 'number' && global_isFinite(value);
    },
    configurable: true,
    enumerable: false,
    writable: true
  });
})(this);
```

- Number.isNaN()用来检查一个值是否为NaN。
```js
Number.isNaN(NaN) // true
Number.isNaN(15) // false
Number.isNaN('15') // false
Number.isNaN(true) // false
Number.isNaN(9/NaN) // true
Number.isNaN('true'/0) // true
Number.isNaN('true'/'true') // true
```

- 它们与传统的全局方法isFinite()和isNaN()的区别在于，传统方法先调用Number()将非数值的值转为数值，再进行判断，而这两个新方法只对数值有效，Number.isFinite()对于非数值一律返回false, Number.isNaN()只有对于NaN才返回true，非NaN一律返回false。

## Number.parseInt(), Number.parseFloat()
- ES6将全局方法parseInt()和parseFloat()，移植到Number对象上面，行为完全保持不变。
- 目的：减少全局性的方法，使语言逐步模块化
```js
Number.parseInt === parseInt // true
Number.parseFloat === parseFloat // true
```
## Number.isInteger()
- Number.isInteger()用来判断一个值是否为整数。需要注意的是，在JavaScript内部，整数和浮点数是同样的储存方法，所以3和3.0被视为同一个值。
```js
Number.isInteger(25) // true
Number.isInteger(25.0) // true
Number.isInteger(25.1) // false
Number.isInteger("15") // false
Number.isInteger(true) // false
```
## Number.EPSILON
- 代表极小的常量Number.EPSILON。
```js
Number.EPSILON
// 2.220446049250313e-16
Number.EPSILON.toFixed(20)
// '0.00000000000000022204'
```

- 引入一个这么小的量的目的，在于为浮点数计算，设置一个误差范围。我们知道浮点数计算是不精确的。但是如果这个误差能够小于Number.EPSILON，我们就可以认为得到了正确结果。因此，Number.EPSILON的实质是一个可以接受的误差范围。
## 安全整数和Number.isSafeInteger() 
- JavaScript能够准确表示的整数范围在-2^53到2^53之间（不含两个端点），超过这个范围，无法精确表示这个值。
- ES6还引入了Number.MAX_SAFE_INTEGER和Number.MIN_SAFE_INTEGER这两个常量，用来表示这个范围的上下限。
- Number.isSafeInteger()则是用来判断一个整数是否落在这个范围之内。
```js
Number.isSafeInteger('a') // false
Number.isSafeInteger(null) // false
Number.isSafeInteger(NaN) // false
Number.isSafeInteger(Infinity) // false
Number.isSafeInteger(-Infinity) // false

Number.isSafeInteger(3) // true
Number.isSafeInteger(1.2) // false
Number.isSafeInteger(9007199254740990) // true
Number.isSafeInteger(9007199254740992) // false

Number.isSafeInteger(Number.MIN_SAFE_INTEGER - 1) // false
Number.isSafeInteger(Number.MIN_SAFE_INTEGER) // true
Number.isSafeInteger(Number.MAX_SAFE_INTEGER) // true
Number.isSafeInteger(Number.MAX_SAFE_INTEGER + 1) // false
```

- 实现
```js
Number.isSafeInteger = function (n) {
  return (typeof n === 'number' &&
    Math.round(n) === n &&
    Number.MIN_SAFE_INTEGER <= n &&
    n <= Number.MAX_SAFE_INTEGER);
}
```