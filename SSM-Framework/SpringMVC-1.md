# Spring MVC



![spring-logo-9146a4d3298760c2e7e49595184e1975](SpringMVC-1.assets/spring-logo-9146a4d3298760c2e7e49595184e1975.svg)

[TOC]



# 1、SpringMVC简介

## 1、MVC简介

经典MVC模式中，M是指业务模型，V是指用户界面，C则是控制器。

- V-View`视图是指用户看到并与之交互的界面。
- M-`Model`模型是指模型表示业务规则，包括数据和行为。
- C-`Controller`控制器是指控制器接受用户的输入并调用模型和视图去完成用户的需求，控制器本身不输出任何东西和做任何处理，是一个调度员的作用。

使用MVC的目的是将M和V的实现代码分离，从而使同一个程序可以使用不同的表现形式。

MVC的主要作用是降低了视图与业务逻辑间的双向耦合。

MVC不是一种设计模式，而是一种架构模式，所以不同的MVC之间存在差异。



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