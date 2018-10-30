---
title: webSocket
date: 2016-5-10
categories: HTML5
---
# WebSocket
- WebSocket是HTML5开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。
- 在WebSocket API中，浏览器和服务器只需要一次握手的动作，然后浏览器和服务器之间就形成了一条快速通道。两者之间就可以数据相互传送。
- 浏览器通过 JavaScript 向服务器发出建立 WebSocket 连接的请求，连接建立以后，客户端和服务器端就可以通过 TCP 连接直接交换数据。
- 当你获取 Web Socket 连接后，你可以通过send()方法来向服务器发送数据，并通过 onmonmessage事件来接收服务器返回的数据。
- 创建WebSocket对象
```js
//第一个参数 url, 指定连接的 URL。
//第二个参数 protocol 是可选的，指定了可接受的子协议。
var Socket = new WebSocket(url,[protocol]);
```

## WebSocket起源
- 在Web应用越来越复杂的今天，消息推送（即时通讯）已经成为一个非常重要的功能了，有了消息推送，Web页面就能够及时的接收到来自服务端的消息，为用户展现最好的交互体验。过去我们在实现Web页面的消息推送时，一般都是由页面发起请求，以轮询的方式向服务器获取数据，实现起来极为不便，业务逻辑也相对较为复杂。
- 为了解决这个问题，进而实现真正意义上的消息推送，HTML5定义了一套新的规范，这就是WebSocket。
- WebSocket是一种用于在服务器和客户端之间实现高效的双向通信的机制，通过WebSocket，能够实现在一个HTTP连接上自由地双向收发消息。

## WebSocket属性
- `Socket.readyState`:只读属性，表示连接状态
    + 0：表示连接尚未建立
    + 1：表示连接已建立，可以直接进行通信
    + 2：表示连接正在进行关闭
    + 3：表示连接已经关闭或者连接不能打开
- `Socket.bufferedAmount`:只读属性`bufferedAmount`已被send()放入正在队列中等待传输，但是还没有发出的 UTF-8 文本字节数。

## WebSocket事件
- `Socket.onopen`：连接建立时触发 
- `Socket.onmessage`：客户端接收服务器数据时触发
- `Socket.onerror`: 通信错误时触发
- `Socket.onclose`: 连接关闭时触发

## WebSocket方法
- `Socket.send()`: 使用连接发送数据
- `Socket.close()`: 关闭连接

## WebSocket使用
- WebSocket 协议本质上是一个基于 TCP 的协议。
- 为了建立一个 WebSocket 连接，客户端浏览器首先要向服务器发起一个 HTTP 请求，这个请求和通常的HTTP请求不同，包含了一些附加头信息，其中附加头信息"Upgrade: WebSocket"表明这是一个申请协议升级的 HTTP 请求，服务器端解析这些附加的头信息然后产生应答信息返回给客户端，客户端和服务器端的 WebSocket 连接就建立起来了，双方就可以通过这个连接通道自由的传递信息，并且这个连接会持续存在直到客户端或者服务器端的某一方主动的关闭连接。
## 客户端的HTML和javascript
```html
<!DOCTYPE HTML>
<html>
   <head>
   <meta charset="utf-8">
   <title>webSocket</title>
      <script type="text/javascript">
         function WebSocketTest()
         {
            if ("WebSocket" in window)
            {
               alert("您的浏览器支持 WebSocket!");
               
               // 打开一个 web socket
               var ws = new WebSocket("ws://localhost:9998/echo");
                
               ws.onopen = function()
               {
                  // Web Socket 已连接上，使用 send() 方法发送数据
                  ws.send("发送数据");
                  alert("数据发送中...");
               };
                
               ws.onmessage = function (evt) 
               { 
                  var received_msg = evt.data;
                  alert("数据已接收...");
               };
                
               ws.onclose = function()
               { 
                  // 关闭 websocket
                  alert("连接已关闭..."); 
               };
            }
            
            else
            {
               // 浏览器不支持 WebSocket
               alert("您的浏览器不支持 WebSocket!");
            }
         }
      </script>
        
   </head>
   <body>
      <div id="sse">
         <a href="javascript:WebSocketTest()">运行 WebSocket</a>
      </div>
   </body>
</html>
```

## 安装pywebsocket
- 在执行以上程序前，我们需要创建一个支持 WebSocket 的服务。从 pywebsocket 下载 mod_pywebsocket ,或者使用 git 命令下载：
```bash
git clone https://github.com/google/pywebsocket.git
```
- mod_pywebsocket 需要 python 环境支持
- mod_pywebsocket 是一个 Apache HTTP 的 Web Socket扩展，安装步骤如下：
    + 解压下载的文件。
    + 进入 pywebsocket 目录。
    + 执行命令：
```bash
$ python setup.py build
$ sudo python setup.py install
```

### 开启服务
在 pywebsocket/mod_pywebsocket 目录下执行以下命令：
```bash
$ sudo python standalone.py -p 9998 -w ../example/
```
以上命令会开启一个端口号为 9998 的服务，使用 -w 来设置处理程序 echo_wsh.py 所在的目录。
现在我们可以在Chrome浏览器打开前面创建的runoob_websocket.html文件。如果你的浏览器支持 WebSocket(), 点击"运行 WebSocket"，你就可以看到整个流程各个步骤弹出的窗口，
