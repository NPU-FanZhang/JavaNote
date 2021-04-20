# MyBatis

![MyBatis logo](MyBatis(三).assets/mybatis-logo.png)

[TOC]

# 1.	分页

为什么要分页?

- 减少数据的处理量。

SQL中`Limit`分页：

```sql
select * from user limit startIndex , pageSize;
```

## Limit实现分页

使用MyBatis用limit实现分页，通过Map传入 startIndex , pageSize，核心SQL。

`UserMapper`

```java
//分页查询
List<User> getUserWithLimit(HashMap<String,Integer> map);
```

`UserMapper.xml`

```xml
<select id="getUserWithLimit" parameterType="map" resultType="User">
    select * from mybatis.user limit #{startIndex},#{pageSize}
</select>
```

`Test类`

```java
@Test
public void testGetUserWithLimit(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    HashMap<String, Integer> map = new HashMap<String, Integer>();
    map.put("startIndex",1);
    map.put("pageSize",3);
    List<User> res = userMapper.getUserWithLimit(map);
    for (User user : res) {
        System.out.println(user);
    }
    sqlSession.close();
}
```



## RowBounds分页

`UserMapper`接口

```java
//RowBounds分页查询
List<User> getUserWithRowBounds();
```

`UserMapper.xml`

```xml
<select id="getUserWithRowBounds"  resultMap="UserMap">
    select * from mybatis.user;
</select>
```

`UserDaoTest`

```java
@Test
public void testGetUserWithRowBounds(){
    //第一步:获取SqlSession对象
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    RowBounds rowBounds = new RowBounds(1, 2);
    List<User> UserList = sqlSession.selectList("com.zhang.dao.UserMapper.getUserWithRowBounds",null,rowBounds);

    for (User user : UserList) {
        System.out.println(user);
    }
    sqlSession.close();
}
```



# 2.	注解开发

​		使用注解来映射简单语句会使代码显得更加简洁，但对于稍微复杂一点的语句，Java 注解不仅力不从心，还会让你本就复杂的 SQL 语句更加混乱不堪。 因此，如果你需要做一些很复杂的操作，最好用 XML 来映射语句。

​		注解开发的核心，就是使用反射，通过反射，获取注解。

​		底层：动态代理。

使用方法:

1. 定义`mapper`接口

   ```java
   //查询用户
   @Select("select * from user")
   List<User> getUser();
   ```

2. `mybatis-config.xml`中绑定接口，注意接口绑定使用的`class`，而不是`resource`，因为`resource`绑定的是接口对应的`xml`，而`class`直接绑定的是`mapper`类，所以注解开发只能使用`class`。

   ```xml
   <!--    绑定接口-->
       <mappers>
           <mapper class="com.zhang.dao.UserMapper" />
       </mappers>
   ```

   - 接口和他的Mapper配置文件必须同名。

   - 接口和他的Mapper配置文件必须在同一个包下。

3. `Test`类中进行调用

   ```java
   @Test
   public void TestSelect(){
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
       List<User> users = mapper.getUser();
       for (User user : users) {
           System.out.println(user);
       }
   }
   ```



## CURD

1. 定义`mapper`接口

   ```java
       //查询用户
       @Select("select * from user where id = #{id} or name= #{name}")
       List<User> getUserById(@Param("id")int id,@Param("name")String name);
   
   //    //Insert
   //    @Insert("insert into user(id,name,password) values(#{id},#{name},#{password})")
   //    int addUser(@Param("id") int id,@Param("name") String name,@Param("password") String password);
       //Insert
       @Insert("insert into user(id,name,password) values(#{id},#{name},#{psw})")
       int addUser(User user);
   
       //Update
       @Update("update user set name = #{name},password = #{psw} where id = #{id}")
       int updateUser(User user);
   
       //Delete
       @Update("delete from user  where id = #{uid}")
       int deleteUser(@Param("uid") int id);
   ```

2. `mybatis-config.xml`中绑定接口，注意接口绑定使用的`class`，而不是`resource`，因为`resource`绑定的是接口对应的`xml`，而`class`直接绑定的是`mapper`类，所以注解开发只能使用`class`。

   ```xml
   <!--    绑定接口-->
       <mappers>
           <mapper class="com.zhang.dao.UserMapper" />
       </mappers>
   ```

