---
title : 浅析AngularJS的生命周期
date : 2016-09-20
categories: AngularJS
---

## AngularJS的生命周期

- 在AngularJS应用启动之前，会以html文本的形式保存在文本编辑器中。应用启动后会进行编译和链接，作用域会和html进行数据绑定。
### 编译阶段
- 在编译阶段，AngularJS会遍历整个HTML文档并根据JavaScript中的指令定义来处理页面上声明的指令。
- 每一个指令中都可能会含有另一个指令，因此，AngularJs进行编译的时候会形成一个模板树，并遍历其中的所有模板。
- 一旦对指令和其中的子模板进行遍历或编译，编译后的模板会返回一个叫模板函数的函数。我们可以在模板函数被返回之前，对编译后的DOM树进行操作
  + 例如：ng-repeat，它会遍历指定的数组或对象，在数据绑定之前构建出对应的DOM结构。
- 每个指令都有自己的模板和编译函数，每个模板返回自己的模板函数，模板函数最后会被传递给编译后的DOM树中的每个指令中定义的链接函数。
### compile编译函数
- 在模板函数返回之前，我们可以使用compile对编译后的DOM树进行DOM操作。
- compile函数的返回值即链接函数或者链接对象，即link
- 本质上，当我们设置了link选项，实际上是创建了一个postLink()链接函数，以便compile()函数可以定义链接函数。
- __compile和link选项是互斥的。如果同时设置了这两个选项，那么会把compile
  所返回的函数当作链接函数，而link选项本身则会被忽略。__
- compile的作用是对指令的模板进行转换，常用于自定义指令
```javascript
angular.module('myApp',[])
.directive('myDirective',function(){
    return{
        compile:function(tEle,tAttrs,transcludeFn){
            //在此处可以进行DOM操作
            //在此处不能操作$scope     
            return function(scope,ele,attrs){
            //此处返回的是链接函数，可以在此处处理数据
            //$scope只有在链接阶段才会绑定到元素上 	
            }
        },
        link:function(){
          //如果设置了compile，在此处设置link会被忽略
        }
    }
});
```
### 链接阶段
- 链接函数负责将作用域和DOM进行链接。如绑定数据，注册事件
- __postlink__
  + 我们设置的link其实就是postlink
- __prelink__
- prelink会在Postlink之前执行，他在compile之后执行。
```javascript
angular.module('myApp',[])
.directive('myDirective',function(){
    return{
        compile:function(tEle,tAttrs,transcludeFn){
            //这里进行DOM的操作
            return {
              pre: function(scope, iElem, iAttrs){
                //次处时prelink，在postlink之前，compile之后执行
                // 在子元素被链接之前执行
                // 在这里进行Don转换不安全
                // 之后调用'lihk'函数将无法定位要链接的元素
              },
              post: function(scope, iElem, iAttrs){
                //此处即postlink
              }
            }
        }
    }
});
```