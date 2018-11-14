---
title : node-module
date : 2016-11-06
categories: node
---

# node 常用模块

## path 模块

| 方法                         | 作用                 | 备注   |
| -------------------------- | ------------------ | ---- |
| path.basename(path[, ext]) | 获取一个路径中的文件名部分      |      |
| path.dirname(path)         | 获取一个路径中的目录部分       |      |
| path.extname(path)         | 获取一个路径中的后缀名部分      |      |
| path.format(pathObject)    | 将一个路径对象格式化成一个路径字符串 |      |
| path.isAbsolute(path)      | 判断一个路径是否是绝对路径      |      |
| path.join([...paths])      | 将多个路径拼接为一个完整的路径    |      |
| path.normalize(path)       | 将一个非标准路径转为标准路径     |      |
| path.parse(path)           | 将一个字符串路径转为一个路径对象   |      |
| path.sep                   | 获取当前操作系统的路径分隔符     |      |

## fs 模块

| API                                      | 作用         | 备注                     |
| ---------------------------------------- | ---------- | ---------------------- |
| fs.readFile(file[, options], callback)   | 读取文件       |                        |
| fs.writeFile(file, data[, options], callback) | 写入文件       |                        |
| fs.unlink(path, callback)                | 删除文件       |                        |
| fs.readdir(path[, options], callback)    | 读取目录       |                        |
| fs.mkdir(path[, mode], callback)         | 创建目录       |                        |
| fs.rmdir(path, callback)                 | 删除目录       |                        |
| fs.rename(oldPath, newPath, callback)    | 重命名文件或目录   | 也可以移动文件或目录             |
| fs.stat(path, callback)                  | 获取文件或目录的状态 | 例如是否是文件或文件大小等信息        |
| fs.watch(filename[, options][, listener]) | 监视文件或目录    | 文件或目录变化会执行对应的 listener |
| fs.watchFile(filename[, options], listener) | 监视文件       |                        |
| fs.appendFile(file, data[, options], callback) | 向文件追加内容    |                        |
| fs.access(path[, mode], callback)        | 判断一个路径是否存在 |                        |
|                                          |            |                        |

## http 模块

### http 模块相关属性及方法

- http.METHODS
  + 获取 http 支持的请求方法
- http.STATUS_CODES
  + 获取 http 模块支持的状态码
- http.createClient([port][, host])
  + 创建客户端，使用 Node 发送请求
- http.createServer([requestListener])
  + 创建服务器，接收客户端请求
- http.get(options[, callback])
  + 发起 get 请求
- http.globalAgent
- http.request(options[, callback])
  + 发请求

### Class: http.Server 服务实例对象

事件：

- Event: 'checkContinue'
- Event: 'checkExpectation'
- Event: 'clientError'
- Event: 'close'
- Event: 'connect'
- Event: 'connection'
- Event: 'request'
  + 请求事件
- Event: 'upgrade'

方法：

- server.close([callback])
  + 关闭服务器
- server.listen(handle[, callback])
  + 绑定监听
- server.listen(path[, callback])
  + 绑定监听
- server.listen([port][, hostname][, backlog][, callback])
  + 绑定监听
- server.listening
- server.maxHeadersCount
- server.setTimeout(msecs, callback)
- server.timeout
### Class: http.ServerResponse 响应对象

事件：

- Event: 'close'
- Event: 'finish'

方法：

- response.addTrailers(headers)
- response.end([data][, encoding][, callback])
  + 结束响应
- response.finished
- response.getHeader(name)
- response.headersSent
- response.removeHeader(name)
- response.sendDate
- response.setHeader(name, value)
- response.setTimeout(msecs, callback)
- response.statusCode
  + 状态码
- response.statusMessage
  + 状态短语
- response.write(chunk[, encoding][, callback])
  + 写入响应流数据
- response.writeContinue()
- response.writeHead(statusCode[, statusMessage][, headers])
  + 写入响应头

### Request（http.IncomingMessage）请求对象

事件：

Event: 'aborted'
Event: 'close'

方法：

- message.destroy([error])
- message.headers
  + 获取请求头
- message.httpVersion
  + 获取 http 协议版本
- message.method
  + 获取请求方法
- message.rawHeaders
- message.rawTrailers
- message.setTimeout(msecs, callback)
- message.statusCode
- message.statusMessage
- message.socket
- message.trailers
- message.url
  + 获取请求路径

---