3. `Test`类中进行调用

   ```java
   @Test
   public void TestSelectById(){
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
       List<User> users = mapper.getUserById(2,"张三");
       for (User user : users) {
           System.out.println(user);
       }
       sqlSession.close();
   }
   @Test
   public void TestAddUser(){
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
       int res = mapper.addUser(new User(11,"rose","45123"));
       sqlSession.commit();
       sqlSession.close();
   }
   @Test
   public void TestUpdateUser(){
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
   
       int res = mapper.updateUser(new User(10,"mike","45123"));
       sqlSession.commit();
       sqlSession.close();
   }
   @Test
   public void TestDeleteUser(){
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
       int res = mapper.deleteUser(10);
       sqlSession.commit();
       sqlSession.close();
   }
   ```

**@Param()注解**

- 基本类型或String需要加上
- 引用类型不需要加
- 只有一个基本类型可以省略，但最好加上。
- #{}可以防止sql注入，${}不可以防止sql注入，因为他直接进行字符串拼接了。



# 3.	MyBatis详细执行流程

```java
-->Resources获取加载全局配置文件mybatis-config.xml
    -->实例化SQLSessionFactoryBuilder构造器
    -->解析配置文件流XMLConfigBuilder
    -->Configuration所有的配置信息
    -->SqlSessionFactory实例化
    	-->Transaction事务管理
    	-->创建Executor执行器
    	-->创建SqlSession
    	-->实现CRUD
    	-->若回滚,返回Transaction事务管理
    -->查看是否执行成功,若失败,返回事务管理.
    -->提交事务
```



# 4.	Lombok（不推荐使用，但香）

*Project Lombok is a java library that automatically plugs into your editor and build tools, spicing up your java.*
*Never write another getter or equals method again, with one annotation your class has a fully featured builder, Automate your logging variables, and much more.*

1. IDEA引入plugin

   ![image-20210419205625923](MyBatis(三).assets/image-20210419205625923.png)

2. pom.xml中引入Jar包。

   ```xml
   <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
       <version>1.18.12</version>
   </dependency>
   ```

3. POJO类中引入

   ```java
   import lombok.Data;
   
   @Data
   public class User {
       private int id;
       private String name;
       private String psw;
   
   }
   ```



**常用注解**

```java
@Getter and @Setter
@FieldNameConstants
@ToString
@EqualsAndHashCode
@AllArgsConstructor, @RequiredArgsConstructor and @NoArgsConstructor
@Log, @Log4j, @Log4j2, @Slf4j, @XSlf4j, @CommonsLog, @JBossLog, @Flogger, @CustomLog
`@Data`
@Builder
@SuperBuilder
@Accessors
```



# 5.	多对一的处理

例如，多个学生对应一个老师。

1. 创建数据库表。

   ```sql
   CREATE TABLE `teacher` (
     `id` INT(10) NOT NULL,
     `name` VARCHAR(30) DEFAULT NULL,
     PRIMARY KEY (`id`)
   ) ENGINE=INNODB DEFAULT CHARSET=utf8;
   
   INSERT INTO teacher(`id`, `name`) VALUES (1, '秦老师'); 
   
   CREATE TABLE `student` (
     `id` INT(10) NOT NULL,
     `name` VARCHAR(30) DEFAULT NULL,
     `tid` INT(10) DEFAULT NULL,
     PRIMARY KEY (`id`),
     KEY `fktid` (`tid`),
     CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
   ) ENGINE=INNODB DEFAULT CHARSET=utf8;
   INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('1', '小明', '1'); 
   INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('2', '小红', '1'); 
   INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('3', '小张', '1'); 
   INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('4', '小李', '1'); 
   INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('5', '小王', '1');
   ```

2. 创建实体类

   多对一

   `Student`

   ```java
   @Data
   public class Student {
       private int id;
       private String name;
       private Teacher teacher;
   }
   ```

   `Teacher`

   ```java
   @Data
   public class Teacher {
       private int id;
       private String name;
   }
   ```

3. 创建`MyBatis`项目，进行配置。

   2. 创建实体类接口
   3. 创建接口对应的`xml`文件

   其中，多个实体类后，将接口对应的映射文件创建到`resources`下面，并放在与接口类同名的包下，`resources`建包需要使用/区分开。

   ![image-20210420130434137](MyBatis(三).assets/image-20210420130434137.png)

4. `mybatis-config.xml`下进行`mapper`映射

   ```xml
   <mappers>
       <mapper resource="com/zhang/dao/TeacherMapper.xml"></mapper>
       <mapper resource="com/zhang/dao/StudentMapper.xml"></mapper>
   </mappers>
   ```



## 1.	按照查询嵌套处理

假设为了查找每个学生，及其对应的老师对象。

接口定义:

