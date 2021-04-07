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

> 可以实现代码的动态编译，但是会牺牲性能。
>
> 由于class文件的字节码内容加载到内存中，并将静态数据转换成方法区的运行时数据结构，然后生成一个代表这个类的`Java.lang.Class`对象，因此Class对象只能获取，因为它是加载到内存之中才形成的。

```java
// 实体类 pojo,entity
class Person{
     String name;
     int age;
     char gender;
    public Person() { }

    public Person(String name, int age, char gender) {
        this.name = name;
        this.age = age;
        this.gender = gender;
    }
    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", gender=" + gender +
                '}';
    }
}
class Student extends Person{
    private String id;

    public Student() {
        this.name = "Student";
    }
}
```
反射获取类的Class对象的方式：

```java
public static void main(String[] args) throws ClassNotFoundException {
        //通过反射获取类的class对象
        Class c1 = Class.forName("com.company.Person");
        Class c2 = Class.forName("com.company.Person");
        System.out.println(c1);//class com.company.User
        //------------------------------------------------------
        //一个类在内存中只有一个class对象
        //一个类被加载后，类的整个结构都会被封装在class对象中
        System.out.println(c1.hashCode());//1915910607
        System.out.println(c2.hashCode());//1915910607
        //------------------------------------------------------
        //获取class类的创建方法，以Students为例
        Person person = new Student();
        System.out.println("this man is "+person.name);//this man is Student
        //方式一 通过对象获得
        Class c3 = person.getClass();
        System.out.println(c3.hashCode());//284720968
        //方式二 forName获取
        Class c4 = Class.forName("com.company.Student");
        System.out.println(c4.hashCode());//284720968
        //方式三 类名.class获取
        Class c5 = Student.class;
        System.out.println(c5.hashCode());//284720968
        //方式四 基本类型的包装类都有一个TYPE
        Class c6 = Integer.TYPE;
        System.out.println(c6.hashCode());//284720968
        //------------------------------------------------------
        //获得父类类型
        Class c7 = c3.getSuperclass();
        System.out.println(c7.hashCode());//1915910607 与c1 一致
}
```

所有类型的CLass

```java
//所有类型的Class
Class c8 = Object.class;//类
Class c9 = Comparable.class;//接口
Class c10 = String[].class;// 一维数组
Class c11 = int[][].class;//二维数组
Class c12 = Override.class;//注解
Class c13 = ElementType.class;//枚举
Class c14 = Integer.class;//基本数据类型
Class c15 = void.class;//void
Class c16 = Class.class;//Class
System.out.println(c8);//class java.lang.Object
System.out.println(c9);//interface java.lang.Comparable
System.out.println(c10);//class [Ljava.lang.String;
System.out.println(c11);//class [[I
System.out.println(c12);//interface java.lang.Override
System.out.println(c13);//class java.lang.annotation.ElementType
System.out.println(c14);//class java.lang.Integer
System.out.println(c15);//void
System.out.println(c16);//class java.lang.Class

int[] a = new int[10];
int[] b =  new int[100];
int[][] c = new int[2][2];
System.out.println(a.hashCode());//883049899
System.out.println(b.hashCode());//2093176254
System.out.println(c.hashCode());//1854731462
//元素类型与维度一致，就是同一个Class
System.out.println(a.getClass().hashCode());//317574433
System.out.println(b.getClass().hashCode());//317574433
System.out.println(c.getClass().hashCode());//885284298
```

