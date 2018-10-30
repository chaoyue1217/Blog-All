---
title: JSP基础
date: 2017-10-11
categories: JSP
---

# 什么是JSP

JSP全称是Java Server Pages,其根本是一个简化的Servlet设计，它实现了在Java中使用HTML标签。JSP是一种动态网页技术标准也是JAVAEE的标准，JSP与Servlet一样，是在服务器端执行的。

优点： java平台，安全性高，适合开发大型的，企业级的web应用程序。

# JSP页面元素构成
静态内容、指令、表达式、小脚本、声明、注释

## 指令

**page：** 通常位于jsp页面的顶端，同一页面可以拥有多个page指令
语法：`<%@ page 属性="属性值" ... 属性="属性值" %>``
常用属性：

| 属性          |          描述          |                          默认值 |
| ----------- | :------------------: | ---------------------------: |
| language    |    指定JSP页面使用的脚本语言    |                         java |
| import      | 通过该属性来引用脚本语言中使用到的类文件 |                            无 |
| contentType |   指定JSP页面所采用的编码方式    | text/html,charset=ISO-8859-1 |

**include：** 将一个外部文件嵌入到当前JSP文件，同时解析这个页面中的JSP语句。
**taglib：** 使用标签库定义新的标签，在JSP页面中启用定制行为。 

## 注释

**HTML的注释**
`<!--html注释-->`   客户端可见

**JSP注释**
`<%--html注释--%>`   客户端不可见

**JSP脚本注释**
`//`  单行注释
`/**/` 多行注释

## JSP脚本

在jsp页面中执行的java代码：<% java代码 %>

```java
<%@ page language="java" import="java.util.*" contentType="text/html,charset=utf-8" %>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My JSP</title>
</head>
<body>
    <h1>hello world!</h1>
    <hr>
    <% 
        out.print(new Date());
    %>
</body>
```

## 声明
在jsp页面中定义变量或者方法。
语法：<%! java代码 %>

## 表达式
在jsp页面中执行的表达式。
语法：<%= 表达式 %>  表达式不以分号结束

```html
<%@ page language="java" import="java.util.*" contentType="text/html,charset=UTF-8" %>
<% 
    //加入这两句是解决页面上中文显示问号的问题
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
    <h1>hello world!</h1>
    <hr>
    <%! 
        String s = "张三";//声明了一个字符串变量
        int add(int x,int y)//声明了一个函数
        {
            return x + y;
        }
    %>
    <% 
        out.print("abc");
    %>
    <br>
    你好，<%= s %><br>
    x+y = <%=add(10,5) %>
</body>

```

## 生命周期

```flow
st=>start: 用户发出请求index.jsp
e=>end: 解析执行jspService()
cond=>condition: 是否是第一次请求?
op1=>operation: JSP引擎把该jsp文件转换成为一个Servlet，生成字节码文件，并执行jspInit()
op2=>operation: 生成字节码文件
st->cond
cond(yes,right)->op1->op2->e
cond(no)->op2->e
```

- 当用户第一次请求一个jsp页面时，首先被执行的方法是构造方法；
  就是转换为class文件后，他会创建一个Servlet的实例，然后调用Servlet的构造方法，再调用jspInit()方

  1）JSP引擎先把该JSP文件转换成一个Java源文件（Servlet），在转换时如果发现JSP文件有任何语法错误，转换过程将中断，并向服务端和客户端输出出错信息。
  2）如果转换成功，JSP引擎用javac把该Java源文件编译成相应的class文件。
  3）创建一个该Servlet（JSP页面的转换结果）的实例，该Servlet的jspInit()方法被执行，jspInit()方法在Servlet的生命周期中只被执行一次。
  部署启动Tomcat之后在work目录中是不存在该项目的字节码文件，只有在访问之后才会生成；且在修改jsp文件后重新部署启动访问后，该字节码文件也会重新更新。

- jspService()方法被调用来处理客户端的请求。对每一个请求，JSP引擎创建一个新的线程来处理该请求。如果有多个客户端同时请求该JSP文件，则JSP引擎会创建多个线程。每个客户端请求对应一个线程。以多线程方式执行可以大大降低对系统的资源需求，提高系统的并发量及响应时间。但也要注意多线程带来的同步问题，由于该Servlet始终驻于内存，所以响应是非常快的。