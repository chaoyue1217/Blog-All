---
title : AngularJS 基础使用
date : 2016-09-03
categories: AngularJS
---

# AngularJS
* AngluarJS是一款由谷歌公司开发的前端MVC框架(MVVM)
* AngularJS是一个以数据和逻辑为核心的框架(诸多类库的集合)
* 使用angular需要引入angular.js文件
## MVC
* MVC是一种软件开发架构方式，是一种非常好的代码架构组织方式（使用web开发），用来解耦视图和数据，利用控制器去调度视图和模型数据的交互。
* 使用MVC模式，使开发变得更高效，便于维护和管理
* 是一种开发模式，由模型（Model）、视图（View）、控制器（Controller）3部分构成
* 模型（model）: 用来处理数据，一般指操作数据库
* 视图（view）: 用来展示数据，前端是指通过html页面展示
* 控制器（controller）: 用来做链接模型和视图的桥梁
## AngularJS的基本结构
### 定义应用
* 在任意dom元素上使用属性ng-app就可以定义一个AngularJS的应用
* ng-app属性所在的DOM元素所包含的所有子元素都属于此应用的一部分
### 定义模块
* 引入了angular框架后，会拥有一个全局的对象 angular
* 在此对象下有若干方法，其中angular.module()可以实例化一个模块
* module()需要两个参数
  * 1、  模块名称
  * 2 、 依赖。是一个数组
### 定义控制器
* 上面步骤实例出来的模块也是一个对象，在此对象下也有若干方法，controller()方法，用来创建控制器的角色
* controller()需要两个参数
  * 1、名称
  * 2、依赖。【依赖的数组里最后一个单元必须是一个函数】
```html
<!--定义一个应用-->
<body ng-app="App">
<!--关联控制器-->
<div class="box" ng-controller="DemoCtrl">
	<!--此处相当于视图，用来展示数据-->
	<p>我叫{{name}},我今年{{age}}岁了,我的性别是{{gender}}</p>
</div>
</body>
<script>
	//定义模块
	var app = angular.module("App",[]);
	//定义控制器
	app.controller("DemoCtrl",["$scope",function($scope){
        //$scope就是我们需要的另外一个角色Model,专门处理数据
        //$scope就是一个空对象，类似{}，但是有其自己的专有属性
        $scope.name="张三";
        $scope.age=18;
        $scope.gender = "男";
    }]);
</script>
```
