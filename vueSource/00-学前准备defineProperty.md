---
title: 00-学前准备之defineProperty
date: 2018-03-14
categories: vue-SourceCode
---

 vue的双向数据绑定的原理是使用defineProperty实现的，因此有必要在学习源码之前先弄懂defineProperty。

# 属性的特性
每个js对象都有属性和方法，来描述对象的特征和行为，而每个属性也有自己的特性，来限定这个属性是否能被修改，是否能被遍历等特征。由于这些特性是内部值，是提供给js引擎用的，所以在js中不能直接进行访问。
在ECMAScript中有两种属性：数据属性和访问器属性。

# 数据属性
数据属性有四个特征：
`[[Configurable]]`: 布尔值，表示该属性能否进行配置，包括其他特性是否可修改，能否使用delete关键字删除属性，能否修改数据属性为访问器属性等。
`[[Enumberable]]`: 布尔值，表示能否通过for-in循环获取到该属性。
`[[Writable]]`: 布尔值，表示能否修改该属性的值。
`[[Value]]`: 表示当前属性的值。可以看成一个位置，属性的读和写都在这个位置进行。默认undefined。

**------>>>进入重点  **
怎么修改属性的特性呢? 
答案：使用Object.defineProperty()方法。

## defineProperty
 参数：1、属性所在的对象  2、属性的名称  3、描述符对象，即由属性的特性组成的对象

**先看两句话，有助于理解下面的小栗子**
1、在调用`Object.defineProperty()`方法时，如果不指定`configurable`、`enumerable`、`writable`的值，默认均为false。
2、在不使用`Object.defineProperty()`定义属性时(属性可以是通过字面量定义，也可以是使用`对象.属性`定义等)，`configurable`、`enumerable`、`writable`的值默认为true。

### writable

```js
 // writable
var person = {};
Object.defineProperty(person,"name",{
    writable: false,
    value: "张三"
    });
console.log(person.name);//张三
person.name = "李四";
console.log(person.name);//张三
/*由于设置了name属性不可修改，所以person.name="李四"这句代码执行无效。*/

```

### enumerable

enumerable设置为false之后，for-in循环不会获取到该属性。

```javascript
var person = {
    name:"小李",
    gender:"男",
    age:22,
}
Object.defineProperty(person,"age",{
    enumerable: false,
    })
for(var key in person){
    console.log(key);// name gender
}
```

### configurable

configurable的情况比较复杂，可通过调试去进行比较：

- 当`configurable:false`时,使用delete删除该属性无效
```javascript
var person = {};
Object.defineProperty(person,"name",{
    value: "张三",
    configurable: false
    });
delete person.name; //该句执行无效
console.log(person.name)//张三
```
- 当属性通过defineProperty定义时：
  若`configurable:false`时，再次调用defineProperty定义该属性的特性时，【修改】除writable之外的特性，都会导致报错。
  **但是：**
  **writable特性由true-->false，不会报错，由false-->true会报错。**

由此：一旦confirgurable被设置为false后，将终身为false，不能再改回true(ps:真是一个悲惨的故事)。

举两个小栗子：

```javascript
var person = {};
Object.defineProperty(person,"name",{
    configurable: false,
    value: "张三"
    });
console.log(person.name);//张三
Object.defineProperty(person,"name",{
    writable: true  
    /*此处会报错，因为上一次定义name时，writable的值已经设置为了false,
    由false-->true会报错*/
    });
```

```javascript
var person = {};
Object.defineProperty(person,"name",{
    configurable: false,
    writable: true,
    value: "张三"
});
console.log(person.name);//张三
Object.defineProperty(person,"name",{
    value: "小五",
    /*至此可正常运行，因为上一次设置中writable是true，表示value是可以进行修改的。
    */
   writable: false
   //由true-->false，不会报错
});
person.name = "李四";
console.log(person.name);//小五
```

- 当对象定义时，直接指定了属性
```js
//通过该方式设置的属性，其configurable,enumerable,writable的值均为true
var person = {name:"小五"}; 
Object.defineProperty(person,"name",{
    configurable: false,
});
console.log(person.name);//张三
person.name = "李四";
console.log(person.name);//李四
```

