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





