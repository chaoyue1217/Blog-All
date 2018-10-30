---
date: 2017-06-05
title: ocLazyload
categories: Angular组件
---

# Angular 懒加载之ocLazyload

- 1.ocLazyLoad是AngularJS的第三方库，可以实现模块、控制器及其他依赖的动态加载/按需加载
- 2.$ocLazyLoad.load()支持多个文件，可以自定义指定文件类型，可以自定义指定是否缓存在客户端
- 3.在 AngularJs 实现动态（懒）加载主要是依赖于3个主js文件

```html
<script src="angular/1.4.8/angular/angular.min.js"></script>
<script src="angular/ui-router/release/angular-ui-router.min.js"></script>
<script src="angular/oclazyload/src/ocLazyLoad.min.js"></script>
```

## 具体使用

-  配置ocLazyload

```js
 var app = angular.module('pkcms', ["ui.router", "oc.lazyLoad"]);
 app.config(['$ocLazyLoadProvider',function($ocLazyLoadProvider){
    $ocLazyLoadProvider.config({
        //loadedModules: ['monitorApp'],//主模块名,和ng.bootstrap(document, ['monitorApp'])相同
       // jsLoader: requirejs, //使用requirejs去加载文件
        //files: ['modules/summary','modules/appEngine','modules/alarm','modules/database'], //主模块需要的资源，这里主要子模块的声明文件
        debug: true,
        modules: [
              {
                  name: 'ngGrid',
                  files: [
                      'vendor/modules/ng-grid/ng-grid.min.js',
                      'vendor/modules/ng-grid/ng-grid.min.css',
                      'vendor/modules/ng-grid/theme.css'
                  ]
              },                           
              {
                  name: 'ui.select',
                  files: [
                      'vendor/modules/angular-ui-select/select.min.js',
                      'vendor/modules/angular-ui-select/select.min.css'
                  ]
              }]
    });
}]);
```

- 配置uiRouter

```js
angular.module('app')
  .config(['$stateProvider', '$urlRouterProvider',function ($stateProvider,$urlRouterProvider) {
          $urlRouterProvider.otherwise('/app/main/index');
          $stateProvider
            .state('app.gateway.gateway',{
                url: '/gateway',
                templateUrl: 'tpl/gateway.html',
                resolve: {
                    deps: ['$ocLazyLoad',function( $ocLazyLoad ){
                        //加载多个模块或者文件时使用数组
                        return $ocLazyLoad.load('ngGrid').then(function(){
                            return $ocLazyLoad.load('js/controllers/gateway.js');
                          });
                      }]
                  }
              })
  }]);

```