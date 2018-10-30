---
title: ajax
date: 2016-04-23
categories: ajax
---

# AJAX

* 同步交互
  * 服务器返回的数据会覆盖原来的页面
* 异步交互
  * 不刷新页面加载数据
## JS中的ajax
* get方式请求
```javascript
//创建对象
var xhr = new XMLHttpRequest();
//1.提交方式
//2.要请求的资源地址,链接服务器
xhr.open("get","01.php?username=zhangsan");
//发送数据,参数可以为null，但是不能为空
xhr.send(null);
//监听服务器状态的改变
xhr.onreadystatechange = function(){
  //服务器会返回五个状态码：0,1,2,3,4；其中4代表响应完成
  if(xhr.readyState==4){
  	//判断响应是否成功
    if(xhr.status){
    	//接收服务器返回的数据
    	var data = xhr.responseText;
    	//对数据的处理自行定义
    	.....
    }
  }
}
```
* post方式请求
```javascript
var xhr = new XMLHttpRequest();
xhr.open("post","01.php");
//在发送数据之前需要设置一个请求头:【必须设置】
//必须设置在open()之后，send()之前
xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
xhr.send("username=lisi");
xhr.onreadystatechange = function(){
  if(xhr.readyState==4){
    if(xhr.status){
    	var data = xhr.responseText;
    	//对数据的处理自行定义
    	.....
    }
  }
}
```
* xhr.readyState：请求/响应过程的当前活动阶段
  * 0:未初始化。尚未调用open()方法
  * 1:启动。已经调用open()方法，但尚未调用send()方法
  * 2:发送。已经调用send()方法，但尚未接收到响应
  * 3:接收。已经接收到部分响应收据。
  * 4:完成。响应已经完成
* 响应的状态码
  * 200:响应成功
  * 302:请求重定向
  * 304:请求的资源没有发生改变、
  * 404:请求的资源没有找到
  * 405:请求的方式不支持
  * 500:服务器内部错误
## get请求和post请求的区别
* get: 请求的数据都在地址栏中，所以信息相对不安全，对请求的数据大小有限
* get:没有请求体，并且少一些请求头，性能比post较好
* post请求的数据在请求体中，信息相对安全，且对请求的数据大小没有限制
* post中有一个特殊的请求头，可以对请求数据中的中文进行编码
  * `Content-Type:"application/x-www-form-urlencoded"`
## jQuery中的ajax
* $.ajax()
* $.get()
* $.post()
* $.getJSON()
* $.getScript()
* load()  远程载入资源
* 序列化表单：serialize()、serializeArray()
  ** $.ajax()原理分析**
```javascript

var $ = {
	ajax:function(obj){
        //底层使用采用XMLHttpRequest 对象发送请求
        var xhr=new XMLHttpRequest();
        if(obj.beforeSend){
            var flag = obj.beforeSend();
            if (flag===false){
                return;
            }
        }
        if(typeof  obj.data==="object"){
            obj.data = this.params(obj.data);
        }
        /*判断用户是get 方式的请求，还是post 方式的请求.*/
        if(obj.type.toLocaleLowerCase()=="get"){
            obj.url=obj.url+"?"+obj.data;
            obj.data =null;
        }
        xhr.open(obj.type,obj.url);
        if(obj.type.toLocaleLowerCase()=="post"){
            xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
        }
        xhr.send(obj.data); //发送请求.
        //监听服务器端状态的改变
        //服务器有状态的时候调用.
        xhr.onreadystatechange=function(){
            if(xhr.readyState==4){
                if(xhr.status==200){
                    //服务器必须响应完成成功的时候才调用
                    var data=xhr.responseText;
                    var jsonObj = JSON.parse(data);
                    obj.success(jsonObj);
                }else{
                  	obj.error&&obj.error();
                }
                obj.complete&&obj.complete();
            }
        }
    },
    params:function (obj) {
        var str = "";
        for(var key in obj){
            str +=key+"="+obj[key]+"&";
        }
        str.substr(0,str.length-1);
        return str;
    }
}
```