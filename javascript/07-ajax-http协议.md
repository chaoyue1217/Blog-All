---
title: http协议
date: 2016-04-20
categories: http
---

# HTTP协议

* http协议是W3C制定的。用来约束客户端浏览器与web服务器进行通讯的数据格式
* http协议的数据格式分为两种，一种是请求，一种是响应

## 请求报文（客户端----->服务器）
* 由请求首行、请求头、请求空行、请求体组成
* 常见的请求有两种：get和post
### GET请求
* GET /day08/code/01http/01get.html HTTP/1.1
  * 请求首行：请求的方式  要访问的资源的地址  协议版本
* **以下部分全部为请求头 **
  * Host127.0.0.1
* 请求的主机名 允许多个域名同处一个IP地址，即虚拟主机
  * Cache-Controlmax-age=0
  * 这个做缓存控制的.
  * Upgrade-Insecure-Requests1
* User-Agent    Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36
  * 客户端浏览器的版本，操作系统的版本信息
* Accept     text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
  * 接收，告诉服务器，客户端可以接收的数据格式.
  * Refererhttp://127.0.0.1/day08/code/01http/Accept-Encodinggzip, deflate, sdch
  * 客户端接收的压缩格式
  * Accept-Languagezh-CN,zh;q=0.8
  * 客户端接收的语言
  * If-None-Match"7700000000217a-96-548afe60e947a"   If-Modified-SinceFri, 17 Feb 2017 01:33:19 GMT

### POST请求
* POST  /day08/code/01http/02post.php HTTP/1.1
  * 请求首行:请求的方式  要访问的资源的地址
* **以下为请求头**
* Host: 127.0.0.1
  * 请求的主机名
* Content-Length: 21
  * 内容长度，指的 请求体的长度
* Cache-Control: max-age=0
  * 缓存的
* Origin: http://127.0.0.1
  * 只存在于post请求	
  * 浏览器发现这次跨源AJAX请求是简单请求，就自动在头信息之中，添加一个Origin字段。
  * Origin字段用来说明，本次请求来自哪个源（协议 + 域名 + 端口）。服务器根据响应头Access-Control-Allow-Origin中有没有该字段，决定是否同意这次请求。
* Upgrade-Insecure-Requests: 1
* User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36
  * 客户端浏览器的版本，操作系统的版本信息
* Content-Type: application/x-www-form-urlencoded
  * 这个是post 提交独有第一个请求头，如果发送到服务器的数据有中文，会把中文进行编码
  * **在使用ajax发送post请求时，需要设置该请求头。设置为application/x-www-form-urlencoded时，要以key=value&key=value的格式发送数据；设置为application/json时，要以json的格式发送数据**
  * 在发送给服务器，服务器得到数据会自动解码.
* Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
  * 接收的数据格式
* Referer: http://127.0.0.1/day08/code/01http/02post.htm
  * 告诉服务器，请求来自于那个页面，服务器借此可以获得一些信息用于处理
  * 比如从我主页上链接到一个朋友那里，他的服务器就能够从HTTP Referer中统计出每天有多少用户点击我主页上的链接访问他的网站
* Accept-Encoding: gzip, deflate
  * 客户端接收压缩格式
* Accept-Language: zh-CN,zh;q=0.8
  * 客户端接收的语言
* ​
  * 请求空行 【用来将请求头跟请求体进行一个区分，方便服务器解析数据】
* username=dasfadsfdafs
  * 请求体【客户端发送到服务器的数据.】

## 响应报文(服务器---->客户端)
* 由响应首行、响应头、响应空行、响应体组成

* HTTP/1.1 200 OK
   * 请求首行：协议版本  状态码
   * **以下为响应头**
* Date: Fri, 17 Feb 2017 01:35:58 GMT
   * 响应头 告诉客户端服务器端的时间.
* Server: Apache/2.2.21 (Win32) PHP/5.3.10
   * 告诉客户端服务器的版本
* Last-Modified: Fri, 17 Feb 2017 01:33:19 GMT
   * 文件的最后修改时间
* If-Modified-Since:Fri, 17 Feb 2017 01:33:19 GMT
   * 如果两次请求之间If-Modified-Since和 Last-Modified的值相同，服务器就会给客户端一个304的状态码，代表请求的资源没有发生改变，会从本地缓存中获取数据
* ETag: "7700000000217a-96-548afe60e947a"
* Accept-Ranges: bytes
* Content-Length: 150
   * 内容的长度
* Content-Type: text/html;
   * 内容类型，告诉客户端浏览器，我给你返回的是一个文本，html 格式的文本,
   * 客户端浏览器得到这个，就会以html 的方式去解析.
* header("Content-Type:text/html;charset=utf-8")
   * 响应空行
```html 
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Title</title>
  </head>
  <body>
       中文
  </body>
  </html>
```
*  响应体

## GET和POST请求的区别
* 1、get请求的参数写在地址栏中，信息不安全，并且对数据的大小有限制
* 2、get请求没有请求体
* 3、get请求报文中信息较少，相对于post请求性能较高
* 4、post请求的参数在请求体中，相对安全
* 5、post请求对数据的大小没有限制
* 6、post 请求有一个特殊的请求头: Content-Type:application/x-www-form-urlencoded 可以对请求的数据包含中文的数据进行编码.

## Origin和Referer的区别
* origin中只包含请求的主机名
* referer包含请求的URL路径和请求内容
* origin比referer对信息的保密性强
* origin只存在于post请求中，referer可以存在于所有的请求类型中

