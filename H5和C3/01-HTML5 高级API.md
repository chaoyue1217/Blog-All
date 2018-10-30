---
title: HTML5高级API
date: 2016-03-26
categories: HTML5
---

# HTMl5高级API

## 全屏效果
* **requestFullScreen()方法;**
* 该方法目前还处于测试阶段，要想正常使用必须添加前缀
* 不同的浏览器的渲染效果不同
* ie的全屏方法是msRequestFullscreen(screen中的s是小写)
* 兼容浏览器写法
```html
<input type="button" value="全屏"* 
<script>
documet.querySelector("input").onclick = function(){
    if(this.webkitRequestFullScreen){
        // 谷歌
        this.webkitRequestFullScreen();
    }else if(this.mozRequestFullScreen){
        // 火狐
        this.mozRequestFullScreen();
    }else if(this.msRequestFullscreen){
        // ie的全屏方法是msRequestFullscreen
        // 可以查MSDN
        // IE 
        this.msRequestFullscreen();
    }else if(this.requestFullScreen){
        // 其他
        this.requestFullScreen();
    }
}
</script>
```

## 拖(drag)放(drop)
* 有些元素默认支持鼠标拖放，例如img。但是该拖放只是一种视觉效果，元素并不会移动位置，如果实现元素的位置移动，需要使用js
* 有些不支持拖放，如div
* 设置元素可拖放,需要设置属性`draaable="true"`例如：`<div draggable="true">`
### 拖放事件 
* dragstart: 元素开始拖动事件。设置在被拖动的元素上
* dragend: 元素拖动结束事件。设置在被拖动的元素上
```html
<img src="img/xiang.jpg" alt="">
<script>
    document.querySelector("img").ondragstart = function(){
        // 拖拽开始改变图片内容
        this.src = "img/huli.jpg";
    };
    document.querySelector("img").ondragend = function(){
       	// 拖拽结束还原图片
       	this.src = "img/xiang.jpg";
    };
</script>
```
* dragenter: 有元素拖入时触发
* dragleave: 元素离开时触发
```html
<img src="img/xiang.jpg" alt="">
<div class="box" style="width:300px;height:300px;background:yellowgreen"></div>
<script>
// 图片拖入box时改变box的背景颜色
document.querySelector(".box").ondragenter = function(){
    this.style.background = "yellow";
};
// 图片离开box时还原box的背景颜色
document.querySelector(".box").ondragleave = function(){
    this.style.background ="yellowgreen";
};
</script>
```
* dragover: 规定在何处放置被拖动的数据
* **注意**
* 默认情况下无法将元素放置到其他元素中。若要允许放置，需要阻止元素的默认行为。
* 通过调用ondragover 事件的 event.preventDefault() 方法
* drop: 放置被拖动的元素时触发。只有设置了ondragover事件，drop事件才会触发
```html
<img src="img/xiang.jpg" alt="">
<div class="box" style="width:300px;height:300px;background:yellowgreen"></div>
<script>
document.querySelector(".box").ondragover = function(event){
    event.preventDefault();
}
document.querySelector(".box").ondrop = function(){
    var imgObj = document.querySelector("img");
    this.appendChild(imgObj);
    // 改变img的大小
    imgObj.style.width = "100px";
};
</script>
```
## 地理位置
* 经常用于移动端,PC端的定位不准确
* 该方式只能获取经纬度，若要显示地图，需要配合百度地图等使用
```html
<input type="button" value="获取位置">
<script>
    document.querySelector("input").onclick = function(){
        // 获取当前的位置信息
        window.navigator.geolocation.getCurrentPosition(function(position){
            // 获取位置信息
            console.log(position);
            // 获取纬度
            console.log(position.coords.latitude);
            // 获取经度
            console.log(position.coords.longitude);
        });
    };
</script>
```
## video的API
* video的方法:
  * play(); 播放视频
  * pause(); 暂停视频
* video的事件
  * ontimeupdate: 视频播放时触发的事件
* video元素的属性
* currentTime： 当前的播放时间
* duration: 视频的总时长