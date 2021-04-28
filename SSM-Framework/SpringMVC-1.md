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



## 1、创建第一个SpringMVC程序

1. 整个项目的目录如下

   ![image-20210427211748836](SpringMVC-1.assets/image-20210427211748836.png)

2. 配置`web\WEB-INF\web.xml`

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
       <servlet>
           <servlet-name>springmvc</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:springmvc-servlet.xml</param-value>
           </init-param>
           <!--启动级别1-->
           <load-on-startup>1</load-on-startup>
       </servlet>
       <!--/ 匹配所有请求:不包括jsp-->
       <!--/* 匹配所有请求:包括jsp-->
       <servlet-mapping>
           <servlet-name>springmvc</servlet-name>
           <url-pattern>/</url-pattern>
       </servlet-mapping>
   </web-app>
   ```

3. 配置`src\main\resources\springmvc-servlet.xml`

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd">
       
       <!--处理器映射器-->
       <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
       <!--处理器适配器-->
       <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
       
       <!--视图解析器:DispatcherServlet给他的ModelAndView 将来可以设置模板引擎Thymeleaf Freemarker-->
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
           <!--前缀-->
           <property name="prefix" value="/WEB-INF/jsp/"/>
           <!--后缀-->
           <property name="suffix" value=".jsp"/>
       </bean>
   
       <!--handler-->
       <bean id="/hello" class="com.zhang.controller.HelloController"/>
   </beans>
   ```

4. 编写`src\main\java\com\zhang\controller\HelloController.java`

   ```java
   public class HelloController implements Controller {
       public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
           //业务代码和视图跳转在此处写
           //ModelAndView 视图和模型
           ModelAndView modelAndView = new ModelAndView();
           modelAndView.addObject("msg","HelloSpringMVC");
           modelAndView.setViewName("hello");//WEB_INF/jsp/hello.jsp
           return modelAndView;
       }
   }
   ```

5. Web页面的文件如下

   ![image-20210427211704820](SpringMVC-1.assets/image-20210427211704820.png)

6. 启动Tomcat进行测试

   ![image-20210427211825868](SpringMVC-1.assets/image-20210427211825868.png)

---

可能遇到的问题:

1. 出现访问404

   - 查看控制台输出,是不是缺少Jar包

   - 查看项目输出结构，看IDEA有没有自动生成lib文件夹，如果没有，手动添加

     ![image-20210427212052294](SpringMVC-1.assets/image-20210427212052294.png)

   - 重启Tomcat



## 2、SpringMVC执行流程



![mvc](SpringMVC-1.assets/mvc.png)

**SpringMVC执行流程:**

1. 用户发送请求至前端控制器DispatcherServlet
2. DispatcherServlet收到请求调用处理器映射器HandlerMapping。
3. 处理器映射器根据请求url找到具体的处理器，生成处理器执行链HandlerExecutionChain(包括处理器对象和处理器拦截器)一并返回给DispatcherServlet。
4. DispatcherServlet根据处理器Handler获取处理器适配器HandlerAdapter执行HandlerAdapter处理一系列的操作，如：参数封装，数据格式转换，数据验证等操作
5. 执行处理器Handler(Controller，也叫页面控制器)。
6. Handler执行完成返回ModelAndView
7. HandlerAdapter将Handler执行结果ModelAndView返回到DispatcherServlet
8. DispatcherServlet将ModelAndView传给ViewReslover视图解析器
9. ViewReslover解析后返回具体View
10. DispatcherServlet对View进行渲染视图（即将模型数据model填充至视图中）。
11. DispatcherServlet响应用户。

![img](SpringMVC-1.assets/5220087-a1a9a9c3835aa94c.png)

---

自己写的代码的执行流程：

1. `web\WEB-INF\web.xml`注册`DispatcherServlet`

   ```xml
   <!--1.注册DispatcherServlet-->
   <servlet>
       <servlet-name>springmvc</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--关联一个SpringMvc的配置文件:[Servlet-name]-servlet.xml-->
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springmvc-servlet.xml</param-value>
       </init-param>
       <!--启动级别1-->
       <load-on-startup>1</load-on-startup>
   </servlet>
   <!--/ 匹配所有请求:不包括jsp-->
   <!--/* 匹配所有请求:包括jsp-->
   <servlet-mapping>
       <servlet-name>springmvc</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>
   ```

2. `src\main\resources\springmvc-servlet.xml`查找中央控制器和处理器映射器，根据请求url找到具体的处理器。

   ```xml
   <!--处理器映射器-->
   <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
   <!--处理器适配器-->
   <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
    <!--handler-->
   <bean id="/hello" class="com.zhang.controller.HelloController"/>
   ```

3. `src\main\java\com\zhang\controller\HelloController.java` 这一步本来还要处理模型，数据库拿数据等等，最后返回再加上页面，就是所谓的`ModelAndView`

   ```java
   public class HelloController implements Controller {
       public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
           //业务代码和视图跳转在此处写
           //ModelAndView 视图和模型
           ModelAndView modelAndView = new ModelAndView();
           modelAndView.addObject("msg","HelloSpringMVC");
           modelAndView.setViewName("hello");//WEB_INF/jsp/hello.jsp
           return modelAndView;
       }
   }
   ```

4. `src\main\resources\springmvc-servlet.xml`拼接视图名字，然后调用视图并返回。

   ```xml
   <!--视图解析器:DispatcherServlet给他的ModelAndView 将来可以设置模板引擎Thymeleaf Freemarker-->
   <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
       <!--前缀-->
       <property name="prefix" value="/WEB-INF/jsp/"/>
       <!--后缀-->
       <property name="suffix" value=".jsp"/>
   </bean>
   ```



# 3、注解开发SpringMVC

1. 配置`web\WEB-INF\web.xml`

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
       <!--1.注册DispatcherServlet-->
       <servlet>
           <servlet-name>springmvc</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <!--关联一个SpringMvc的配置文件:[Servlet-name]-servlet.xml-->
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:springmvc-servlet.xml</param-value>
           </init-param>
           <!--启动级别1-->
           <load-on-startup>1</load-on-startup>
       </servlet>
       <!--/ 匹配所有请求:不包括jsp-->
       <!--/* 匹配所有请求:包括jsp-->
       <servlet-mapping>
           <servlet-name>springmvc</servlet-name>
           <url-pattern>/</url-pattern>
       </servlet-mapping>
   </web-app>
   ```

2. 配置`src\main\resources\springmvc-servlet.xml`

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          https://www.springframework.org/schema/context/spring-context.xsd
          http://www.springframework.org/schema/mvc
          https://www.springframework.org/schema/mvc/spring-mvc.xsd">
   
       <!--自动扫描包,让指定包下的注解生效,由IOC容器统一管理-->
       <context:component-scan base-package="com.zhang.controller"/>
       <!--让SpringMVC不处理静态资源 .css .js .html .MP3-->
       <mvc:default-servlet-handler/>
       <!--支持MVC注解驱动
           在Spring中一般采用@RequestMapping注解来完成映射关系
           想要使@RequestMapping注解生效,必须使上下文中注册DefaultAnnotationHandlerMapping,
           还有一个AnnotationMethodHandlerAdapter实例,这两个实例分别在类级别和方法级别处理.
           而annotation-driven配置帮助我们自动完成上述两个实例的注入
           -->
       <mvc:annotation-driven/>
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
           <!--前缀-->
           <property name="prefix" value="/WEB-INF/jsp/"/>
           <!--后缀-->
           <property name="suffix" value=".jsp"/>
       </bean>
   	<!--注解开发不需要配这个处理器映射器、和处理器适配器，只需要支持mvc注解驱动-->
       <!--处理器映射器-->
       <!--<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>-->
       <!--处理器适配器-->
       <!--<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>-->
       <!--视图解析器:DispatcherServlet给他的ModelAndView 将来可以设置模板引擎Thymeleaf Freemarker-->
   </beans>
   ```

3. 写`src\main\java\com\zhang\controller\HelloController.java`

   ```java
   @Controller
   @RequestMapping("/Annotation")//是类里面的路径的前缀
   public class HelloController  {
       //真实访问地址:项目名/Annotation/hello
       @RequestMapping("/hello")
       public String Hello(Model model){
           model.addAttribute("msg","Hello,SPring MVCANnotation");
           //web-inf/jsp/hello.jsp
           return "hello";
       }
   }
   ```

4. 访问

   ​	![image-20210428142300057](SpringMVC-1.assets/image-20210428142300057.png)



# 4、Controller详解

## 1、 实现接口来写Controller

Controller接口源码的定义：

```java
@FunctionalInterface
public interface Controller {
    @Nullable
    ModelAndView handleRequest(HttpServletRequest var1, HttpServletResponse var2) throws Exception;
}
```

最精简方式

1. `web\WEB-INF\web.xml`

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
       <servlet>
           <servlet-name>springmvc</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:spring-servlet.xml</param-value>
           </init-param>
           <load-on-startup>1</load-on-startup>
       </servlet>
       <servlet-mapping>
           <servlet-name>springmvc</servlet-name>
           <url-pattern>/</url-pattern>
       </servlet-mapping>
   </web-app>
   ```

2. `src\main\resources\spring-servlet.xml`

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/mvc
          https://www.springframework.org/schema/mvc/spring-mvc.xsd">
       <mvc:default-servlet-handler/>
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/jsp/"></property>
           <property name="suffix" value=".jsp"/>
       </bean>
       <bean name="/test1" class="com.zhang.controller.ControllerDemo1"></bean>
   </beans>
   ```

3. `src\main\java\com\zhang\controller\ControllerDemo1.java`

   ```java
   //只要实现了Controller接口的类,说明这就是一个控制器了
   public class ControllerDemo1 implements Controller {
       public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
           ModelAndView modelAndView = new ModelAndView();
           modelAndView.addObject("msg","Controller1");
           modelAndView.setViewName("test");
           return modelAndView;
       }
   }
   ```

4. 页面`web\WEB-INF\jsp\test.jsp`

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
   ${msg}
   </body>
   </html>
   ```



## 2、使用注解来写Controller

- 下面几个注解作用相同，是在不同的控制器中使用的注解。

```java
@Controller
@Component
@Repository
@Service
```

- 为了使注解生效，需要在`src\main\resources\spring-servlet.xml`中使用扫描机制来找到所有基于注解的控制器类。

  ```xml
  <context:component-scan base-package="com.zhang.controller"/>
  <!-- 最好加上-->
  <mvc:annotation-driven/>
  ```

- 对应的JavaController的编写

  ```java
  @Controller
  //代表这个类会被Spring接管,被这个注
  // 解的类,如果方法返回值为String,并且有具体的页面可以跳转,那么就会自动被视图解析器解析。
  public class ControllerDemo2 {
      @RequestMapping("/t1")
      //放到方法上是return的页面的访问路径http://localhost:8080/t1
      //放到类上是页面的访问指增加了父路径http://localhost:8080/t1/test
      public String Demo2(Model model){
          model.addAttribute("msg","ControllerTest2");
          return "test";//  /WEB-INF/jsp/test.jsp
      }
  }
  ```