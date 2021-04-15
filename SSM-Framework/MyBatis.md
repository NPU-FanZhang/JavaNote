# MyBatis

![MyBatis logo](MyBatis.assets/mybatis-logo.png)

[TOC]

## 什么是 MyBatis？

- MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。
- MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
- MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。



## 为什么使用MyBatis

- 传统JDBC代码太过复杂。简化使用，实现自动化。
- 方便。
- 帮助程序员将数据存入数据库。
- 特性
  - 灵活：mybatis不会对应用程序或者数据库的现有设计强加任何影响。 `sql写在xml里，便于统一管理和优化。通过sql语句可以满足操作数据库的所有需求。`
  - `解除sql与程序代码的耦合`：通过提供DAO层，将业务逻辑和数据访问逻辑分离，使系统的设计更清晰，更易维护，更易单元测试。sql和代码的分离，提高了可维护性。
  - `提供xml标签，支持编写动态sql`。
  - 简单易学：本身就很小且简单。没有任何第三方依赖，最简单安装只要两个jar文件+配置几个sql映射文件易于学习，易于使用，通过文档和源代码，可以比较完全的掌握它的设计思路和实现。
  - 提供映射标签，支持对象与数据库的orm字段关系映射
  - 提供对象关系映射标签，支持对象关系组建维护



# 第一个MyBatis程序

## 1、环境搭建

1. 创建数据库表

   ```sql
   create database `MyBatis`;
   
   use `mybatis`;
   
   create table `user`(
   	`id` int(20) not null primary key,
       `name` varchar(50) default null,
       `password` varchar(50) default null
   )engine=InnoDB default charset=utf8;
   
   insert into `user`(`id`,`name`,`password`) values(1,'张三','123456');
   insert into `user`(`id`,`name`,`password`) values(2,'李四','123456');
   insert into `user`(`id`,`name`,`password`) values(3,'王五','123456');
   insert into `user`(`id`,`name`,`password`) values(4,'赵六','123456');
   ```

2. 新建项目

   创建一个空的Maven项目，注意Maven的项目位置，删除src，为了下面创建多个`modules`

   ![image-20210415171917379](MyBatis.assets/image-20210415171917379.png)

3. 导入依赖

   ```xml
   <!--  导入依赖  -->
   <!--    mysql驱动-->
   <!--    mymatis-->
   <!--    junit-->
       <dependencies>
           <dependency>
               <groupId>junit</groupId>
               <artifactId>junit</artifactId>
               <version>4.13</version>
           </dependency>
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>5.1.47</version>
           </dependency>
           <dependency>
               <groupId>org.mybatis</groupId>
               <artifactId>mybatis</artifactId>
               <version>3.5.2</version>
           </dependency>
       </dependencies>
   ```

4. 创建一个`module`

   ![image-20210415172723362](MyBatis.assets/image-20210415172723362.png)

## 2、配置MyBatis

> - XML 配置文件中包含了对 MyBatis 系统的核心设置，包括获取数据库连接实例的数据源（Data Source）以及决定事务作用域和控制方式的事务管理器（TransactionManager）。
>
> - 每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的。SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。而 SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先配置的 Configuration 实例来构建出 SqlSessionFactory 实例。

