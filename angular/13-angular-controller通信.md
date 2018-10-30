---
title: angular-controller之间通信
date: 2017-07-21
categroies: AngularJS
---

由于近来canvas图形界面的开发需求，controller之间的通信成了必不可少的一方面。

在Angular中，controller之间通信的方式主要有三种：
- 作用域继承。利用子controller控制父controller上的数据。(父controller中的数据要为引用类型，不能是基本类型。为的是保证数据的变化可以同时反映到不同的controller上)
- 注入服务。把需要共享的数据注册为一个service，在需要的controller中注入。
- 基于事件。利用angular的事件机制，使用`$on`、`$emit`和`$broadcast`。
其中，作用域继承仅限于上下级之间的通信，注入服务和基于事件的机制可以实现任意级别的controller通信。

# 作用域继承

页面：

```html
<div ng-controller="parentCtrl">
    <p>data in parent controller : {{data.name}}</p>
    <div ng-controller="childCtrl">
        <input type="text" ng-model="data.name">
    </div>
</div>
```

控制器：

```js
angular.module('demo',[])
.controller("parentCtrl",["$scope",function($scope){
    $scope.data = {
        name: 'zhangsan'
    }
}])
.controller("childCtrl", ["$scope",function($scope){

}]);
```

以上是父controller中的数据是引用类型的情况。如果父controller中的数据是基本类型，可通过`$scope.$parent.data`访问。
**该方式只适用于父子级别的controller之间的通信**

# 注入服务

在Angular中，服务是一个单例，所以在服务中生成一个对象，该对象就可以利用依赖注入的方式在所有的控制器中共享。
定义一个service：

```js
angular.module("demo")
.factory("Data",function(){
    return {
        name: 'zhangsan'
    }
})
```

页面:

```html
<div ng-controller="childCtrl1">
    <h3>data in child controller 1 : {{data.name}}</h3>
    <input type="text" class="form-control" ng-model="data.name">
</div>
<div ng-controller="childCtrl2">
    <h3>data in child controller 2 : {{data.name}}</h3>
    <input type="text" class="form-control" ng-model="data.name">
</div>
```

控制器：

```js
.controller("childCtrl1",["$scope","Data",function($scope,Data){
    $scope.data = Data;
}])
.controller("childCtrl2",["$scope","Data",function($scope,Data){
    $scope.data = Data;
}])
```

# 基于事件

Angular为`$scope`提供了冒泡和隧道机制，`$broadcast`会把事件广播给所有子controller，而`$emit`则会将事件冒泡传递给父controller，`$on`则是angular的事件监听函数，利用这三者，可以实现上下级和同级(需要构造一个共同的父级control)之间的通信。

## 上下级之间

如果是子controller往父controller上发送事件(从作用域往上发送事件)，使用`scope.$emit`

```js
$scope.$emit("someEvent",{});
```

如果是父controller往子controller上发送事件(从作用域往下发送事件)，使用`scope.$broadcast`

```js
$scope.$broadcast("someEvent", args);
```

无论是`$emit`还是`$broadcast`发送的事件，都用`$scope.$on`来接收：

```js
$scope.$on("someEvent",function(event,data){
    //此处可以获得发送过来的数据data
});
```

## 同级之间
同级之间利用事件通信有两种方式。一种是利用上下级之间事件传播的变形，另一种是借助`$rootScope`。

### 借助父controller

在子controller中向父controller触发一个事件，然后在父controller中监听事件，再广播给子controller，这样通过事件携带的参数，实现了数据经过父controller，在同级controller之间传播。

但是要注意，通过父controller作为中介进行传递的话，子controller触发的事件名和父controller广播用的事件名不能一样，否则会进入死循环。

```html
<div ng-controller="outerCtrl">
    <h3>{{name}}</h3>
    <div ng-controller="innerCtrl1">
        <input type="text" ng-model="name" ng-change="change()">
    </div>
    <div ng-controller="innerCtrl2">
        <input type="text" ng-model="name" ng-change="change()">
    </div>
</div>
```
```js
.controller("outerCtrl",function($scope){
    $scope.name = 'zhangsan',
    $scope.$on("dataChanged",function(event,data){
        $scope.name = data;
        //2. 父ctrl监听到dataChanged事件后，触发changeData事件
        $scope.$broadcast("changeData", data);
    });
})
.controller("innerCtrl1",function($scope){
    $scope.change = function(){
        //1. 子controller中数据发生改变之后触发dataChanged事件
        $scope.$emit("dataChanged",$scope.name);
    }
    $scope.$on("changeData",function(event,data){
        $scope.name = data;
    })
})
.controller("innerCtrl2",function($scope){
    $scope.change = function(){
        $scope.$emit("dataChanged",$scope.name);
    }
    //3. 监听到changeData事件后，改变ctrl2中的数据
    $scope.$on("changeData",function(event,data){
        $scope.name = data;
    })
})
```

### 借助`$rootScope`

每个 Angular 应用默认有一个根作用域 `$rootScope`,根作用域位于最顶层，从它往下挂着各级作用域。

```html
<div ng-controller="innerCtrlA">
    <input class="form-control" type="text" ng-model="name" ng-change="change()">
</div>
<div ng-controller="innerCtrlB">
    <input class="form-control" type="text" ng-model="name" ng-change="change()">
</div>
```
```js
.controller('innerCtrlA', ['$scope', '$rootScope', function($scope, $rootScope){
    $scope.change = function(){
        // 广播事件
        $rootScope.$broadcast('nameChanged', $scope.name);
    }
    $rootScope.$on('nameChanged', function(event, data){
        $scope.name = data;
    })
}])
.controller('innerCtrlB', ['$scope', '$rootScope', function($scope, $rootScope){
    $scope.change = function(){
        $rootScope.$broadcast('nameChanged', $scope.name);
    }
    // 监听事件
    $rootScope.$on('nameChanged', function(event, data){
        $scope.name = data;
    })
}])
```