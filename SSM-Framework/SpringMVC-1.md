# Spring MVC



![spring-logo-9146a4d3298760c2e7e49595184e1975](SpringMVC-1.assets/spring-logo-9146a4d3298760c2e7e49595184e1975.svg)

[TOC]



# 1、前置知识

## 1、MVC简介

经典MVC模式中，M是指业务模型，V是指用户界面，C则是控制器。

- V-`View`	视图是指用户看到并与之交互的界面。
- M-`Model`模型是指模型表示业务规则，包括数据和行为。
- C-`Controller`控制器是指控制器接受用户的输入并调用模型和视图去完成用户的需求，控制器本身不输出任何东西和做任何处理，是一个调度员的作用。

使用MVC的目的是将M和V的实现代码分离，从而使同一个程序可以使用不同的表现形式。

MVC的主要作用是降低了视图与业务逻辑间的双向耦合。

MVC不是一种设计模式，而是一种架构模式，所以不同的MVC之间存在差异。

---

MVC框架需要做哪些事情?

- 将URL映射到Java类或者Java类的方法。
- 封装用户提交的数据。
- 处理请求-调用相关的业务处理-封装响应数据。
- 将响应的数据进行渲染.jsp/html.等表示层数据。



## 2、回顾Servlet

1. 环境搭建，父类导入依赖

   ```xml
   <dependencies>
       <dependency>
           <groupId>junit</groupId>
           <artifactId>junit</artifactId>
           <version>4.12</version>
       </dependency>
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-webmvc</artifactId>
           <version>5.3.6</version>
       </dependency>
       <dependency>
           <groupId>javax.servlet</groupId>
           <artifactId>servlet-api</artifactId>
           <version>2.5</version>
       </dependency>
       <dependency>
           <groupId>javax.servlet.jsp</groupId>
           <artifactId>jsp-api</artifactId>
           <version>2.2</version>
       </dependency>
       <dependency>
           <groupId>javax.servlet</groupId>
           <artifactId>jstl</artifactId>
           <version>1.2</version>
       </dependency>
   </dependencies>
   ```
   
2. 创建module，新建继承`HttpServlet`的类，实现`doGet`，`doPost`方法重写。

   ![image-20210427153930249](SpringMVC-1.assets/image-20210427153930249.png)

   ```java
   public class HelloServlet extends HttpServlet {
       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           //1. 获取前端参数
           String method = req.getParameter("method");
           if (method.equals("add")){
               req.getSession().setAttribute("msg","执行了add方法");
           }
           if (method.equals("delete")){
               req.getSession().setAttribute("msg","执行了delete方法");
           }
           //2.调用业务层
           //3.视图转发或重定向
           req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req,resp);
       }
   
       @Override
       protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           doGet(req, resp);
       }
   }
   ```

3. Web.xml中配置servlet接口

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
       <servlet>
           <servlet-name>hello</servlet-name>
           <servlet-class>com.zhang.servlet.HelloServlet</servlet-class>
       </servlet>
       <servlet-mapping>
           <servlet-name>hello</servlet-name>
           <url-pattern>/hello</url-pattern>
       </servlet-mapping>
       <!--配置Session超时时间-->
       <!--<session-config>
           <session-timeout>15</session-timeout>
       </session-config>-->
       <!--配置欢迎页-->
       <!--<welcome-file-list>
           <welcome-file>index.jsp</welcome-file>
       </welcome-file-list>-->
   </web-app>
   ```

4. 创建测试的jsp页面，配置Tomcat进行测试。

5. 访问http://localhost:8080/hello?method=add

   ![image-20210427155201932](SpringMVC-1.assets/image-20210427155201932.png)





# 2、 SpringMVC简介

​			The Spring Web model-view-controller (MVC) framework is designed around a `DispatcherServlet` that dispatches requests to handlers, with configurable handler mappings, view resolution, locale, time zone and theme resolution as well as support for uploading files. The default handler is based on the `@Controller` and `@RequestMapping` annotations, offering a wide range of flexible handling methods. With the introduction of Spring 3.0, the `@Controller` mechanism also allows you to create RESTful Web sites and applications, through the `@PathVariable` annotation and other features.

- Spring MVC属于SpringFrameWork的后续产品，已经融合在Spring Web Flow里面。
- SpringMVC是基于Java实现MVC的轻量级Web框架。

- Spring 框架提供了构建 Web 应用程序的全功能 MVC 模块。

---

SpringMVC的特点：

- SpringMVC的核心是`DispatcherServlet` ，他的作用就是将请求分发到不同的处理器。
- 可以采用基于注解的`Controller`声明方式。
- `DispatcherServlet`本质也是`Servlet`



![mvc](SpringMVC-1.assets/mvc.png)