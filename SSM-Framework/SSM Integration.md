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

  

- pom.xml中配置静态资源导出

  