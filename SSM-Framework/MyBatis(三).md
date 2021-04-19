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







