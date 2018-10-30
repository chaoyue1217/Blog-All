---
title: what  are  Set and WeakSet ？
date: 2017-01-06
categories: ES6
---

# Set和WeakSet

## Set
* Set类似于数组，但是成员的值都是唯一的，没有重复的值
* Set本身是一个构造函数，用来生成Set数据结构。可以接收一个数组或类数组的对象，用来初始化Set
* **Set中识别数据类型，即5和“5”不同，但是NaN只能存在一个，{}与{}不同**
```javascript
var set = new Set([1,2,2,3,4]);
console.log(set);//[1,2,3,4]
```
### 实例属性
* constructor : 指向构造函数
* size : 长度
### 实例方法
* add : 向Set中添加数据，返回Set
* delete : 删除某个值，返回一个布尔值，表示删除是否成功
* has ： 返回布尔值，表示某个值是否存在
* clear : 清除所有成员，没有返回值
```javascript
var set = new Set();
set.add(1).add(2).add(2);
set.size;//2

set.delete(2);//true
set.delete(2);//false
set.has(1);//true
```
** Array.from()可将Set转化成真正的数组**
### 遍历操作
** Set的遍历顺序就是插入顺序**
* keys() : 返回键名的遍历器
* values : 返回键值的遍历器
* __由于Set结构没有键名，所以键名和键值是一个__
* entries : 返回键值对的遍历器  即【索引+键值】
* forEach()  : 使用回调函数遍历每个成员，没有返回值
## WeakSet
* WeakSet结构与Set类似，也是不重复的值的集合。但是，它与Set有两个区别
* 1、WeakSet的成员只能是对象(具有iterable接口的对象)，而不能是其他类型的值
* 2、WeakSet中的对象都是弱引用，即垃圾回收机制不考虑WeakSet对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于WeakSet之中。这个特点意味着，无法引用WeakSet的成员，因此WeakSet是不可遍历的
* WeakSet是一个构造函数，可以使用new命令，创建WeakSet数据结构。
```js
var ws = new WeakSet();
```
* 作为构造函数，WeakSet可以接受一个数组或类似数组的对象作为参数（实际上，任何具有iterable接口的对象，都可以作为WeakSet的参数）。该数组的所有成员，都会自动成为WeakSet实例对象的成员。
```js
var a = [[1,2],[3,4]];
var ws = new WeakSet(a); 
```
* a是一个数组，有两个成员，且两个成员都是对象。a数组的成员是WeakSet的成员，而不是a数组本身
```js
var b = [1,2];
var ws = new WeakSet(b);//报错
```
* 因为b数组成员不是对象，加入WeakSet就会报错
* WeakSet结构有以下三个方法。
    * WeakSet.prototype.add(value)：向WeakSet实例添加一个新成员。
    * WeakSet.prototype.delete(value)：清除WeakSet实例的指定成员。
    * WeakSet.prototype.has(value)：返回一个布尔值，表示某个值是否在WeakSet实例之中。
* WeakSet 没有size属性，因为它不可遍历。
* WeakSet不能遍历，是因为成员都是弱引用，随时可能消失，遍历机制无法保证成员的存在，很可能刚刚遍历结束，成员就取不到了。WeakSet的一个用处，是储存DOM节点，而不用担心这些节点从文档移除时，会引发内存泄漏。
