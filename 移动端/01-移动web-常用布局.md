---
title: 移动端常用布局
date: 2016-04-12
categories: mobile
---

# 自适应布局
* 适应移动端
* 也称作流式布局
* 通过设置宽度的百分比实现
## 视口viewport
* 视口是用来承载网页的，只在移动端有
* 视口的宽度和浏览器的宽度一致
* width: 视口的宽度。
  * 设置成设备宽度`device-width`，达到适配
* initial-scale: 默认的初始缩放比例，标准比例为1:1。
  * 只设置`initial-scale=1.0`也可以达到适配
* user-scaleable: 是否允许用户自行缩放。可以设置no/yes或者0/1。默认可以缩放
* maximum-scale: 最大允许缩放比
* minimum-scale: 最小允许缩放比
* **标准移动端开发设置**`<meta name="viewport" content="width=device-width,initial-sccale=1.0,user-scaleable=0">`
## 两栏自适应
* 实现原理: 一个盒子浮动，一个盒子通过设置`overflow:hidden`，触发BFC(块级格式化上下文)，产生一个绝缘的盒子，两个盒子之间互不影响
```html
 <style>
     body{
       margin:0;
       padding: 0;
     }
     div:first-child{
       width:100px;
       height: 100px;
       float: left;
       background: red;
     }
     /*该盒子自动填满剩余部分*/
     div:last-child{
     	overflow: hidden;
     }
</style>
<body>
	<div></div>
    <div>
    内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内容内
    </div>
```
## 移动端常用事件
### touch事件
* touchstart、touchmove、 touchend
* event.changedTouches 所有的改变的触摸点集合
* event.targetTouches  目标元素上的所有的触摸点集合
* event.touches   页面上所有的触摸点集合
* 可通过以上三个对象，获取触摸点在屏幕上的位置，如：` event.touches[0].clientX`
###  过渡结束事件
* transitionend
* 需要通过addEventListener()绑定
### 动画结束事件
* animationend 
* 需要通过addEventListener()绑定
### tap事件
* 使用fastclick插件
* 解决了移动端click事件响应延迟300ms的问题
* 通过封装touch事件实现
```javascript
function tap(dom,callback){
    /**
     * 1, 没有滑动过
     * 2, 比click响应快  150ms以内的响应都认为是tap事件
     * */
    if(!dom||typeof dom != "object") return false;
    var isMove = false;/*默认没有滑动*/
    var startTime = 0;/*记录开始触摸的时间*/
    dom.addEventListener("touchstart",function () {
        // startTime = new Date().getTime();
        startTime = Date.now();/*获取当前的时间*/
    });
    dom.addEventListener("touchmove",function () {
        isMove = true;
    });
    dom.addEventListener("touchend",function (e) {
        /*计算响应时间*/
        var time = Date.now()-startTime;
        /*确定是不是tap事件*/
        if (!isMove && time<150){
            /*若回调函数存在则执行*/
            callback && callback(e);
        }
        /*重置默认参数*/
        isMove = false;
    });
}
```

# 响应式布局
* 实现原理: 媒体查询(Media Query)
* 适配不同的终端
* 与自适应布局的区别: 自适应布局适配的是移动端，响应式布局适配的是多个终端
## 媒体查询
* 根据媒介的不同或者媒介的值的不同，设置不同的CSS
* 响应式原理: 根据屏幕大小的不同使用不同的布局
```html
<style>
	body{
      margin: 0;
      padding: 0;
	}
    .container{
      width: 1200px;
      margin: 0 auto;
      background: red;
      height: 1000px;
    }
	/*1.在宽屏设备  版心宽度变成1170px  背景颜色 粉红  1200-*/
    @media screen and (min-width: 1200px){
      .container{
        width: 1170px;
        background: pink;
      }
    }
        /*2.在中等设备  版心宽度变成970px  背景颜色 绿色  992-1200*/
    @media screen and (min-width: 992px) and (max-width: 1200px){
      .container{
        width: 970px;
        background: green;
      }
    }
        /*3.在小屏幕设备  版心宽度变成750px  背景颜色 蓝色 768-992*/
    @media screen and (min-width: 768px) and (max-width: 992px){
      .container{
        width: 750px;
        background: blue;
      }
    }
        /* 4.在超小屏设备  版心宽度变成100%  背景颜色 黄色 -768*/
    @media screen and (max-width: 768px){
      .container{
        width: 100%;
        background: yellow;
      }
    }
</style>
<body>
    <div class="container"></div>
</body>
```
* **扩展: 第二种写法**
* 通过样式覆盖实现
```html
<style>
        body{
            margin: 0;
            padding: 0;
        }
        .container{
            width: 1200px;
            margin: 0 auto;
            background: red;
            height: 1000px;
        }
        /*4.在超小屏设备  版心宽度变成100%  背景颜色 黄色 -768*/
        .container {
            width: 100%;
            background: yellow;
        }
        /*3.在小屏幕设备  版心宽度变成750px  背景颜色 蓝色 768-992*/
        @media (min-width: 768px) {
            .container {
                width: 750px;
                background: blue;
            }
        }
        /*2.在中等设备  版心宽度变成970px  背景颜色 绿色  992-1200*/
        @media (min-width: 992px) {
            .container {
                width: 970px;
                background: green;
            }
        }
        /*1.在宽屏设备  版心宽度变成1170px  背景颜色 粉红  1200-*/
        @media (min-width: 1200px) {
            .container{
                width: 1170px;
                background: pink;
            }
        }
</style>
<body>
    <div class="container"></div>
</body>
```