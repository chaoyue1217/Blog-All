---
title: Class
date: 2017-01-09
categories: ES6
---
# 类
* ES6增加了类的概念，实际上也是依靠函数function和prototype实现
## 基本使用
* constructor方法是构造方法，其中的this指向实例，默认返回实例对象。这个方法是类必须的，如果没有写，js会自动添加一个空方法
* 一个类中只能有一个constructor方法
* 使用new创建对象的时候，实际上是调用的constructor方法
* **必须先声明类再创建实例**
```javascript
class Person{
	//contructor方法是构造方法
  	constructor(name){
  		//this指向实例对象
		this.name = name;
	}
	getName(){
      return this.name;
	}
}
```
## 静态方法
* 使用static关键字声明
* 可以直接使用类名调用
```javascript
class Person{
	//contructor方法是构造方法
  	constructor(name){
  		//this指向实例对象
		this.name = name;
	}	
	static getName(){
		//此处的this指的是整个Person函数
		console.log(this);
	}
} 
var per = new Person("zhangsan");
console.log(per.name);//zhansan
Person.getName();
```
## 类的继承
- 使用extends关键字实现继承
- **super的使用**
  - super()方法必须在constructor中调用
  - 在constructor中，必须先调用super方法才能使用this，否则会报错
  - 子类调用父类的方法时，使用super.父类方法()调用。
  - 子类的super是对父类的引用
  - super()方法只能在constructor方法中使用一次。出现两次及以上会报错
```javascript
class Person{
	constructor(name){
		this.name = name;
	}
	getName(){
		console.log(this.name);
	}
}
class Student extends Person{
	constructor(name,age){
		super(name);
		this.age = age;
	}
	getInfo(){
		super.getName();;
		console.log(this.age);
	}
}
var stu = new Student("lisi",17);
stu.getInfo();
```