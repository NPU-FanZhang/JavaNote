# JavaWeb笔记-Servlet

> - Servlet是SUN公司开发动态web的一门技术
> - Sun公司在这些API中提供了一个接口叫做Servlet,如果你想开发一个Servlet程序,只需要完成两个小步骤:
>   - 编写一个类,实现Servlet接口
>   - 把开发好的Java类部署到Web服务器中
>
> 把实现了Servlet接口的Java程序叫做: Servlet

## 1. 新建一个Maven项目

- 创建一个空的Maven项目, 然后配置依赖, Servlet项目需要添加两个依赖

  <img src="JavaWeb笔记-Servlet.assets/image-20210412103200535.png" alt="image-20210412103200535" style="zoom: 67%;" />

  > 配置时注意Maven仓库的问题, 我自己并没有能将依赖下载本地仓库的指定目录下, 只下载到了默认路径下, 路径配置在`D:\maven\conf\settings.xml`中

## 2. 创建Servlet Module

- 创建之后的项目属于刚才创建的空项目的子项目

![image-20210412104207350](JavaWeb笔记-Servlet.assets/image-20210412104207350.png)

1. 关于父子项目的理解:

   父项目的`pom.xml`会出现:

   ```xml
   <modules>
       <module>Servlet-01</module>
   </modules>
   ```

   子项目可能会出现:

   ```xml
   <parent>
       <artifactId>javaweb-01</artifactId>
       <groupId>com.example</groupId>
       <version>1.0-SNAPSHOT</version>
   </parent>
   ```

   父项目的jar包, 子项目可以直接使用, 反之不行

   ```java
   son extends father
   ```

2. Maven环境优化

   1. 修改web.xml为最新(Tomcat中的web.xml)

   2. 将Maven项目的结构搭建完整

      ![image-20210412105025465](JavaWeb笔记-Servlet.assets/image-20210412105025465.png)

3. 编写一个Servlet程序

   1. 新建class, 实现Servlet接口类

      文件结构如下:

      ![image-20210412111005489](JavaWeb笔记-Servlet.assets/image-20210412111005489.png)

      接口类的实现原理如下:

      ```java
      public class HelloServlet extends HttpServlet {
          //继承HttpServlet的原因是因为整个包的继承关系为:
          //Servlet->GenericServlet->HttpServlet->自己定义的HelloServlet
          // 由于get或者Post只是请求实现的方式不同,可以相互调用,业务逻辑相同
          @Override
          protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
              super.doGet(req, resp);
          }
      
          @Override
          protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
              super.doPost(req, resp);
          }
      }
      ```

   2. 在web.xml编写Servlet的映射

      为什么需要映射:我们所写的是Java程序, 但是要浏览器访问,  浏览器需要链接web服务器, 所以我们需要在web服务中注册我们所写的Servlet, 还需要给他一个浏览器能够访问的路径

      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      
      <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                            http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
               version="4.0"
               metadata-complete="true">
      
        <servlet>
      <!--    Servlet名称-->
          <servlet-name>hello</servlet-name>
      <!--    Servlet的实现位置-->
          <servlet-class>com.zhang.servlet.HelloServlet</servlet-class>
        </servlet>
        <servlet-mapping>
      <!--    路径所调用的Servlet名称-->
          <servlet-name>hello</servlet-name>
      <!--    浏览器输入的路径-->
          <url-pattern>/Fuck</url-pattern>
        </servlet-mapping>
      
      </web-app>
      ```

   3. 配置Tomcat

      ![image-20210412112844233](JavaWeb笔记-Servlet.assets/image-20210412112844233.png)

      ​	注意项目配置的路径。

## 3. Servlet原理