```java
public interface StudentMapper {
    //查询所有学生及对应老师的信息
    public List<Student> getStudent();

}
```

`StudentMapper.xml`的定义:

```xml
<!--
思路:
1.先查询所有学生的信息
2.然后根据查询的学生的tid,寻找对应的老师
-->
<select id="getStudent" resultMap="StudentTeacher">
    select * from student ;
</select>
<resultMap id="StudentTeacher" type="Student">
     <result property="id" column="id"></result>
     <result property="name" column="name"></result>
        <!--复杂属性,需要单独处理
        对象:association
        集合:collection
        -->
     <association property="teacher" column="tid" javaType="Teacher" select="getTeach"></association>
</resultMap>
<select id="getTeach" resultType="Teacher">
    select * from teacher where id = #{id};
</select>
```

Test中直接调用接口即可。

重点是xml的语句逻辑。

![image-20210420142821689](MyBatis(三).assets/image-20210420142821689.png)



## 2.	按照结果嵌套处理

接口定义:

```java
public interface StudentMapper {
    //查询所有学生及对应老师的信息
    public List<Student> getStudent2();

}
```

`StudentMapper.xml`的定义:

```xml
<!--按照结果嵌套查询-->
<select id="getStudent2" resultMap="StudentTeacher2">
    select s.id ,s.name sname,t.name  tname from student s,teacher t where s.tid = t.id;
</select>
<resultMap id="StudentTeacher2" type="Student">
    <result property="id" column="id"></result>
    <result property="name" column="name"></result>
    <association property="teacher" javaType="Teacher">
        <result property="name" column="tname"></result>
    </association>
</resultMap>
```

![image-20210420144709041](MyBatis(三).assets/image-20210420144709041.png)

结果(因为没有查找tid, 所以tid对应为0):

```shell
Student(id=1, name=null, teacher=Teacher(id=0, name=秦老师))
Student(id=2, name=null, teacher=Teacher(id=0, name=秦老师))
Student(id=3, name=null, teacher=Teacher(id=0, name=秦老师))
Student(id=4, name=null, teacher=Teacher(id=0, name=秦老师))
Student(id=5, name=null, teacher=Teacher(id=0, name=秦老师))
```



# 6.	一对多的处理



实体类一对多定义:

`Student`

```java
@Data
public class Student {
    private int id;
    private String name;
    private int tid;
}
```

`Teacher`

```java
@Data
public class Teacher {
    private int id;
    private String name;
    private List<Student> students;
}
```



两种方式下的接口：

```java
//获取指定老师下的所有学生的信息
Teacher getTeacherStudent(@Param("tid") int id);
//获取指定老师下的所有学生的信息
Teacher getTeacherStudent2(@Param("tid") int id);
```

结果嵌套和查询嵌套下的`XML`

```xml
<mapper namespace="com.zhang.dao.TeacherMapper">
    <!--结果嵌套-->
    <select id="getTeacher" resultType="Teacher">
        select * from teacher;
    </select>
    <select id="getTeacherStudent" resultMap="TeacherStudent">
        select s.id sid,s.name sname,t.name tname,t.id tid
        from mybatis.teacher t,mybatis.student s
        where s.tid = t.id and t.id = #{tid}
    </select>
    <resultMap id="TeacherStudent" type="Teacher">
        <result column="tid" property="id"></result>
        <result column="tname" property="name"></result>
        <collection property="students" ofType="Student">
            <result property="id" column="sid"></result>
            <result property="tid" column="tid"></result>
            <result property="name" column="sname"></result>
        </collection>
    </resultMap>
	<!--查询嵌套-->
    <select id="getTeacherStudent2" resultMap="TeacherStudent2">
        select * from mybatis.teacher where id = #{tid};
    </select>
    <resultMap id="TeacherStudent2" type="Teacher">
        <collection property="students" javaType="ArrayList" ofType="Student" column="id" select="getStudentByTID"/>
    </resultMap>
    <select id="getStudentByTID" resultType="Student">
        select * from mybatis.student where tid = #{tid};
    </select>
</mapper>
```

结果:

