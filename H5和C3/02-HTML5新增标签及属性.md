---
title: HTML5新增标签及属性
date: 2016-03-25
categories: HTML5
---

# HTML5新增标签及属性
* 1.`<doctype>`不是标签，是一种文档声明，告诉浏览器按照哪种规则解析
* 2.新增语义标签：header  footer等
## 新增语义标签兼容低版本IE
* 1.第一种：使用原生js创建标签，添加到页面中
* **注意：**标签创建完成后必须使其`display=block`
* 示例：浏览器只有在遇到IE8及以下时才会执行其中的代码
* **注意:**lte  代表:  less than  equal
``` html
  <!--[if lte IE 8]>
  <script>
    var header = document.createElement("header");
    //必须设置display
    header.style.display = "block";
    document.body.appendChild(header);
  </script>
  <![endif]-->
```
* 2.第二种：外部引入js库实现兼容
```html
  <!--[if lte IE 8]>
    <script src="html5shiv.min.js"></script>
  <!--<![endif]&ndash;&gt;-->
```

## 多媒体标签  audio  vedio
* vedio:属性：
  * controls控制器
    * autoplay：自动播放
    * loop:循环播放
    * width/height:不会拉伸视频
    * 可以vedio标签中使用source标签同时指定多个视频，目的是为了兼容浏览器
* 示例：
```html
  <video autoplay controls>
   <source src="movie/rabbit.ogg" type="">
   <source src="movie/move11.mp4" type="">
   亲,您的浏览器版本过低，建议更新观看
  </video>
```
* 多媒体标签可以直接被用户另存为

## HTML5表单

### 表单新属性
* color  	拾取颜色
* date  设置日期
* range范围  0-100
* number数字
* tel  email   search 这三个属性在PC端没有明显变化，但是在移动端会根据属性的不同改变键盘 email： @    tel： 九宫格数字键盘  search： 右下角是搜索或者是放大镜的外观
### 表单新元素
* `<output></output>` 用于接收输出数据，仅仅是增强了语义（可以使用span代替）
* `<keygen/>` 密钥
* `<datalist></datalist>`  不能单独使用，需要配合输入使用 输入框通过list属性跟 datalist的id进行关联  选中的是option的value值
  示例：
```html
<input type="text" list="foodlist" >
<datalist id='foodlist'>
    <option value="vegetable">西兰花</option>
    <option value="beef">牛肉</option>
   	<option value="rice">泰国香米</option>
</datalist>
```
* **注意：** 以上三种基本不用
* 两种进度条：
   ` <meter value=".5"></meter> `
   `<progress value="50" max="100"></progress>`  需要设置value和max才能显示百分比
* **注意：** 使用进度条时，一般都是自定义
### 表单验证
* 表单的非空验证： required 
* type = email 自带格式验证 必须为邮箱格式
* pattern  自定义验证规则：正则表达式
### 表单中的事件
* onchange 事件 会在元素的值发生改变的时候触发 会在变化停止后触发 
  示例：
```html
<input type="range"  id='myRange'><span id='output'>0</span>
<script>
 	var rangeDom = document.getElementById('myRange');
  	rangeDom.onchange = function(){
   	 	console.log(this.value);
  	}
</script>
```
* oninput 事件会在用户输入时触发  会一直触发
```html
rangeDom.oninput = function(){
    console.log('我正在输入');
    console.log(this.value);
  }
```
* oninvalid 元素验证失败会触发  需要绑定给对应的表单元素
  示例：
```html
 <input type="tel" placeholder="手机号" pattern="\d{4}" id='tel'>
 <script>
 	document.getElementById('tel').oninvalid = function(){
   		console.log('验证失败');
    	this.setCustomValidity('请输入手机号');
 	}
</script>
```

## HTML5 DOM操作扩展
* document.querySelector() 传入的参数是选择器 还有CSS3的选择器  获取的是满足条件的第一个元素
* document.querySelectorAll()  获取的是一个数组
## HTML5  操作类样式
DOM对象.classList 获取到的是一个对象
* 方法：add()增加类样式
  remove() 删除类样式
  contains() 判断类样式是否存在
  toggle() 切换类样式，有则删除，无则添加
## HTML5 自定义属性
* 推荐使用“data-”
* 使用data时，如果定义的时候使用了大写字母获取的时候会变成小写,我们需要通过小写字母来获取
* 我们使用 多个 `-` 进行分割，获取时需要使用驼峰标识
  示例：
```html
<ul>
	<li data-sex='girl' data-boyFriend='佐助' data-mother='泰罗奥特曼' 		data-cloth-green='绿裙子.绿鞋子,绿手套' data-cloth-red='红裙子,红裤子...'>	   娜美</li>
</ul>
<script>
	nameiLi = document.querySelector("li");
	console.log(nameiLi.dataset); //dataset对象中包含中自定义的属性的内容
 	console.log(nameiLi.dataset.mother);
	console.log(nameiLi.dataset.boyFriend);
 	console.log(nameiLi.dataset.boyfriend);
 	console.log(nameiLi.dataset.clothGreen);
 	console.log(nameiLi.dataset['clothGreen']);
</script>	
```



