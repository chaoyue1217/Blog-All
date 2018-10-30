---
title: canvas基本使用
date: 2016-06-10
categories: canvas
---

# Canvas基础

> canvas : 画布默认宽高是300x150;
> canvas的宽高要使用canvas标签的属性设置，不能使用css设置
* 简单画一条直线
```html
<canvas id="cas" style="border: 1px solid blue;" width="600" height="400"></canvas>
<script>
onload = function () {
    var cas = document.getElementById("cas");
    console.log(cas.height);
    console.log(cas.width);
    //获取画图工具
    var ctx = cas.getContext("2d");
    //指定画图的起点
    ctx.moveTo(100,100);
    //画路径
    ctx.lineTo(300,100);
    //描边
    ctx.stroke();
}
</script>
```
* 绘制矩形
> **非零环绕原则(填充的原则)：**从闭合区域中画一条线，这条线经过的线条中，如果正向与反向的线条相互抵消，则不填充
```html
  <canvas id="cas" style="border: 1px solid blue;" width="600" height="400"></canvas>
  <script>
    //绘制正方形
    //ctx.closePath()作用是闭合路径
    var cas = document.getElementById("cas");
    var ctx = cas.getContext("2d");
    ctx.moveTo(100,100);
    ctx.lineTo(300,100);
    ctx.lineTo(300,300);
    ctx.lineTo(100,300);
    ctx.lineTo(100,100);
    ctx.closePath();//闭合路径
	//绘制内部矩形
    ctx.moveTo(150,150);
    ctx.lineTo(150,250);
    ctx.lineTo(250,250);
    ctx.lineTo(250,150);
    ctx.lineTo(150,150);
    ctx.closePath();
    //设置线的颜色
    ctx.strokeStyle ="red";
    //设置线的宽度
    ctx.lineWidth = 10;
    ctx.stroke();
    //填充
    ctx.fill();
  </script>
```
> 手动闭合路径和closePath()
```html
<canvas id="cas" style="border: 1px solid blue;" width="600" height="400"></canvas>
<script>
    //手动闭合路径和closePath()闭合路径
    /**
     * 如果线宽较大，会明显缺角，补上这个角需要多加一个点
     *closePath()闭合路径可以省略最后一条线
     */
    var cas = document.getElementById("cas");
    var ctx = cas.getContext("2d");
    ctx.lineWidth = 10;
    ctx.moveTo(100,100);
    ctx.lineTo(300,100);
    ctx.lineTo(300,300);
    //如果使用closePath().可以省略下面1,2两条代码
    ctx.lineTo(100,100);//1
    //手动闭合
    ctx.lineTo(101,100);//2
//    ctx.closePath();
    ctx.stroke();
</script>
```
* 绘制曲线
```html
<canvas id="cas" style="border: 1px solid blue;" width="600" height="400"></canvas>
<script>
    var cas = document.getElementById("cas");
    var ctx = cas.getContext("2d");
    //绘制曲线：画一个一个的点，点的路线符合方程曲线的规律
    ctx.moveTo(0,0);
    for(var i=0;i<1000;i++){
        var x = i;
        //正弦曲线
        var y = 100*Math.sin(x/50)+100;
        ctx.lineTo(x,y);
    }
    ctx.stroke();
</script>
```
## 常用属性
* lineWidth:线宽
* strokeStyle：线的颜色
* fillStyle：填充颜色
* lineCap:线末端的类型，默认是butt，可以设置round、square
* lineJion:相交线的拐点类型，默认是miter，可设置round，bevel
* setLineDash():设置虚线,参数是一个数组，设置黑白切换
```html
<canvas id="cas" style="border: 1px solid blue;" width=600 height=400></canvas>
<script>
    var cas = document.getElementById("cas");
    var ctx = cas.getContext("2d");
    ctx.lineWidth = 50;
    ctx.moveTo(100,100);
    ctx.lineTo(400,100);
    ctx.setLineDash([10,15,20]);
    ctx.stroke();
</script>
```