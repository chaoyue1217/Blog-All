---
title: jQuery
date: 2016-03-15
categories: jquery
---

# jquery基础

## jquery中的顶级对象

* DOM中的顶级对象：document
* BOM中的顶级对象：window
* jquery中的顶级对象：jQuery===$
* 在jquery中，将jQuery对象赋给了$
* 释放对$的控制权：$.noConflict();
## jquery对象和DOM对象的相互转换

* DOM对象------->jquery : $(DOM对象)
* jquery对象---------->DOM对象 : jquery对象.get(0)或者 jquery对象[0];
## window.onload和$(document).ready()

### window.onload
* window.onload只能出现一次，如果出现多次会覆盖
* 页面中所有的内容全部加载完毕后才会执行(图片，标签，引入文件......)
### $(document).ready(function(){})
> 可以简写为jQuery(function(){})或者$(function(){})
* 该页面加载事件可以出现多次
* 页面中DOM元素加载完毕后就会执行
## jquery细节问题
### 选择器
* $("selector","父元素"):  在指定的父元素下寻找指定的匹配selector的元素
### 类操作
* removeClass();  移除类样式，当不传参数的时候，移除全部类样式
###  尺寸
* width()  获取的是内容的宽度
* innerWidth()  获取的是content+padding的宽度
* outerWidth() 获取的是content+padding+border的宽度
* outerWidth(true) 获取的整个盒子所占的宽度，即content+padding+border+margin
###  文档处理
* 创建元素：$("html内容") 创建的是jQuery对象
* 复制：clone()  参数默认是false，复制的是标签和内容；传入true时，同时复制事件
###  注意
* 在DOM中如果是获取文本域textarea的内容,使用value属性,如果是设置文本域的内容,最好使用innerText
* 在jQuery中获取和设置文本域的内容最好使用text();
* 使用.val()设置下拉菜单的选中的时候,方法中传入的是某个option的value的值
## 事件
### 事件绑定
* 对象.事件名
* 对象.bind("事件名",事件处理函数)
* 父元素.delegate("子元素","事件名",事件处理函数)
  * 无论子元素在页面中一直存在，还是动态创建的，都可以委托父元素为其绑定事件
* 对象.on("事件名","元素",事件处理函数)
### 事件解绑
* 事件绑定和事件解绑事件在使用时必须对应
  * unbind()
  * undelegate()
  * off()
* .off("事件名") 方法解除绑定事件的时候,如果括号中只有事件类型,那么此时,父级元素和子级元素所有的 该事件都会解除绑定
* `.off("事件名","**")` 解除所有子元素的该事件，固定写法
* .off() 无论是父级元素还是子级元素,所有绑定的事件全部被解除绑定
### 事件触发
* 对象.事件()
* 对象.trigger("事件")
* 对象.triggerHandler("事件")
* **区别**
* 前两种相同，
* trigger会触发浏览器的默认行为
* triggerHandler不会触发浏览器的默认行为