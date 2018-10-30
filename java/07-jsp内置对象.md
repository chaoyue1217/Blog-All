---
title: JSP内置对象(上)
date: 2017-10-16
categories: JSP
---

JSP内置对象是web容器创建的一组对象，不适用new关键字就可以使用。

```java
<%
int[] value = {6,7,8};
for(int i:value){
    out.println(i);//out为内置对象
}
%>
```

**九大内置对象**
常用：out、request、response、session、application
其他：page、pageContext、exception、config

**缓冲区**
内存中的一块区域，用来保存临时数据。

# out对象
out对象时JspWriter类的实例，是向客户端输出内容的常用对象。
常用方法：
- void println()  向客户端打印字符串
- void clear() 清除缓冲区的内容，如果在flush之后调用会抛出异常。
- void clearBuffer()  清除缓冲区的内容，如果在flush之后调用不会抛出异常
- void flush() 将缓冲区内容输出到客户端
- int getBufferSize() 返回缓冲区字节数的大小，不设置则为0
- int getRemaining() 返回缓冲区剩余大小
- boolean isAutoFlush() 缓冲区满时，自动清空还是抛出异常
- void close()  关闭输出流

```java
<%@ page language="java" import="java.util.*" contentType="text/html; charset=utf-8"%>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <base href="<%=basePath%>">
    
    <title>My JSP 'index.jsp' starting page</title>
    <meta http-equiv="pragma" content="no-cache">
    <meta http-equiv="cache-control" content="no-cache">
    <meta http-equiv="expires" content="0">    
    <meta http-equiv="keywords" content="keyword1,keyword2,keyword3">
    <meta http-equiv="description" content="This is my page">
    <!--
    <link rel="stylesheet" type="text/css" href="styles.css">
    -->
  </head>
  
  <body>
    <h1>out内置对象</h1>
    <% 
       out.println("<h2>静夜思</h2>");
       out.println("床前明月光<br>");
       out.println("疑是地上霜<br>");
       out.flush();
       //out.clear();//这里会抛出异常。
       out.clearBuffer();//这里不会抛出异常。
       out.println("举头望明月<br>");
       out.println("低头思故乡<br>");
    
    %>
        缓冲区大小：<%=out.getBufferSize() %>byte<br>
        缓冲区剩余大小：<%=out.getRemaining() %>byte<br>
       是否自动清空缓冲区：<%=out.isAutoFlush() %><BR>    
  </body>
</html>
```


# request对象

客户端的请求信息被封装在request对象中，通过它才能了解到客户的需求，然后做出响应。它是HttpServletRequest类的实例。request对象具有请求域，即完成客户端的请求之前，该对象一直有效。

常用方法：
- String getParameter(String name)返回name指定参数的参数值
- String[] getParameterValues(String name)返回包含参数name的所有值的数组
- void setAttribute(String,Object)存储此请求中的属性
- Object getAttribute(String namae) 返回指定属性的属性值
- String getContentType() 得到请求体的MIME类型
- String getProtocol() 返回请求用的协议类型及版本号
- String getServerName() 返回接受请求的服务器主机号
- int getServerPort() 返回服务器接收此请求所用的端口号
- String getCharacterEncoding() 返回字符编码方式
- void setChatacterEncoding() 设置请求的字符编码方式
`request.setChatacterEncoding("utf-8")`:解决中文乱码问题，但是无法解决url的中文参数问题
- int getContentLength() 返回请求体的长度
- String getRemoteAddr() 返回发送请求的客户端IP地址
- String getRealPath("请求的页面") 返回请求的真实路径
- String getContextPath() 返回上下文路径
**也可以接受url的传参**

发送页面：