### value
value值的设置完全由writable决定。

# 访问器属性

访问器属性不包含数据值，有一对函数：getter和setter。在读取属性时，实际上调用的是getter函数，写入属性时调用的是setter函数。

特性：
`[[Configurable]]`:布尔值，表示该属性能否进行配置，包括其他特性是否可修改，能否使用delete关键字删除属性，能否修改该属性为数据属性等。
`[[Enumerable]]`: 布尔值，表示能否通过for-in循环获取到该属性。
`[[Get]]`: 读取属性时调用的函数。默认为undefined。
`[[Set]]`: 写入属性时调用的函数。默认为undefined。

**访问器属性不能直接定义，必须使用defineProperty来定义**

下面来看看访问器属性的使用：
```javascript
var book = {
    _year: 2017,
    edition: 2
};
//定义一个访问器属性year
Object.defineProperty(book,"year",{
    get:function(){
        return this._year;
    },
    set:function(newValue){
        if(newValue>2017){
            this._year = newValue;
            this.edition += newValue -2017;
        }
    }
});

book.year = 2018;
console.log(book._year);   //2018
console.log(book.edition);   //3
```

_year前面的下划线是一种常用的记号，表示只能通过对象方法访问的属性。
从上面的栗子中可以看出，访问器属性year的改变，影响了其他数据属性的改变。书上说，这是使用访问器属性的常见方式，即设置一个属性会导致其他属性发生变化。

注意：getter和setter不是必须的，当只有getter时，该属性只能读不能写，当只有setter时，该属性只能写不能读。

- 数据属性和访问器属性的转化

```javascript
var person = {
    //此时的curyear是数据属性
    curyear: 2016,
    year: 1994,
    age: 22
};

//若此处解开注释，则表示curyear属性不能被修改为访问器属性，下面的defineProperty会报错
/*Object.defineProperty(person,"curyear",{
    configurable: false
})*/
//使用defineProperty为curyear加上getter和setter方法后，curyear变为访问器属性
Object.defineProperty(person,"curyear",{
    get: function(){
        return new Date().getFullYear();
    },
    set:function(newValue){
        this.age = newValue - this.year;
    },
    //value: 2017    //加上value特性会报错，因为此时为访问器属性，没有value特性
})
console.log(person.curyear); //2018
person.curyear = 2019;
console.log(person.age); //25
```


# 总结

## 对比数据属性和访问器属性
1、主要区别：数据属性有value值，没有getter和setter；访问器属性有getter和setter函数，没有value值。
2、访问器属性只能通过`Object.defineProperty()`定义
3、在configurable为true的情况下，数据属性和访问器属性可以相互转换。
4、访问器属性的改变会影响其他属性的改变。

**补充**
- 定义多个属性： `Object.defineProperties()`
  参数：1、对象  2、属性对象

```javascript
var person = {};
//同时定义数据属性和访问器属性
Object.defineProperties(person,{
    name:{
        value: "小李"
    },
    age:{
        value: 24,
    },
    birthday:{
        value: 1994
    },
    year:{
        get:function(){
            return new Date().getFullYear();
        },
        set:function(value){
            this.age = value - this.birthday;
        }
    }
})
```

- 读取属性的特性：`Object.getOwnPropertyDescriptor()`
  参数：1、对象    2、属性名
  返回值：描述符对象

 ```javascript
var person = {};
//同时定义数据属性和访问器属性
Object.defineProperties(person,{
    name:{
        value: "小李"
    },
    age:{
        value: 24,
    },
    birthday:{
        value: 1994
    },
    year:{
        get:function(){
            return new Date().getFullYear();
        },
        set:function(value){
            this.age = value - this.birthday;
        }
    }
})

var descriptor = Object.getOwnPropertyDescriptor(person,"name");
console.log(descriptor.configurable);  //false

var descriptor = Object.getOwnPropertyDescriptor(person,"year");
console.log(typeof descriptor.get);  //function
 ```