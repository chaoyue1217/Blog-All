---
title: ajax-跨域
date: 2016-04-25
categories: ajax
---

# 跨域

## url的规范格式
* `scheme://host:port/path?query#fragment`
* scheme:表示协议 http https ftp file
* host : 表示域名或者IP地址
* port : 端口 IP地址+端口可以确定计算机上的具体某个网络应用程序
* path : 域名之后，参数(?)之前的部分叫做路径  /aaa/bbb/ccc
* query : 查询字符串，作用就是向服务器传递数据  键=值&键=值
* fragment : hash 锚点 定位页面的某一部分
* `scheme://host:port` 该三项是必需的
## 同源策略
* 同源策略是一种约定，是浏览器最核心的安全功能
* 协议，域名，端口全部相同则是同源，不同源则跨域
## 跨域
* 在A站点向B站点发送访问请求时，只要A站点和B站点的协议，域名，端口有一个不相同，就会跨域，浏览器会禁止去访问B站点的资源
* 跨域是浏览器的一种安全限制
## 跨域的解决方案
### 主域名相同 (document.domain+iframe)
* 如果两个站点的主域名相同，二级域名不同时，在两个页面同时设置`document.domain="主域名"`即可。例如:在`http://www.tianmao.com`页面要去访问`http://login.tianmao.com`中的资源，即可以在两个页面中设置 `document.domain="tianmao.com"`，即可允许访问
```html
<!--主域名界面-->
 <iframe src="http://www.digou.com" frameborder="0"></iframe>
 <script>
 	document.domain = "digou.com";
 	var iframe=document.querySelector("iframe");
 	iframe.onload=function(){
 	this.contentWindow.document.querySelector("p").style.color="blue";
 	}
</script>
```
```html
<!--二级域名 如：http://login.digou.com-->
<p>这里是二级域名界面</p>
<script>
	document.domain = "digou.com";
</script>
```
### jsonp方式解决(script)
#### 原理
* 由于时script标签不受同源策略的限制，所以使用script标签的src属性去访问一个跨域的站点
* 通过发送callback="回调函数”请求参数，服务器端返回一个回调函数的调用
* 服务端的数据作为回调函数的参数，传递给客户端
* 客户端再对接收到的数据进行处理
* **注意**
* jsonp方式是get请求
* 回调函数需要在客户端设置，并且为全局函数
* 凡是拥有src属性的标签都可以跨域，并且都有onload事件
#### jQuery中的解决方式
* 在$.ajax()中设置dataType属性`dataType:"jsonp"`;
#### 模拟$.ajax中jsonp原理的封装
```html
<input type="button" value="点击" id="btn">
<script src="jquery.js"></script>
<script>
$(function () {
  	$("#btn").click(function () {
  		//jsonp 原理
        function ajax(obj){
        	//设置默认的参数
            var defaluts = {
            	jsonp: "callback"
            }
            //覆盖defaults的属性
        	for(var key in obj){
        		defaluts[key] = obj[key];
        	}
        	//默认的回调函数的函数名
        	var jqueryName = "jQuery"+("v1.11.2"+Math.random()).replace(/\D/g,"")+(new Date().getTime());
        	//如果传进了设置回调函数名的参数，则将默认的覆盖
        	if(defaluts.jsonpCallback){
        		jqueryName = defaluts.jsonpCallback;
        	}
        	//设置后台要调用的回调函数，并将接收到的后台数据传递给响应成功的函数
          	//这样后台的数据就会以参数的形式传递给success
        	window[jqueryName] = function (data) {
        		defaluts.success(data);
        	};
        	//创建script标签
        	var script = document.createElement("script");
        	script.src = defaluts.url + "?" + defaluts.jsonp+"="+jqueryName;
        	document.body.appendChild(script);
    	}
        ajax( {
            url:"jsonp.php",
            //修改函数名
            dataType:"jsonp",
            jsonCallback:"callback",
            //修改参数名
            jsonp:"cb",
            success:function (data) {
            	console.log(data);
            }
        });
    });
});
</script>
```
* **后台代码**
```php
<?php
	$cbName = $_GET['cb'];
    $arr = array('apple','banana','orange');
	//json_encode是将PHP格式的数据转化成json格式
    echo $cbName.'('.json_encode($arr).')';
?>
```
#### 原生js解决
```html
<script>
//假设该站点是http://www.b.com/01.html,去访问http://www.a.com/01.php中的资源
    window.onload = function () {
        var script = document.createElement("script");
        script.src = "http://www.a.com/01.php?callback=gteInfo";
        document.body.appendChild(script);
    }
     function gteInfo(data) {
    //服务器端返回的数据为参数data，在此处进行后台数据的处理
        console.log(data);
    }
</script>
```
```php
<?php
	//php代码
    header("Content-Type:text/html;charset=utf-8");
    $callback = $_GET["callback"];
    $data = '{"username":"zhangsan"}';
    //$data 作为服务端的数据，作为回调函数的参数传递给客户端
    echo $callback."(".$data.")";
?>
```
* **案例：调用360搜索的接口**
```html
<style>
        *{
            margin:0;
            padding: 0;
        }
        .box{
            width: 800px;
            margin: 150px auto;
        }
        .txt{
            width: 500px;
            font-size: 28px;
        }
        .btn{
            font-size: 28px;
            width: 200px;
        }
        .box ul{
            border: 1px solid #ccc;
            width:500px;
            border-top: none;
            list-style: none;
            display: none;
        }
        .box li{
            padding: 5px;
        }
        .box li:hover{
            background: lavenderblush;
            cursor: pointer;
        }
</style>
<div class="box">
    <input type="text" class="txt"><input type="button" value="智能搜索" class="btn">
    <ul>
    </ul>
</div>
<!--调用artTemplate模板-->
<script src="template-native.js"></script>
<script src="jquery-1.12.4.js"></script>
<script type="text/template" id="tmp">
    <%for(var i=0;i<result.length;i++){%>
        <li><%=result[i].word%></li>
    <%}%>
</script>
<script>
	//回调函数
    function getInfo(data) {
        var html = template("tmp",data);
        document.querySelector("ul").innerHTML = html;
    }
    var txt = document.querySelector(".txt");
    //按键弹起即发送请求
    txt.onkeyup = function () {
        var keywords = this.value;
        if(keywords.length>0){
            var script = document.createElement("script");
            script.src = "https://sug.so.360.cn/suggest?callback=getInfo&encodein=utf-8&encodeout=utf-8&format=json&fields=word,obdata&word="+keywords;
            document.body.appendChild(script);
            document.querySelector("ul").style.display = "block";
        }else{
            document.querySelector("ul").style.display = "none";
        }
    };
	//点击跳转事件
    $(".box ul").on("click","li",function () {
        var keyword = $(this).text();
        window.location.href = "https://www.so.com/s?ie=utf-8&shb=1&src=&q="+keyword;
    });
</script>
```
### CORS 跨域资源共享
* Cross-origin Resource Sharing
* 允许通过XMLHttpRequest发送跨域请求
* 同时支持get和post请求
* 需要在服务器端设置一个响应头
* `Access-Control-allow-Origin:http://www.jd.com,http://www.tmall.com`允许京东和天猫访问
* `Access-Control-allow-Origin:*;`允许任何站点访问