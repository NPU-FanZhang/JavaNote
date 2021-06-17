# SpringBoot

[TOC]

# 1、什么是Spring Boot

Spring Boot makes it easy to create stand-alone, production-grade Spring based Applications that you can "just run".

We take an opinionated view of the Spring platform and third-party libraries so you can get started with minimum fuss. Most Spring Boot applications need minimal Spring configuration.

## Features

- Create ==stand-alone== Spring applications
- Embed Tomcat, Jetty or Undertow directly (no need to deploy WAR files)
- Provide opinionated 'starter' dependencies to simplify your build configuration
- Automatically configure Spring and 3rd party libraries whenever possible
- Provide production-ready features such as metrics, health checks, and externalized configuration
- Absolutely no code generation and no requirement for XML configuration



# 2、SpringBoot基本原理

<img src="asset/SpringBoot-1.assets/6912735-51aa162747fcdc3d.png" alt="img"  />



# 3、SpringBoot配置

Springboot的核心配置文件为`application.properties`，也可以是`application.yaml`。

官方推荐使用`yaml`格式，因为更加简单。

配置文件名必须为`application`。

Spring Boot中强调JavaConfig，就是使用Java类进行环境的配置。



## 1、yaml给实体类赋值。

- 创建`src\main\resources\application.yaml`

  ```yaml
  person:
    name: zhangfan
    age: 3
    happy: false
    birth: 2020/11/01
    maps: {k1: v1, k2: v2}
    list:
      - code
      - music
      - girl
    dog:
      name: 旺财
      age: 3
  ```

- 定义实体类

  ```java
  @Component
  @ConfigurationProperties(prefix = "person")
  //当配置文件名不是application时,需要手动的引入配置文件
  //然后在每个属性前使用注解进行配置@Value("${name}"),其中的属性是自定义的配置文件中的属性名
  //@PropertySource(value = "classpath:xxx.properties")
  public class Person {
      private String name;
      private Integer age;
      private Boolean happy;
      private Date birth;
      private Map<String,Object> maps;
      private List<Object> list;
      //自定义了Dog类
      private Dog dog;
  
   	//后面包括构造方法和get、set方法
  }
  ```

