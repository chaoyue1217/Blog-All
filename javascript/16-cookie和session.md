---
title: cookie
date: 2016-05-09
categories: cookie和session
---

# cookie和session
* cookie是浏览器进行状态保持的一种技术
* http是无状态的，无法记录客户端和服务器端的通信状态
* url重写可以解决用户禁用cookie后，依然保持客户端与服务器端的正常通信状态。
* 由于 Http 协议的请求过程，是基于TCP/IP的，当客户端请求服务器，服务器处理后，进行响应，这个过程是无状态的。在有些时候，是需要保存一些客户端的请求信息，识别客户端的某些状态，智能的、有针对性的去分析某些客户端的习惯。这些时候，就需要去记录客户端的连接状态，识别请求的状态等。
* cookie可以在客户端存储数据
* 在子路径设置的cookie在父路径下是获取不到的，反之则可；
* 不可以跨域获取cookie
## 操作cookie
* document.cookie   可以访问cookie
* cookie的格式是键值对
* 多个cookie之间使用分号和空格隔开 ，如 `username=zhangsan; age=12`
### cookie分类
* session cookie   会话cookie  关闭浏览器窗口，cookie就消失
* presistant cookie   持久cookie  需要设置过期时间
```javascript
var date = new Date();
date.setDate(date.getDate()+5);
//设置为从当前时间起五天后过期
document.cookie = "age=12;expires="+date.toGMTString();
document.cookie="username=zhangsan";//设置多个cookie，cookie设置是累加的
```
** 封装cookie**
```javascript
    //设置cookie
	function setCookie(key,value,expires) {
        document.cookie = key + "=" + value + "; " +"expires="+expires + 	"; path=/";   //   path=/  表示在当前的根目录有效
    }
    //获取cookie
    function getCookie(cname) {
        var cookies = document.cookie;
        var arr = cookies.split("; ");
        for(var i=0;i<arr.length;i++){
            var kv = arr[i].split("=");
            if(cname ==kv[0]){
                return kv[1];
            }
        }
    }
```
### URL重写
* 该工作在服务器端进行
* 解决用户禁用cookie的情况下依然可以保存客户端和服务器的通信状态
* URL 重写是拦截客户端传入 Web 请求URL并自动将其定向到到规则指定的 URL 的过程。比如浏览器发来请求 `http://blog.mocoder.com/hello.html `，服务器自动将这个请求中定向为`http://blog.mocoder.com/test.do?method=hello`。
* ** jquery 插件操作cookie**   使用jquery.cookie.js
## session
* 当程序需要为某个客户端的请求创建一个session时，服务器首先检查这个客户端的请求里是否已包含了一个session标识（称为session id），如果已包含则说明以前已经为此客户端创建过session，服务器就按照session id把这个session检索出来使用（检索不到，会新建一个），如果客户端请求不包含session id，则为此客户端创建一个session并且生成一个与此session相关联的session id，session id的值应该是一个既不会重复，又不容易被找到规律的字符串，这个session id将被在本次响应中返回给客户端保存,交给cookie保存，这样在交互过程中浏览器可以自动的按照规则把这个标识发送给服务器。一般这个cookie的名字都是类似于SESSIONID。
## cookie 和 session的区别
* cookie数据存放在客户端，session数据存放在服务器上
* cookie数据不安全,可以被别人获取，session则相对安全。
* session数据会在一定的时间内保存在服务器上，默认是30min。当访问量增多的时候，会比较消耗服务器的性能
* 单个cookie保存的数据不能超过4K
## 前端可以发送请求方式
* 1、地址栏直接输入url
* 2、表单的action属性
* 3、a标签href
* 4、img的src
* 5、script的src
* 6、link的href
* 7、iframe 的src
* 8、ajax
* 9、postMessage  H5新的API
* 10、flash插件
* 11、location.href
