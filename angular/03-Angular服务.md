---
title :  AngularJS服务 
date : 2016-09-08
categories: AngularJS
---
# Angular 服务
* 服务就是一个对象或参数，对外提供特定的功能
* 服务提供了一种能在应用的整个生命周期内保持数据的方法，它能够在控制器之间进行通信，并且能保证数据的一致性。
* 服务是一个单例对象，在每个应用中只会被实例化一次（被`$injector`实例化），并且是延迟加载的（需要时才会被创建）。
## 内置服务
### 1、`$log`  是一个对象
```javascript
var app = angular.module("app", []).controller("demoCtrl",["$log",function ($log) {
		//不同的方法会以不同的样式在控制台输出
        $log.error("这是一个错误");
        $log.warn("这是一个警告");
        $log.debug("这是一个调试");
        $log.info("这是一个普通的信息");
        $log.log("这是console.log");
    }]);
```
### 2、`$timeout`和`$interval`：这两个服务是函数，使用方法类似于setTimeout和setInterval
```html
<body ng-app="app">
<div ng-controller="demoCtrl">
	<h1>{{name}}</h1>
    <button ng-click="show()">点我</button>
    <h2>当前时间为：{{now|date:"yyyy-MM-dd hh:mm:ss"}}</h2>
    <button ng-click="stop()">停</button>
</div>
<script>
    var app = angular.module("app", [])
    .controller("demoCtrl",["$scope","$timeout","$interval","$log",function ($scope,$timeout,$interval,$log) {
       //延时显示数据。可以使用$timeout服务可以实现
       //使用原生的setTimeout时，不能正确的进行数据绑定
       setTimeout(function () {
            //此处仅仅是改变了数据，并没有进行脏值检查
            //$timeout模块自动进行了脏值检查
            $scope.name = "小明";
            //需要调用$digest进行脏值检查.否则页面不会显示数据
			//$scope.$digest();
        },1000);
        //点击事件触发后会显示数据，因为Angular中的该事件自动进行了脏值检查
        $scope.show = function () {
            alert($scope.name);
        }
       //----------------------------------------
       var timer = $interval(function () {
            $scope.now = new Date();
        },0.5);
        $scope.stop = function () {
            //取消定时器
            $interval.cancel(timer);
        };
    }]);
</script>
</body>
```
### 3、`$http`  是一个函数
* 用于向服务端发起异步请求。
* 使用方法如下
```javascript
//该使用方法适用于1.5版本
var app = angular.module("app",[]);
app.controller("demoCtrl",["$http","$scope"],function ($http,$scope) {
    //$http是一个函数
    $http({
        url:"",//接口地址
        method:"",//请求方式
        data:"",//post方式的请求参数
        params:"",//get 方式的请求参数
        headers:{
        //设置请求头
        }
      }).success(function () {
      		//成功
      }).error(function () {
      		//失败
      });
});
```
## 自定义服务
* 使用service或者factory方法
- **factory方法**
```html
<body ng-app="app">
<div ng-controller="demoCtrl">
    <h1>{{now}}</h1>
</div>
<script>
    var app = angular.module("app", []);
    app.factory("time", ["$filter",function ($filter) {
        var date = $filter("date");
        return {
            now :date(new Date()),
            format:function (format) {
                return date(new Date,format);
            }
        };
    }]);
    app.controller("demoCtrl", ["time","$scope", function (time,$scope) {
        $scope.now = time.format("yyyy-MM-dd hh:mm:ss");
    }]);
</script>
</body>
```
*  **server方法**
*  factory 和 service的区别
     * factory 直接返回一个对象
     * service 中使用this  返回一个实例对象
```html
<body ng-app="app">
<div ng-controller="demoCtrl"></div>
<script>
    var app = angular.module("app",[]);
    // 参数
    //1 名称
    //2 数组 依赖
    app.service("sayHi",[function () {
        this.name = "小明";
        this.sayHello = function (name) {
            return "Nihao "+name;
        }
    }]);
    app.controller("demoCtrl",["$scope","sayHi",function ($scope,sayHi) {
        //此处返回的相当于一个实例对象
        console.log(sayHi);
    }]);
</script>
</body>
```
# 模块加载
- AngularJS在执行时是按着特定的顺序执行的，其中有两个环节（配置和运行）处在初始化之后，所以通过配置可以影响整个angularJS的执行结果
- 服务和模块只是不同的名字而已，实质上是一样的。就好比函数和方法
##  配置块
- 模块的配置使用config 方法。参数是一个数组
```html
<body ng-app="app">
<div ng-controller="demoCtrl">
	<!--My name is zhangsan-->
    <p>{{info|capitalize}}</p>
</div>
<script>
var app = angular.module("app",[]);
app.config(["$logProvider","$filterProvider",function ($logProvider,$filterProvide) {
        //通过$logProvider对$log这个服务进行配置。此处是设置debug方法不可用
        $logProvider.debugEnabled(false);
        
        //通过$filterProvide进行配置
        //通过register方法也可以实现自定义过滤器。此处是对模块内部进行配置
    $filterProvider.register("capitalize",function () {
        return function (input) {
        	return input[0].toUpperCase()+input.slice(1);
        }
    });
}]);
app.controller("demoCtrl",["$log","$scope",function ($log,$scope) {
	//$log.debug("这是一个debug");//不会产生输出
	$scope.info = "my name is zhangsan";
}]);
</script>
</body>
```
## 运行块
- 一个服务不仅可以被当做依赖使用，还可以直接运行。
- 使用run方法可以直接运行一个服务
- 参数：数组
```html
<body ng-app="app">
<div ng-controller="parentCtrl">
<!--页面上显示  顺治-->
    <p>{{name}}</p>
    <div ng-controller="childCtrl">
    <!--此处也是 顺治-->
    	<p>{{name}}</p>
    </div>
</div>
<script>
	var app = angular.module("app",[]);
    /*app.run(["$http","$log",function ($http,$log) {
        $http({
            url:"",
            method:""
        }).then(function (res) {

        });
    }]);*/
    app.run(["$rootScope",function ($rootScope) {
        //可以初始化全局的数据
        $rootScope.name = "顺治";
    }]);
    // $rootScope 是一个服务  根作用域
    app.controller("parentCtrl",["$scope",function ($scope) {
    }]);
    app.controller("childCtrl",["$scope",function ($scope) {
    }])
</script>
</body>
```