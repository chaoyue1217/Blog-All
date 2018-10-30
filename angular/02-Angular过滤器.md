---
title :  AngularJS 过滤器
date : 2016-09-06
categories: AngularJS
---

# AngularJS过滤器
* 在AngularJs中使用过滤器格式化展示数据。
## 内置过滤器
* 1、currency ： 将数据格式化为货币格式。默认是`$`,可设置参数为￥
* 2、date ：日期格式化。设置为符合中国的日期，参数为“yyyy-MM-dd hh:mm:ss”
* 3、lowercase ：将文本格式化为小写
* 4、uppercase ：将文本格式化为大写
* 5、number ：数字格式化，可通过参数控制小数位数
* 6、json ：将javascript对象转化为json对象
* 7、orderBy ：对数组进行排序。第一个参数是排序条件，通常用于对对象进行排序。第二个参数是布尔值，可控制正序（false，默认）和倒序（true）
* 8、limitTo ：截取字符串或数组的前几位（正数）或后几位（负数）
* 9、filter ：截取数组中满足条件的子集，并返回。条件可以是字符串，对象或函数
```html
<body ng-app="app">
<dl  ng-controller="demoCtrl">
    <dt>过滤器</dt>
    <!-- 在`{{}}`中使用"|"调用过滤器，使用" : "传递参数 -->
    <dd>价格：{{price|currency:'¥':3}}</dd>
    <dd>时间：{{now|date:'yyyy-MM-dd hh:mm:ss'}}</dd>
    <dd>大写：{{lowstr|uppercase}}</dd>
    <dd>小写：{{upstr|lowercase}}</dd>
    <dd>数字：{{num|number:2}}</dd>
    <dd>截取数组：{{list|limitTo:-2}}</dd>
    <!--可以截取汉字-->
    <dd>截取字符串：{{text|limitTo:2}}</dd>
    <!--默认false ：正序 -->
    <dd>排序1：{{list|orderBy:'':true}}</dd>
    <dd>排序2：{{arr|orderBy:'age':false}}</dd>
    <dd>对象转json：{{obj|json}}</dd>
    <!--查找数组中满足条件的元素，返回一个新数组-->
    <dd>指定条件进行数组筛选：{{list|filter:'s'}}</dd>
</dl>
<script>
    var app = angular.module("app",[]);
    app.controller("demoCtrl",["$scope",function ($scope) {
        $scope.price = 12.345;
        $scope.now = new Date();
        $scope.lowstr = "my name is Rose";
        $scope.upstr = "WHAt ARe YOU NOng sha LEi";
        $scope.num = 12.237;
        $scope.list = ["html","css","js","php"];
        $scope.text = "你是谁";
        $scope.arr = [
            {name:"小红",age:18,score:89},
            {name:"小橙",age:16,score:97},
            {name:"小黄",age:19,score:93},
            {name:"小绿",age:12,score:99},
            {name:"小青",age:20,score:100},
        ];
        $scope.obj = {
            name:"雍正",
            age:18
        };
    }]);
</script>
</body>
```
## 自定义过滤器
* 使用filter方法。该方法有两个参数，第一个是过滤器名称，第二个是回调函数或数组
* 回调函数需要返回一个函数，返回的函数需要有一个返回值，为格式化后的数据。“|”左边的变量作为第一个参数，“:”后面的参数作为第二个及以后的参数。
```html
<body ng-app="app">
<div ng-controller="demoCtrl">
    <h1>{{name|test:"arg1":"arg2"}}</h1>
    <p>{{info|capitalize}}</p>
</div>
<script>
    var app = angular.module("app",[]);
    app.controller("demoCtrl",["$scope",function ($scope) {
        $scope.name = "小明";
        $scope.info = "my name is zhangsan";
    }]);
    //自定义过滤器
    //需要两个参数
    // 1、名称
    // 2、回调函数或者数组
  	app.filter("test",function () {
        return function (arg,arg1,arg2) {
            console.log(arg);//小明
            console.log(arg1);//arg1
            console.log(arg2);//arg2
            return '<a href="">'+arg+'</a>';
        };
    });
    //设置首字母大写
    app.filter("capitalize",function () {
        return function (input) {
           return input[0].toUpperCase() + input.slice(1);
        };
    });
</script>
</body>
```