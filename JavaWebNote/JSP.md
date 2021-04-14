# JSP

> 什么是JSP？
>
> ​		Java Server Page : Java服务器端页面，也和Servlet一样，用于动态Web技术。

[TOC]

## 1. JSP特点

- 写JSP就像写HTML
- 与HTML区别
  - HTML只能给用户提供静态数据
  - JSP页面中可以嵌入JAVA代码，为用户提供动态数据
- 同样缺点就是不能前后端分离开发



## 2. JSP原理

JSP是如何执行的?

​		服务器内部会创建一个工作目录，生成将JSP代码生成对应的JAVA代码

- Tomcat目录在

  `D:\apache-tomcat-9.0.45-windows-x64\apache-tomcat-9.0.45\work\Catalina\localhost`

- IDEA目录在

  `C:\Users\Admin\AppData\Local\JetBrains\IntelliJIdea2020.1\tomcat\Unnamed_EmptyServlet\work\Catalina\localhost\Request_war\org\apache\jsp`

**浏览器向服务器发送请求，不管访问什么资源，其实都在访问Servlet！**

JSP最终也会被转换成一个Java类！

```java
public final class index_jsp extends HttpJspBase implements JspSourceDependent, JspSourceImports
```

JSP本质上是一个Java类

```java
//初始化
public void _jspInit() 
//销毁
public void _jspDestroy()
//服务JSPService
public void _jspService(HttpServletRequest request, HttpServletResponse response) throws IOException, ServletException
```

JSP生成的类的主要作用:

1. 判断请求

2. 内置一些对象

   ```java
   final javax.servlet.jsp.PageContext pageContext;//页面上下文
   javax.servlet.http.HttpSession session = null;	//session
   final javax.servlet.ServletContext application;	//application
   final javax.servlet.ServletConfig config;		//config
   javax.servlet.jsp.JspWriter out = null;			//out
   final java.lang.Object page = this;				//page当前
   javax.servlet.jsp.JspWriter _jspx_out = null;	
   javax.servlet.jsp.PageContext _jspx_page_context = null;
   HttpServletRequest request						//请求
   HttpServletResponse response					//响应
   ```

3. 输出页面前，增加的代码

   ```java
   response.setContentType("text/html;charset=UTF-8");
   pageContext = _jspxFactory.getPageContext(this, request, response,
            null, true, 8192, true);
   _jspx_page_context = pageContext;
   application = pageContext.getServletContext();
   config = pageContext.getServletConfig();
   session = pageContext.getSession();
   out = pageContext.getOut();
   _jspx_out = out;
   ```

4. 以上的这些对象中，可以在jsp页面中直接使用。

5. jsp页面中的Java代码会原封不动的编译到Jsp.class中

6. html代码输出为下面这种形式

   ```java
   out.write("<h1>Login</h1>\n");
   ```



## 3. JSP基本语法