- @ConfigurationProperties(prefix = "person")

  - 这个注解来映射到yaml中的person值。

  - 这个注解需要进行配置，不然爆红。

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
    </dependency>
    ```

- 测试类中进行测试

  ```java
  @SpringBootTest
  class Springbootdemo1ApplicationTests {
      @Autowired
      private Person person;
      @Test
      void contextLoads() {
          //Person{name='zhangfan',
          // age=3,
          // happy=false,
          // birth=Sun Nov 01 00:00:00 CST 2020,
          // maps={k1=v1, k2=v2},
          // list=[code, music, girl],
          // dog=Dog{name='旺财', age=3}}
          System.out.println(person);
      }
  }
  ```

- 属性松散绑定
  表示驼峰式、下划线(_)、短横线(-)

  标准方式	person.firstName

  大写用-	person.first-name

  大写用_	person.first_name

  三种方式，都可以使用，推荐，属性书写方式
  PERSON_FIRST_NAME

- JSR303校验

  ```java
  先加上@Validated//数据校验
  空检查
  @Null       验证对象是否为null
  @NotNull    验证对象是否不为null, 无法查检长度为0的字符串
  @NotBlank 检查约束字符串是不是Null还有被Trim的长度是否大于0,只对字符串,且会去掉前后空格.
  @NotEmpty 检查约束元素是否为NULL或者是EMPTY.
  Booelan检查
  @AssertTrue     验证 Boolean 对象是否为 true 
  @AssertFalse    验证 Boolean 对象是否为 false  
  
  长度检查
  @Size(min=, max=) 验证对象（Array,Collection,Map,String）长度是否在给定的范围之内 
  @Length(min=, max=) Validates that the annotated string is between min and max included.
  
  日期检查
  @Past           验证 Date 和 Calendar 对象是否在当前时间之前  
  @Future     验证 Date 和 Calendar 对象是否在当前时间之后  
  @Pattern    验证 String 对象是否符合正则表达式的规则
  
  数值检查，建议使用在Stirng,Integer类型，不建议使用在int类型上，因为表单值为“”时无法转换为int，但可以转换为Stirng为"",Integer为null
  @Min            验证 Number 和 String 对象是否大等于指定的值  
  @Max            验证 Number 和 String 对象是否小等于指定的值  
  @DecimalMax 被标注的值必须不大于约束中指定的最大值. 这个约束的参数是一个通过BigDecimal定义的最大值的字符串表示.小数存在精度
  @DecimalMin 被标注的值必须不小于约束中指定的最小值. 这个约束的参数是一个通过BigDecimal定义的最小值的字符串表示.小数存在精度
  @Digits     验证 Number 和 String 的构成是否合法  
  @Digits(integer=,fraction=) 验证字符串是否是符合指定格式的数字，interger指定整数精度，fraction指定小数精度。
  @Range(min=, max=) 检查数字是否介于min和max之间.
  @Range(min=10000,max=50000,message="range.bean.wage")
  private BigDecimal wage;
  @Valid 递归的对关联对象进行校验, 如果关联对象是个集合或者数组,那么对其中的元素进行递归校验,如果是一个map,则对其中的值部分进行校验.(是否进行递归验证)
  @CreditCardNumber信用卡验证
  @Email  验证是否是邮件地址，如果为null,不进行验证，算通过验证。
  @ScriptAssert(lang= ,script=, alias=)
  @URL(protocol=,host=, port=,regexp=, flags=)
  ```

  

## 2、多环境配置及配置文件

SpringBoot可以包含多个配置文件，并且有加载优先级。



## 3、首页配置







# 4、thymeleaf模板引擎

单纯的SpringBoot项目无法进入页面，需要导入模板引擎来进行渲染

引入依赖:

```xml
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-java8time</artifactId>
</dependency>
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring5</artifactId>
</dependency>
```

查看源码`ThymeleafProperties`，说明文件需要放在templates目录下

```java
public static final String DEFAULT_PREFIX = "classpath:/templates/";
public static final String DEFAULT_SUFFIX = ".html";
```

后面的`html`文件可以使用`thymeleaf`语法进行开发，类似于Vue，就是没有完全分离。



# 5、SpringMVC扩展配置

==以下只是几个例子，说明SpringBoot的配置方法。==

自定义类，实现MVC的自定义配置`src\main\java\com\zhang\springbootdemo\config\MyConfig.java`

```java
//扩展MVC配置
//@Configuration相当于传统的xml配置文件，如果有些第三方库需要用到xml文件，建议仍然通过@Configuration类作为项目的配置主类
@Configuration
public class MyConfig implements WebMvcConfigurer {
    
    //ViewResolver实现了视图解析器接口的类,我们就可以看做视图解析器
    @Bean
    public ViewResolver myViewResolver(){
        return new MyViewResolver();
    }
    //自定义视图解析器
    public static class MyViewResolver implements ViewResolver{
        @Override
        public View resolveViewName(String viewName, Locale locale) throws Exception {
            return null;
        }
    }
}
```

自定义某些数据格式: `src\main\resources\application.yaml`

```yaml
#自定义日期格式
spring:
  mvc:
    format:
      date: dd/MM/yyyy
```

自定义视图跳转:`src\main\java\com\zhang\springbootdemo\config\MyConfig.java`

```java
//扩展MVC配置
//@Configuration相当于传统的xml配置文件，如果有些第三方库需要用到xml文件，建议仍然通过@Configuration类作为项目的配置主类
@Configuration
public class MyConfig implements WebMvcConfigurer {
    //自定义视图跳转
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/kuang").setViewName("test");
    }
}
```