```java
<%@ page language="java" import="java.util.*" contentType="text/html; charset=utf-8"%>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <base href="<%=basePath%>">
    
    <title>My JSP 'index.jsp' starting page</title>
  <meta http-equiv="pragma" content="no-cache">
  <meta http-equiv="cache-control" content="no-cache">
  <meta http-equiv="expires" content="0">    
  <meta http-equiv="keywords" content="keyword1,keyword2,keyword3">
  <meta http-equiv="description" content="This is my page">
  <!--
  <link rel="stylesheet" type="text/css" href="styles.css">
  -->
  </head>
  
  <body>
    <h1>用户注册</h1>
    <hr>
    <% 
       int number=-1;
       //说明用户第一次访问页面，计数器对象还未创建
       if(application.getAttribute("counter")==null)
       {
           application.setAttribute("counter", 0);
       }
       number = Integer.parseInt(application.getAttribute("counter").toString());
       number++;
       application.setAttribute("counter", number);
    %>
    <form name="regForm" action="request.jsp" method="post">
    <table>
      <tr>
        <td>用户名：</td>
        <td><input type="text" name="username"/></td>
      </tr>
      <tr>
        <td>爱好：</td>
        <td>
           <input type="checkbox" name="favorite" value="read">读书
           <input type="checkbox" name="favorite" value="music">音乐
           <input type="checkbox" name="favorite" value="movie">电影
           <input type="checkbox" name="favorite" value="internet">上网
        </td>
      </tr>
      <tr>
         <td colspan="2"><input type="submit" value="提交"/></td>
      </tr>
    </table>
    </form>
    <br>
    <br>
    <a href="request.jsp?username=李四">测试URL传参数</a>
  </body>
</html>

```

接收页面：

```java
<%@ page language="java" import="java.util.*" contentType="text/html; charset=utf-8"%>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <base href="<%=basePath%>">
    
    <title>My JSP 'index.jsp' starting page</title>
    <meta http-equiv="pragma" content="no-cache">
    <meta http-equiv="cache-control" content="no-cache">
    <meta http-equiv="expires" content="0">    
    <meta http-equiv="keywords" content="keyword1,keyword2,keyword3">
    <meta http-equiv="description" content="This is my page">
    <!--
    <link rel="stylesheet" type="text/css" href="styles.css">
    -->
  </head>
  
  <body>
    <h1>request内置对象</h1>
    <% 
       request.setCharacterEncoding("utf-8"); //解决中文乱码问题，无法解决URL传递中文出现的乱码问题。
       request.setAttribute("password", "123456");
    
    %>
        用户名：<%=request.getParameter("username") %><br>   
        爱好 ：
        <% 
           if(request.getParameterValues("favorite")!=null)
           {
               String[] favorites = request.getParameterValues("favorite");
               for(int i=0;i<favorites.length;i++)
               {
                  out.println(favorites[i]+"&nbsp;&nbsp;");
               }
            }
        %>
         <br>
         密码：<%=request.getAttribute("password") %><br> 
         请求体的MIME类型:<%=request.getContentType() %><br>
         协议类型及版本号:  <%=request.getProtocol() %><br>
         服务器主机名 :<%=request.getServerName() %><br>
         服务器端口号：<%=request.getServerPort() %><BR>
         请求文件的长度 ：<%=request.getContentLength() %><BR>
         请求客户端的IP地址：<%=request.getRemoteAddr() %><BR>
         请求的真实路径：<%=request.getRealPath("request.jsp") %><br>
         请求的上下文路径：<%=request.getContextPath() %><BR>
  </body>
</html>
```

# response对象
response对象包含了响应客户请求的有关信息，但在JSP中很少直接用到，它是HttpServletRequest类的实例。response对象具有请求域,即访问一个页面时，该页面内的response对象只能对这次访问有效，其他页面的response对象对当前页面无效。
常用方法：
String getCharacterEncoding() 返回响应用的是何种编码
void setContentType() 设置响应的MIME类型
PrintWriter getWriter() 返回一个可以向客户端输出字符的一个对象
sendRedirect(java.lang.String location) 对客户端的请求重定向

```java
<%@ page language="java" import="java.util.*,java.io.*" contentType="text/html; charset=utf-8"%>
<%
  response.setContentType("text/html;charset=utf-8");
  out.println("<h1>response内置对象</h1>");
  out.println("<hr>");

  out.flush();//这句可以保证先输出out的内容

  PrintWriter outer = response.getWriter();//获得输出流对象
  /* outer对象输出的内容总是提前于内置out对象 */
  outer.println("这是response对象生成的输出流outer对象");

  response.sendRedirect("reg.jsp");
%>
```

# 请求转发与请求重定向

请求重定向：客户端行为，response.sendRedirect(),本质上等同于两次请求，前一次的请求对象不会保存，地址栏的URL地址会发生改变。

请求转发：服务器行为，request.getRequestDispatcher().forward(req,res);是一次请求，转发后请求对象会保存，地址栏的URL地址不会改变。请求转发是在同一个request对象里完成的。