```java
public void testStudent(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        TeacherMapper mapper = sqlSession.getMapper(TeacherMapper.class);
        Teacher teacherStudent = mapper.getTeacherStudent2(1);
        System.out.println(teacherStudent);
    	//注意id不同
    	//getTeacherStudent
//        Teacher(id=1, name=秦老师,
//        students=[Student(id=1, name=小明, tid=1),
//                  Student(id=2, name=小红, tid=1),
//                  Student(id=3, name=小张, tid=1),
//                  Student(id=4, name=小李, tid=1),
//                  Student(id=5, name=小王, tid=1)])
		//getTeacherStudent2
//        Teacher(id=0, name=秦老师,
//        students=[Student(id=1, name=小明, tid=1),
//                  Student(id=2, name=小红, tid=1),
//                  Student(id=3, name=小张, tid=1),
//                  Student(id=4, name=小李, tid=1),
//                  Student(id=5, name=小王, tid=1)])
        sqlSession.close();

    }
```

## 小结

- 关联-association [多对一]
- 集合-collection [一对多]
- javaType & ofType
  - javaType用来指定实体类中的属性
  - ofType用来指定映射到List或者集合中的POJO类型，范型中的约束类型。



# 7. 	动态SQL

​		**动态SQL就是指依据不同的条件，生成不同的SQL语句。**

​		在 MyBatis 之前的版本中，需要花时间了解大量的元素。借助功能强大的基于 OGNL 的表达式，MyBatis 3 替换了之前的大部分元素，大大精简了元素种类，现在要学习的元素种类比原来的一半还要少。

- IF
- choose (when, otherwise)
- trim (where, set)
- foreach

1. 创建数据库表：

   ```sql
   CREATE TABLE `blog`(
   `id` VARCHAR(50) NOT NULL COMMENT '博客id',
   `title` VARCHAR(100) NOT NULL COMMENT '博客标题',
   `author` VARCHAR(30) NOT NULL COMMENT '博客作者',
   `create_time` DATETIME NOT NULL COMMENT '创建时间',
   `views` INT(30) NOT NULL COMMENT '浏览量'
   )ENGINE=INNODB DEFAULT CHARSET=utf8;
   ```

2. 编写实体类

   ```java
   @Data
   public class blog {
       private int id;
       private String title;
       private String author;
       private Date createDate;
       private int views;
   }
   ```

## 1.	IF

`Interface`:

```java
//查询博客
List<Blog> queryBlogIf(Map map);
```

`BlogMapper.xml`

```xml
<select id="queryBlogIf" parameterType="map" resultType="Blog">
    select * from  mybatis.blog where 1=1
    <if test="title != null">
        and title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</select>
```

Test类:

```java
@Test
public void queryBlogIf(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
    HashMap<String, String> map = new HashMap<String, String>();
    map.put("title","Mybatis");
    List<Blog> blogs = mapper.queryBlogIf(map);
    for (Blog blog : blogs) {
        System.out.println(blog);
    }
    sqlSession.close();
}
```

结果:

```shell
==>  Preparing: select * from mybatis.blog where 1=1 and title = ? 
==> Parameters: Mybatis(String)
<==    Columns: id, title, author, create_time, views
<==        Row: c9f7b3e34e954b179e0da43d338d5a70, Mybatis, 狂神说, 2021-04-20 17:24:19.0, 9999
<==      Total: 1
Blog(id=c9f7b3e34e954b179e0da43d338d5a70, title=Mybatis, author=狂神说, createTime=Tue Apr 20 17:24:19 CST 2021, views=9999)
```



## 2.	choose (when, otherwise)

​		有时候，我们不想使用所有的条件，而只是想从多个条件中选择一个使用。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。

```xml 
<choose></choose>标签中的when,有满足when的条件时,会自动附加第一个符合条件的when语句,如果一个满足的都没有,就会自动拼接otherwise标签中的语句
```

`BlogMapper.xml`

```xml
<select id="queryBlogChoose" parameterType="map" resultType="Blog">
    select * from  mybatis.blog
    <where>
        <choose>
            <when test=" title != null">
                title = #{title}
            </when>
            <when test="author != null">
                and author = #{author}
            </when>
            <otherwise>
                and views =#{views}
            </otherwise>
        </choose>
    </where>
</select>
```

`Test`

```java
@Test
public void queryBlogChoose()
{
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
    HashMap<String, String> map = new HashMap<String, String>();
    map.put("title","Mybatis");
    map.put("views","9999");
    List<Blog> blogs = mapper.queryBlogChoose(map);
    for (Blog blog : blogs) {
        System.out.println(blog);
    }
    sqlSession.close();
}
```

结果:

尽管上面的map中加了title和views两个条件,但是最后生成的sql只有一个title条件。

```shell
==>  Preparing: select * from mybatis.blog WHERE title = ? 
==> Parameters: Mybatis(String)
<==    Columns: id, title, author, create_time, views
<==        Row: c9f7b3e34e954b179e0da43d338d5a70, Mybatis, 狂神说, 2021-04-20 17:24:19.0, 9999
<==      Total: 1
```



