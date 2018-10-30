---
date: 2017-06-03
title: ui-router
categories: Angular组件
---
# ui-router
## 使用方法
```javascript
angular.module("myApp",["ui.router"]);
```
- 和ngRouter服务不同的是
  + （1）ui-router可以进行视图的嵌套；
  + （2）视图的存放位置使用ui-view来指定，每一个ui-view下面也可以包含自己的ui-view；
  + （3）使用config对路由进行配置时，使用的不是$routerProvider而是$stateProvider。
- 定义路由的方式

```javascript
 .config(function($stateProvider, $urlRouterProvider) {
    $stateProvider
      .state('start', {
        url: '/start',
        templateUrl: 'partials/start.html'
      })
});
```

## 路由嵌套
- 使用url参数可是实现路由嵌套

```html
$stateProvider
  .state('inbox', {
    url: '/inbox/:inboxId',
    template: '<div><h1>Welcome to your inbox</h1>
            <a ui-sref="inbox.priority">Show priority</a>
            <div ui-view></div>
            </div>',
    controller: function($scope, $stateParams) {
      $scope.inboxId = $stateParams.inboxId;
    }
  })
  .state('inbox.priority', {
    url: '/priority',
    template: '<h2>Your priority inbox</h2>'
  });

```
- 第一个state是平时常见的路由，第二个state是二级路由，即使用"."来进行子路由的定义。

## abstract抽象模块
- 抽象模块不能被激活，但是他的子模块可以。抽象模块提供了一个包括了多个有名的视图的模板，或者它可以传递作用域变量$scope给子模板。
- 使用时除了需要添加abstract属性外，其他设置和设定一个常规状态是相同的

```javascript
$stateProvider
  .state('admin', {
    abstract: true,
    url: '/admin',
    template: '<div ui-view></div>'
  })
  .state('admin.index', {
    url: '/index',
    template: '<h3>Admin index</h3>'
  })
  .state('admin.users', {
    url: '/users',
    template: '<ul>...</ul>'
  });
```

## $urlRouterProvider
- 和ngRoute一样，开发者可以在该对象上设定特定的URL被激活时做什么的规则。由于设定好的状态在特定的url被访问是会自动激活，所以$urlRouterProvider没有必要用来管理激活和载入状态。但当需要管理哪些被发生在当前状态之外的作用域scope时它会非常有用，例如在重定向或者安全验证的时候。在模块的设置函数里便可使用$urlRouterProvider。
  **when()**
- 该函数需要两个参数，第一个是当前的路径，第二个是需要重定向到的路径（或者是需要在路径被访问是运行的函数）。
- 如果传递的是函数，在路径被匹配时该函数会被执行，处理器返回如下3个值中的一个
  + （1）falsy，该回应告诉$urlRouter没有匹配到当前url规则，应该尝试匹配新的路径，这样能保证用户访问了正常的路径。 - 字符串，$urlRouter将该字符串当做重定向的路径。 
  + （2）TRUE 或者 undefined，该回应告诉$urlRouter，url已被处理

```js
.config(function($urlRouterProvider) {
  $urlRouterProvider.when('', '/inbox');
});
```

**otherwise()**
- 和ngRoute的otherwise()函数相似，在用户提交的路径没有被定义的时候它将重定向到指定的页面。这是个创建’默认‘路径的好方法。otherwise()只接受一个参数，要么函数要么字符串，字符串必须为合法的url路由地址，函数则会在没有任何路径被匹配的时候被运行。

```js
.config(function($urlRouterProvider) {
  $urlRouterProvider.otherwise('/');
  // or
  $urlRouterProvider.otherwise(
    function($injector, $location) {
      $location.path('/');
    });
});
```

## $stateProvider
- 有三种方式激活一个状态
    + 调用go()方法
    + 使用ui-sref指示地址
    + 使用state()导航至进行关联的url

### 模板
```js
//templateUrl的值可以是字符串，也可以是一个函数
$stateProvider.state('contacts', {  
  templateUrl: 'contacts.html'  
});
//函数
$stateProvider.state('contacts', {  
  templateUrl: function (stateParams){  
    return '/partials/contacts.' + stateParams.filterBy + '.html';  
  }  
}) 
```

- 可以使用注入功能，但是必须返回html

```js
$stateProvider.state('contacts', {  
  templateProvider: function ($timeout, $stateParams) {  
    return $timeout(function () {  
      return '<h1>' + $stateParams.contactId + '</h1>'  
    }, 100);  
  }  
}) 
```

### 控制器
- 可以使用控制器**controller**，但是必须要有模板

```js
$stateProvider.state('contacts', {  
  template: '<h1>{{title}}</h1>',  
  controller: function($scope){  
    $scope.title = 'My Contacts';  
  }  
}) 

```

### resolve
- resolve可以为控制器提供可选的依赖注入项。
- 在controller实例化之前，resolve中的每一个对象都必须 be resolved

```js
$stateProvider.state('myState', {
      resolve:{
         // Since it's not a promise, it resolves immediately.
         simpleObj:  function(){
            return {value: 'simple!'};
         },
         // 这是一种典型使用方式
         // 请给函数注入任何想要的服务依赖，例如 $http
         promiseObj:  function($http){
            return $http({method: 'GET', url: '/someUrl'});
         },
         // 如果想对返回结果进行处理， 可以使用 .then 方法
         // 这是另一种典型使用方式
         promiseObj2:  function($http){
            return $http({method: 'GET', url: '/someUrl'})
               .then (function (data) {
                   return doSomeStuffFirst(data);
               });
         },        
         // 使用服务名的例子，这将在模块中查找名称为 'translations' 的服务，并返回该服务 
         translations: "translations",
         // 将服务模块作为解决函数的依赖项，通过参数传入
         // 提示：依赖项 $stateParams 代表 url 中的参数
         translations2: function(translations, $stateParams){
             translations.getLang($stateParams.lang);
         },
         greeting: function($q, $timeout){
             var deferred = $q.defer();
             $timeout(function() {
                 deferred.resolve('Hello!');
             }, 1000);
             return deferred.promise;
         }
      },
      // 控制器将等待上面的解决项都被解决后才被实例化
      controller: function($scope, simpleObj, promiseObj, promiseObj2, translations, translations2, greeting){
          
          $scope.simple = simpleObj.value;
          // 这里可以放心使用 promiseObj 中的对象
          $scope.items = promiseObj.items;
          $scope.items = promiseObj2.items;
          $scope.title = translations.getLang("english").title;
          $scope.title = translations2.title;
          $scope.greeting = greeting;
      }
   })

```
