# Java进阶学习笔记（上）



## 注解Annotation

> 注解可以被人理解，也可以被程序读取。
>
> 本身不是程序，但可以被程序所读取（通过反射读取），也可以对程序进行解释。

常见的注解：
```java
//注解存在检查和约束，需要注意规范
@FunctionalInterface //说明后面方法为函数性接口
@Deprecated //已废弃或不推荐使用
@SuppressWarnings(value = "msg")//镇压警告，带参数的注解
@Override //重写的注解，修饰性注解
```
使用方法：

```java
@Override //重写的注解，修饰性注解
public String toString() {
    return super.toString();
}
```

### 元注解 meta-annotation

```java
//    @Target()//描述注解的使用范围
//    @Retention()//表示在说明什么级别保存该注释信息。（SOUTRCE < CLASS < RUNTIME）
//    @Documented //是否将注解生成文档
//    @Inherited  //表示子类可以继承父类的注解
```

自定义一个注解：

```java
//@interface自定义一个注解
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@interface myAnnotation1{
    //注解的参数： 参数类型 + 参数名（）；
    String name() default "ZF4N";
    int age();
}
//如果没有默认值，必须赋值
@myAnnotation1(name = "zhang",age = 18)
public void test(){
}
```



## 反射机制Reflection