## 3.	trim、where、set

`Set`

​		set元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号（这些逗号是在使用条件语句给列赋值时引入的）。

等价于:

```xml
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```

`Where`

​	*where* 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，*where* 元素也会将它们去除。

等价于:

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
```

`Trim`

就是用来自定义where和set的功能，进行微小的调整。

---

`Set例子`：

```xml
<update id="updateBlogSet" parameterType="map">
    update  mybatis.blog
    <set>
        <if test="title != null">
            title = #{title},
        </if>
        <if test="author != null">
            author = #{author},
        </if>
    </set>
    where id = #{id}
</update>
```

`Test`

```java
@Test
public void updateBlogSet()
{
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
    HashMap<String, String> map = new HashMap<String, String>();
    map.put("author","狂神说");
    map.put("id","c9f7b3e34e954b179e0da43d338d5a70");
    int res = mapper.updateBlogSet(map);
    System.out.println(res);
    sqlSession.close();
}
```

效果:

拼接时去掉了author = #{author}`,` 最后的`,`

```shell
==>  Preparing: update mybatis.blog SET author = ? where id = ? 
==> Parameters: 狂神说(String), c9f7b3e34e954b179e0da43d338d5a70(String)
<==    Updates: 1
```



## 4.	Sql片段

​		有的时候我们可能会将一些功能的公共部分抽取出来，用来提高重用。

`XML`

```xml
<sql id="if-title-author">
    <if test="title != null">
        and title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</sql>
<select id="queryBlogIf" parameterType="map" resultType="Blog">
    select * from  mybatis.blog where 1=1
    <include refid="if-title-author"></include>
</select>
```

注意事项:

- 最好基于单表来定义SQl片段。
- 不要存在`WHERE`标签。



## 5.	Foreach

动态 SQL 的另一个常见使用场景是对集合进行遍历（尤其是在构建 IN 条件语句的时候）。

foreach元素的功能非常强大，它允许你指定一个集合，声明可以在元素体内使用的集合项（item）和索引（index）变量。

它也允许你指定开头与结尾的字符串以及集合项迭代之间的分隔符。这个元素也不会错误地添加多余的分隔符。

*你可以将任何可迭代对象（如 List、Set 等）、Map 对象或者数组对象作为集合参数传递给 foreach。*

- *当使用可迭代对象或者数组时，index 是当前迭代的序号，item 的值是本次迭代获取到的元素。*
- *当使用 Map 对象（或者 Map.Entry 对象的集合）时，index 是键，item 是值。*

`XML`

```xml
<!--实现sql
    select * from  mybatis.blog where id = 1 or id = 2 or id = 3;
    -->
<select id="queryBlogForeach" parameterType="map" resultType="Blog">
    select * from mybatis.blog
    <where>
        <foreach collection="allId" item="item" index="index" open="and ( " separator="or" close=")">
            id = #{item}
        </foreach>
    </where>
</select>
```

`Test`

```java
@Test
public void queryBlogForeach()
{
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
    HashMap map = new HashMap();
    ArrayList<String> integers = new ArrayList<String>();

    integers.add("c9f7b3e34e954b179e0da43d338d5a70");
    integers.add("05fb82806be04f2fac7559d919ad0736");

    map.put("allId",integers);
    List<Blog> blogs = mapper.queryBlogForeach(map);
    for (Blog blog : blogs) {
        System.out.println(blog);
    }
    sqlSession.close();
}
```

结果:

​	注意最后生成的SQL语句

```shell
==>  Preparing: select * from mybatis.blog WHERE ( id = ? or id = ? ) 
==> Parameters: c9f7b3e34e954b179e0da43d338d5a70(String), 05fb82806be04f2fac7559d919ad0736(String)
<==    Columns: id, title, author, create_time, views
<==        Row: c9f7b3e34e954b179e0da43d338d5a70, Mybatis, 狂神说, 2021-04-20 17:24:19.0, 9999
<==        Row: 05fb82806be04f2fac7559d919ad0736, Java, 狂神说, 2021-04-20 17:24:19.0, 1999
<==      Total: 2
Blog(id=c9f7b3e34e954b179e0da43d338d5a70, title=Mybatis, author=狂神说, createTime=Tue Apr 20 17:24:19 CST 2021, views=9999)
Blog(id=05fb82806be04f2fac7559d919ad0736, title=Java, author=狂神说, createTime=Tue Apr 20 17:24:19 CST 2021, views=1999)
```

