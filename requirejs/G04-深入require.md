---
title: 深入requirejs
date: 2016-9-15
categories: AMD和CMD
---
# 核心
- RequireJS的目标是鼓励代码的模块化，它使用了不同于传统<script>标签的脚本加载步骤。可以用它来加速、优化代码，但其主要目的还是为了代码的模块化。
- requirejs解决了单纯的script标签引入文件式的文件依赖问题
- 实现了模块化

# require使用流程
1、我们在使用requireJS时，都会把所有的js交给requireJS来管理，也就是我们的页面上只引入一个require.js，把data-main指向我们的main.js。

2、通过我们在main.js里面定义的require方法或者define方法，requireJS会把这些依赖和回调方法都用一个数据结构保存起来。

3、当页面加载时，requireJS会根据这些依赖预先把需要的js通过document.createElement的方法引入到dom中，这样，被引入dom中的script便会运行。

4、由于我们依赖的js也是要按照requireJS的规范来写的，所以他们也会有define或者require方法，同样类似第二步这样循环向上查找依赖，同样会把他们存起来。

5、当我们的js里需要用到依赖所返回的结果时(通常是一个key value类型的object),requireJS便会把之前那个保存回调方法的数据结构里面的方法拿出来并且运行，然后把结果给需要依赖的方法。

# 原理分析
- require函数实现用一句话概括：依次加载require的模块，然后监测script的onload事件，判断所有模块加载成功，执行require的callback，如果只带一个参数且不是数组，就是加载成功后return 模块。
- __简单原理实现__
```js
//标记已经加载成功的个数
var REQ_TOTAL = 0;
//模块导出
window.exports = {};
//记录各个模块的顺序
var exp_arr = [];

//判断是否数组
function isArray(param) {
  return param instanceof Array;
}

//require 真正实现
function require(arr, callback) {

  var req_list;
    //如果传入的是数组，赋值给req_list
  if(isArray(arr)) {
    req_list = arr;
  } else {//如果传入的不是数组，将其放在数组中传给req_list
    req_list = [arr];
  }
  //记录数组的长度
  var req_len = req_list.length;

  //模块逐个加载
  for(var i=0;i<req_len;i++) {
    var req_item = req_list[i];
    //创建script标签
    var $script = createScript(req_item, i);
    //获取页面中的head标签
    var $node = document.querySelector('head');
    //使用闭包保证模块的正常加载
    (function($script) {
      //检测script 的onload事件
      $script.onload = function() {
        REQ_TOTAL++;
        
        var script_index = $script.getAttribute('index');
        exp_arr[script_index] = exports;
        window.exports = {};
        //所有链接加载成功后，执行callback
        if(REQ_TOTAL == req_len) {
          callback && callback.apply(exports, exp_arr);
        }
      }
      //在head中添加script标签
      $node.appendChild($script);
    })($script);
  }
}
//创建一个script标签
function createScript(src, index) {
  var $script = document.createElement('script');

  $script.setAttribute('src', src);
  $script.setAttribute('index', index);

  return $script;
}

```
