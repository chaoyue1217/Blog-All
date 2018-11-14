---
title: Bootstrap
date: 2016-04-15
categories: bootstrap
---

## bootstrap
* bootstrap是一款css框架，用于开发响应式布局
* 主要原理：媒体查询
## bootstrap常用样式
### container类
* 用于定义一个固定宽度且居中的版心
```html
<div class="topbar">
  <div class="container">
    <!--
      此处的代码会显示在一个固定宽度且居中的容器中
      该容器的宽度会跟随屏幕的变化而变化
    -->
  </div>
</div>
```
* container-fluid: 用于定义一个宽度为100%的容器
```html
<div class="topbar">
  <div class="container-fluid">
    <!--
      此处的代码会显示在一个固定宽度且居中的容器中
      该容器的宽度会跟随屏幕的变化而变化
    -->
  </div>
</div>
```
## 栅格系统
- Bootstrap中定义了一套响应式的网格系统，
- 其使用方式就是将一个容器划分成12列，
- 然后通过col-xx-xx的类名控制每一列的占比
### row类
- 因为每一个列默认有一个15px的左右外边距
- row类的一个作用就是通过左右-15px的外边距屏蔽掉这个边距
```html
<style>
	.row{
      margin-left:-15px;
      margin-right:-15px;
	}
</style>
<div class="container">
  <div class="row"></div>
</div>
```
### col-xx-xx类
- col-xs-[列数]：在超小屏幕下展示几份
- col-sm-[列数]：在小屏幕下展示几份
- col-md-[列数]：在中等屏幕下展示几份
- col-lg-[列数]：在大屏幕下展示几份
- __xs__ : 超小屏幕 手机 (<768px)  
- __sm__ : 小屏幕 平板 (≥768px) 
- __md__ : 中等屏幕 桌面显示器 (≥992px) 
- __lg__ : 大屏幕 大桌面显示器 (≥1200px)
```html
<div class="row">
  <div class="col-md-1"></div>
  <div class="col-md-3"></div>
  <div class="col-md-5"></div>
  <div class="col-md-3"></div>
</div>
```
### 扩展
* **列嵌套**
  * 列中可以再嵌套列，可以多层嵌套
* **列偏移**
  * col-xs-offset-n: 向右偏移几份
* **列排序**
  * 可以在不改变页面结构的情况下进行布局
  * col-xs-push-n: 向后推几份
  * col-xs-pull-n: 向前拉几份
```html
<style>
	.container{
  		height: 300px;
  		background: red;
  	}
    .row{
    	height: 100px;
    }
    .col-md-4{
     	height: 100px;
      	border:1px solid white;
      	background: #ff0;

	}
    .row .row{
    	height: 50px;
    	background: hotpink;
    }
    .row .row>div{
    	height: 50px;
    	background: darkorchid;
    	border: 1px solid #ccc;
    }
</style>
<div class="container">
	<div class="row">
    	<!--列嵌套-->
    	<div class="col-md-4">
    		<div class="row">
    			<div class="col-xs-6"></div>
    			<div class="col-xs-6"></div>
    		</div>
    	</div>
    	<!--列偏移-->
   		<div class="col-md-4">
    		<div class="row">
    			<div class="col-xs-6"></div>
    			<!--向右偏移-->
    			<div class="col-xs-4 col-xs-offset-1"></div>
    		</div>
    	</div>
    	<!--列排序-->
    	<div class="col-md-4">
    		<div class="row">
    			<!--向后推-->
    			<div class="col-xs-9 col-xs-push-3"></div>
    			<!--向前拉-->
    			<div class="col-xs-3 col-xs-pull-9"></div>
    		</div>
    	</div>
    </div>
</div>
```
## 字体图标
* 不能直接在标签内写代表某个图标的字符
* 需要写在伪类的content中
* 使用方法和FontAwesome相同
* 制作字体图标的网站：icomoon
* 具体使用方法如下
```html
<style>
/*自定义第三方字体*/
  @font-face {
    /*自定义字体名称*/
      font-family: 'myFont';
      src: url('../font/MiFie-Web-Font.eot') format('embedded-opentype'), 
      url('../font/MiFie-Web-Font.svg') format('svg'), 
      url('../font/MiFie-Web-Font.ttf') format('truetype'), 
      url('../font/MiFie-Web-Font.woff') format('woff');
  }
  /*声明图标字体*/
  .myFont{
    	font-family:"myFont";
  }
  .myFont-phone::before{
  		/*代表图标的字符*/
   		content:"\e908";
  }
  .myFont-tel::before{
    	content:"\e909";
  }
</style>
<body>
	<div class="container">
		<!--使用类名显示图标字体-->
		<span class="myFont myFont-phone"></span>
		<span class="myFont myFont-tel"></span>
	</div>
</body>
```
### 字体文件格式
- eot : embedded-opentype
- svg : svg
- ttf : truetype
- woff : woff