1. 创建`configuration`核心配置文件

   <img src="MyBatis.assets/image-20210415191405297.png" alt="image-20210415191405297" style="zoom:67%;" />

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
       <environments default="development">
           <environment id="development">
               <transactionManager type="JDBC"/>
               <dataSource type="POOLED">
                   <property name="driver" value="com.mysql.jdbc.Driver"/>
                   <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                   <property name="username" value="root"/>
                   <property name="password" value="123456"/>
               </dataSource>
           </environment>
       </environments>
       <!--    每一个Mapper.xml都需要在MyBatis核心配置文件中注册!-->
       <mappers>
           <mapper resource="org/mybatis/example/BlogMapper.xml"/>
       </mappers>
   </configuration>
   ```

2. 编写工具类

   ![image-20210415192114024](MyBatis.assets/image-20210415192114024.png)

   ```java
   package com.zhang.utils;
   
   import org.apache.ibatis.io.Resources;
   import org.apache.ibatis.session.SqlSession;
   import org.apache.ibatis.session.SqlSessionFactory;
   import org.apache.ibatis.session.SqlSessionFactoryBuilder;
   
   import java.io.IOException;
   import java.io.InputStream;
   //sqlSessionFactory-->sqlSession
   public class MybatisUtils {
       private static SqlSessionFactory sqlSessionFactory;
       static {
           try {
               String resource = "mybatis-config.xml";
               InputStream inputStream = Resources.getResourceAsStream(resource);
               sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
           }catch (IOException e){
               e.printStackTrace();
           }
       }
   
       //有了SQLSessionFactory,顾名思义,我们可以从中获取SqlSession的实例了
       //SqlSession完全包含了面向数据库执行Sql命令的所有方法
       public static SqlSession getSqlSession(){
           return sqlSessionFactory.openSession();
       }
   
   }
   ```



## 3、编写代码

![image-20210415195657640](MyBatis.assets/image-20210415195657640.png)

- 实体类

  ```java
  package com.zhang.pojo;
  
  public class User {
      private int id;
      private String name;
      private String password;
  
      public User() {
      }
  
      public User(int id, String name, String password) {
          this.id = id;
          this.name = name;
          this.password = password;
      }
  
      public int getId() {
          return id;
      }
  
      public String getName() {
          return name;
      }
  
      public String getPassword() {
          return password;
      }
  
      public void setId(int id) {
          this.id = id;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public void setPassword(String password) {
          this.password = password;
      }
  
      @Override
      public String toString() {
          return "User{" +
                  "id=" + id +
                  ", name='" + name + '\'' +
                  ", password='" + password + '\'' +
                  '}';
      }
  }
  ```

- Dao接口

  ```java
  public interface UserDao {
      public List<User> getUserList();
  }
  ```

- 接口实现类

  > 由原来的UserDaoImpl转变为一个Mapper配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <!--namespace = 绑定一个对应的Dao/Mapper接口-->
  <mapper namespace="com.zhang.dao.UserDao">
  <!--    id对应方法名 resultType对应结果集-->
      <select id="getUserList" resultType="com.zhang.pojo.User">
          select * from mybatis.user
      </select>
  </mapper>
  ```



## 4、测试

1. 创建测试类

   ![image-20210415201729628](MyBatis.assets/image-20210415201729628.png)

   ```java
   public class UserDaoTest {
       @Test
       public void test(){
           //第一步:获取SqlSession对象
           SqlSession sqlSession = MybatisUtils.getSqlSession();
           //方式一执行Sql
           UserDao userDao = sqlSession.getMapper(UserDao.class);
           List<User> userList = userDao.getUserList();
   		 //方式二:不推荐
           //List<User> userList = sqlSession.selectList("com.zhang.dao.UserDao.getUserList");
           for (User user : userList) {
               System.out.println(user);
           }
           sqlSession.close();
       }
   }
   ```
   
    执行结果为：
   
    ![image-20210415212552977](MyBatis.assets/image-20210415212552977.png)

## 5、常见报错

> - 配置文件没有注册
> - 绑定接口错误
> - 方法名不对
> - 返回类型不对
> - Maven资源导出问题



1. ```shell
   java.lang.ExceptionInInitializerError
      	at com.zhang.dao.UserDaoTest.test(UserDaoTest.java:14)
      Caused by: org.apache.ibatis.exceptions.PersistenceException: 
      ### Error building SqlSession.
      ### The error may exist in com/zhang/dao/UserMapper.xml
   ```

     `UserMapper.xml`在生成`Target`时未能导出，需要在`pom.xml`的`bulid`下面添加`resource`配置导出资源的类型。

      同时，xml中不能有中文，如果有中文也可能报这个错误！！！！！需要将IDEA的编码格式设置为`UTF-8`，有三四处需要设置。

   ```xml
      <resources>
        <resource>
          <directory>src/main/resources</directory>
          <excludes>
            <exclude>**/*.properties</exclude>
            <exclude>**/*.xml</exclude>
          </excludes>
          <filtering>true</filtering>
        </resource>
        <resource>
          <directory>src/main/java</directory>
          <includes>
            <include>**/*.properties</include>
            <include>**/*.xml</include>
          </includes>
          <filtering>true</filtering>
        </resource>
   </resources>
   ```

   

2. ```shell
   org.apache.ibatis.binding.BindingException: Type interface com.zhang.dao.UserDao is not known to the MapperRegistry.
   ```

   `mybatis-config.xml`中没有配置`mapper`

   ```xml
   <!--    每一个Mapper.xml都需要在MyBatis核心配置文件中注册!-->
       <mappers>
           <mapper resource="com/zhang/dao/UserMapper.xml"/>
       </mappers>
   ```




## 6、总结

1. 在`resources`写配置文件`mybatis-config.xml`
2. 编写工具类`utils`
3. 写实体类`pojo`
4. 写接口`Mapper(Dao)`
5. 写接口对应的`Mapper.xml`
6. 最后写`Test`

























