# SSM Integration

[TOC]

---

集成开发环境：`IDEA`

数据库：`Mysql 5.7.32`

服务器：`Tomcat 9.0.45`

项目管理工具：`Maven：3.8.1`

JDK：`JDK 1.8`

---

# 1、数据库建表

```mysql
CREATE DATABASE `ssmbuild`;

USE `ssmbuild`;

CREATE TABLE `books`(
`bookID` INT(10) NOT NULL AUTO_INCREMENT COMMENT '书id',
`bookName` VARCHAR(100) NOT NULL COMMENT '书名',
`bookCounts` INT(11) NOT NULL COMMENT '数量',
`detail` VARCHAR(200) NOT NULL COMMENT '描述',
KEY `bookID`(`bookID`)
)ENGINE=INNODB DEFAULT CHARSET=utf8;

INSERT INTO `books`(`bookID`,`bookName`,`bookCounts`,`detail`) VALUES
(1,'Java',1,'从入门到放弃'),
(2,'MySQL',10,'从删库到跑路'),
(3,'Linux',5,'从进门到进牢');
```

# 2、项目pom.xml配置

- 创建普通Maven项目

- pom.xml中配置依赖

  ```xml
  <!--添加依赖
      junit   数据库驱动   数据库连接池  servlet JSP
      mybatis mybatis-spring  spring lombok
  -->
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
      <!--数据库连接池 : c3p0 -->
      <dependency>
          <groupId>com.mchange</groupId>
          <artifactId>c3p0</artifactId>
          <version>0.9.5.5</version>
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
      <dependency>
          <groupId>org.mybatis</groupId>
          <artifactId>mybatis</artifactId>
          <version>3.5.2</version>
      </dependency>
      <dependency>
          <groupId>org.mybatis</groupId>
          <artifactId>mybatis-spring</artifactId>
          <version>2.0.2</version>
      </dependency>
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-webmvc</artifactId>
          <version>5.3.6</version>
      </dependency>
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-jdbc</artifactId>
          <version>5.3.6</version>
      </dependency>
      <dependency>
          <groupId>org.projectlombok</groupId>
          <artifactId>lombok</artifactId>
          <version>1.18.12</version>
      </dependency>
  </dependencies>
  ```

- pom.xml中配置静态资源导出

  ```xml
  <build>
      <resources>
          <resource>
              <directory>src/main/resources</directory>
              <excludes>
                  <exclude>**/*.properties</exclude>
                  <exclude>**/*.xml</exclude>
              </excludes>
              <filtering>false</filtering>
          </resource>
          <resource>
              <directory>src/main/java</directory>
              <includes>
                  <include>**/*.properties</include>
                  <include>**/*.xml</include>
              </includes>
              <filtering>false</filtering>
          </resource>
      </resources>
  </build>
  ```







# 3、resources中创建配置和资源文件

![image-20210510112906346](SSM Integration.assets/image-20210510112906346.png)

- 创建数据库连接配置文件database.properties

  ```properties
  jdbc.driver=com.mysql.jdbc.Driver
  #Mysql8.0+需要在url中增加时区配置 &serverTimezone=Asia/Shanghai
  jdbc.url=jdbc:mysql://localhost:3306/ssmbuild?useSSL=true&useUnicode=true&characterEncoding=utf8
  jdbc.username=root
  jdbc.password=123456
  ```

- 创建mybatis的配置文件mybatis-config.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.prg//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
  </configuration>
  ```

- 创建spring的配置文件applicationContext.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          http:http://www.springframework.org/schema/beans/spring-beans.xsd">
  
  </beans>
  ```



# 4、创建实体类及接口，实现SQL



- 创建实体类`Books`

  ```java
  @Data
  @AllArgsConstructor
  @NoArgsConstructor
  public class Books {
      private int bookID;
      private String bookName;
      private int bookCounts;
      private String detail;
  
  }
  ```

- 编写dao层的`BookMapper`接口及`BookMapper.xml`实现

  ```java
  public interface BookMapper {
      //增加一本书
      int addBook(Books book);
      //删除一本书
      int deleteBookByID(@Param("bookId") int ID);
      //更新一本书
      int updateBook(Books book);
      //查询一本书
      Books queryBookByID(@Param("bookId") int ID);
      //查询全部的书
      List<Books> queryAllBooks();
  }
  ```

  

  ```xml-dtd
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.prg//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="com.zhang.dao.BookMapper">
  
      <insert id="addBook" parameterType="Books">
          insert into ssmbuild.books( bookName, bookCounts, detail)
          VALUES (#{bookName},#{bookCounts},#{detail});
      </insert>
      <delete id="deleteBookByID" parameterType="int">
          delete from ssmbuild.books where bookID=#{bookId};
      </delete>
      <update id="updateBook" parameterType="Books">
          update ssmbuild.books
          set bookName=#{bookName},
              bookCounts=#{bookCounts},
              detail=#{detail}
          where bookID=#{bookID}
      </update>
      <select id="queryBookByID" resultType="Books">
          select *
          from ssmbuild.books where bookID=#{bookId};
      </select>
      <select id="queryAllBooks" resultType="Books">
          select *
          from ssmbuild.books;
      </select>
  </mapper>
  ```

  写Sql时的自动补齐可以在IDEA中设置，`File | Settings | Languages & Frameworks | SQL Dialects`

- 在`mybatis-config.xml`中注册写好的`BookMapper.xml`

  ```xml
  <mappers>
      <!--dao里面的xml与接口名一致的话,可以使用class绑定
          如果不一致,使用resource绑定-->
      <mapper class="com.zhang.dao.BookMapper"></mapper>
  </mappers>
  ```

