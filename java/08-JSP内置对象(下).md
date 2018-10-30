---
title: JSP内置对象(下)
date: 2017-10-21
categories: JSP
---

# session对象
- session对象是一个JSP内置对象
- session对象在第一个JSP页面被装载时自动创建，完成会话期管理。
- 从一个客户打开浏览器并连接到服务器开始，到客户关闭浏览器离开这个服务器结束，被称为一个会话。
- 当一个客户访问一个服务器时，可能会在服务器的几个页面之间切换，服务器应当通过某种办法知道这是一个客户，就需呀session对象。
- session对象时HttpSession类的实例。
常用方法：
- long getCreationTime():返回session的创建时间
- String getId：返回session创建时JSP引擎为他设的唯一ID
- Object setAttribute(String name,Object value)：使用指定名称将对象绑定到此会话
- Object getAttribute(String name): 返回与此会话中的指定名称绑在一起的对象，如果没有对象，则返回null
- String getValueNames(): 返回一个包含此session中所有可用属性的数组
- int getMaxInactiveInterval(): 两次请求间隔多长时间此session被取消(单位为秒)
- setMaxInactiveInterva()：设置session的失效时间

sessionPage1:

```java
<%@page import="java.text.SimpleDateFormat"%>
<%@ page language="java" import="java.util.*,java.text.*" contentType="text/html,charset=UTF-8" %>
<% 
response.setContentType("text/html;charset = UTF-8"); 
request.setCharacterEncoding("UTF-8"); 
%>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>My JSP</title>
</head>
<body>
  <h1>session内置对象</h1>
  <%
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd HH:mm:ss");
    Date d = new Date(session.getCreationTime());
    session.setAttribute("username", "admin");
    session.setAttribute("password", "123456");
    session.setAttribute("age", "20");
  %>
  session创建时间：<%=sdf.format(d) %><br>
  session的ID号：<%=session.getId() %><br>
  session的属性：<%= session.getAttribute("username") %><br>
  <a href="sessionPage2.jsp" target="_blank">跳转到session2.jsp</a>
</body>
```

sessionPage2:

```java
<%@ page language="java" import="java.util.*" contentType="text/html,charset=UTF-8" %>
<% 
response.setContentType("text/html;charset = UTF-8"); 
request.setCharacterEncoding("UTF-8"); 
%>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>My JSP</title>
</head>
<body>
  用户名：<%=session.getAttribute("username") %><br>
  sessionId: <%=session.getId() %>
  session中保存的属性有: <%
    String names[] = session.getValueNames();
    for(int i=0;i<names.length;i++){
      out.println(names[i] + "&nbsp;&nbsp;&nbsp;");
    }
  %>
</body>

```
在不关闭浏览器的情况下，由于两个页面是同一个服务器的，所以sessionID是一样的，即session是相同的，为同一个会话。

## session的生命周期

- 创建：当客户端第一次访问某个jsp或者servlet时，服务器会为当前会话创建一个SessionId，每次客户端向服务端发送请求时，都会将此sessionId携带过去，服务端会对此sessionId进行校验。
- 活动：
（1）某次会话中通过超链接打开的页面属于同一次会话。
（2）只要当前会话页面没有全部关闭，重新打开新的浏览器窗口访问同一项目资源时属于同一次会话。
（3）只有本次会话的所有页面都关闭后再重新访问某个JSP或者servlet才会创建新的会话。
**创建新的会话后，旧的sessionID仍然存在于服务器，只不过再也没有客户端会携带他然后交与服务端校验。除非旧的session已经超时**
- 销毁：3种方式
（1）调用session.invalidate()方法
（2）设置session过期时间（Tomcat的默认session过期时间是30分钟）
（3）服务器重启

设置session过期时间的方式：
- session.setMaxInactiveInterval(时间);单位是秒
- 在web.xml中配置

```xml
<session-config>
  <session-timeout>
    10<!-- 单位是分钟 -->
  </session-timeout>
</session-config>

```

# application对象

- application对象实现了用户数据的共享，可存放全局变量。
- application开始于服务器的启动，终止于服务器的关闭。
- 在用户的前后连接或不同用户之间的连接中，可以对application对象的同一属性进行操作。
- 在任何地方对application对象属性的操作，都将影响其他用户对此的访问。
- 服务器启动和关闭决定了application对象的生命。
- application对象时ServletContext类的实例。

常用方法：
- void setAttribute();
- Object getAttribute();
- Enumeration getAttributeNames() 返回所有可用属性名的枚举
- String getServerInfo() 返回JSP引擎名及版本号

# page对象

page对象就是指向当前JSP页面本身，有点像类中的this，它是java.lang.Object类的实例。
常用方法：
- class getClass()
- int hashCode();
- boolean equals()
- ...共享Object的方法

# pageContext对象
- pageContext对象提供了对页面内所有的对象及，名字空间的访问
- pageContext对象可以访问到本页所在的session，也可以取本页所在的application的某一属性值。
- pageContext对象相当于页面中所有功能的集合。
- pageContext对象的本类名也叫pageContext。

常用方法：
- JSPWriter getOut() 返回当前客户端响应被使用的JSPWriter流(out)
- HttpSession getSession() 返回当前页中的HttpSession对象(session)
- Object getPage() 返回当前页的Object对象(page)
- getRequest() 返回当前页的request对象
- getResponse() 返回当前页的response对象
- setAttribute()
- getAttribute()
- int getAttributeScope(String name) 返回某属性的作用范围
- void forward()  使当前页面重导到另一页面，地址栏不会变化
- void include()  在当前位置包含另一文件

# config对象
 config对象时在一个Servlet初始化时，JSP引擎向它传递信息用的，此信息包括Servlet初始化时所要用到的参数(通过属性名和属性值构成)以及服务器的有关信息(通过传递一个ServletContext对象)

 常用方法：
 - ServletContext getServletContext()  返回含有服务器相关信息的ServletContext对象
 - String getInitParameter(String name) 返回初始化参数的值
 - Enumeration getInitParameterNames() 返回Servlet初始化所需所有参数的枚举

# exception对象
exception对象是一个异常对象，当一个页面在运行过程中发生了异常，就产生了这个对象。如果一个JSP页面要应用此对象，就必须把isErrotPage设为true，否则无法编译。它实际上是java.lang.Throwable的对象。

常用方法：
- String getMessage() 返回描述异常的信息 
- String toString() 返回关于异常的简短描述信息
- void printStackTrace() 显示异常及其栈轨迹
- Throwable FillInStackTrace() 重写异常的执行栈轨迹

需要在出现异常的页面上配置errorPage属性，值为处理该异常的jsp页面
