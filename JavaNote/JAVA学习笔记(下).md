# JAVA学习笔记（下）



[TOC]

## 面向对象

> OOP(Object-Oriented Programming)
>
> 面向对象的本质是：以类的方式组织代码，以对象的形式(封装)数据。
>
> 面向对象的核心思想：抽象。
>
> 面向对象的三大特性：封装、继承、多态。
>
> 对象是通过引用来操作的。栈---->堆



### 类和对象、构造器 

**基本语法及内存情况**

main方法

```java
// 1.一开始，main方法在栈底
public static void main(String[] args) {
    /**
     * 使用new关键字创建，本质在调用构造器
     * 同时会分配内存空间，还会进行默认初始化，并调用类中的构造器。
     * 必须存在构造器，如果没有自定义构造器，会自动生成默认的构造器，
     */
    Student student = new Student();//2.new调用构造器，student(引用变量名)对象压栈，指向堆内存中的构造的对象。
    student.study();//3.由栈内存中的引用变量名调用堆中的方法区的方法
    student.say();
}
```

Student类

```java
public class Student {
    String name;
    int age;
    
    //构造器
    //alt+inert会自动生成构造器
    //与类名相同，不可以有返回值，也不可以为void
    public Student(){ }
    //有参构造器，如果存在有参构造器，必须显式声明无参构造器
    public Student(int age,String name){
        this.age = age;
        this.name = name;
    }
    public void study(){
        System.out.println("Student learning...");
    }
    public static void say(){
        System.out.println("Student talked...");
    }
}
```



### 封装

> 高内聚，低耦合。
>
> 私有属性：get/set



### 继承 `extends`

> Java中只有单继承，没有多继承。
>
> Ctrl+H 打开类继承树。
>
> 所有的类都默认直接过间接继承Object类

Person类（父类）：

```java
public class Person {
    public Person(){
        System.out.println("Person constructor");
    }
    protected String desc = "person";
    public void print()
    {
        System.out.println("Person print");
    }
}
```

Student类（子类）：

```java
//子类不能继承父类的private方法或属性
public class Student extends Person {
    private String desc = "Student";
    //构造器
    public Student() {
        //隐藏代码
        //如果父类构造器有参，就得显式的调用
        super();//调用父类构造器，必须在子类构造器第一行
        
        System.out.println("Student constructor");
    }
    public  void test1(String desc){
        System.out.println(desc);
        System.out.println(this.desc);
        System.out.println(super.desc);
        //son
		//Student
		//person
    }
    public void print(){
        System.out.println("Student Print");
    }
    public  void test2(){
        print();
        this.print();
        super.print();
        //Student Print
		//Student Print
		//Person print
    }
}
```

mian函数：

```java
public static void main(String[] args) {
    Student student = new Student();
    //Person constructor
	//Student constructor
    student.test1("son");
    //son
	//Student
	//person
    student.test2();
    //Student Print
	//Student Print
	//Person print
}
```

#### **`super`**

> super()调用父类的构造方法。
>
> super只能出现在子类的方法中。
>
> super()和this()不能同时调用构造方法。



#### 方法重写

> 重写需要由继承关系，方法名必须相同，参数列表必须相同，修饰符范围可以扩大，但不能缩小。
>
> 抛出的异常：范围可以缩小，但不能放大。ClassNotFoundException --> Exception(大)

**静态方法重写：**

> 静态方法的重写，方法调用只与定义的数据类型有关（左边）

```java
public static void main(String[] args) {
    Son son = new Son();
    son.test();//Son Test
    Father father = new Son();
    father.test();//Father Test
    //向上转型了
}
```

```java
public class Father {
    public static void test(){
        System.out.println("Father Test");
    }
}
```

```java
public class Son extends Father {
    public  static  void  test()
    {
        System.out.println("Son Test");
    }
}
```

**非静态方法重写：**

```java
public static void main(String[] args) {
    Son son = new Son();
    son.test();//Son Test
    Father father = new Son();
    father.test();//Son Test
}
```

```java
public class Father {
    public  void test(){
        System.out.println("Father Test");
    }
}
```

```java
public class Son extends Father {
    @Override//注解，有功能的注释
    public void test() {
        System.out.println("Son Test");
    }
}
```



### 多态

> 实现动态编译。
>
> 多态存在的条件 
>
> - 有继承关系
>
> - 子类重写父类方法
>
> - 父类引用指向子类对象
>
> 
>



```java
public class Father {
    public  void test(){
        System.out.println("Father Test");
    }
}
```

```java
public class Son extends Father {
    @Override//注解，有功能的注释
    public void test() {
        System.out.println("Son Test");
    }
    public void eat(){
        System.out.println("Son Eat");
    }
}
```

```java
public static void main(String[] args) {
    //一个对象的实际类型是确定的
    //但可以指向的引用类型就不确定了
    //父类的引用指向子类
    Son son1 = new Son();
    Father son2 = new Son();
    Object son3 = new Son();

    //对象能执行那些方法，主要看左边的对象类型，和右边关系不大
    son1.eat();//Son Eat
    ((Son)son2).eat();//Son Eat

    //Son 未重写test方法
    son1.test();//Father Test
    son2.test();//Father Test
    //重写test方法
    son1.test();//Son Test
    son2.test();//Son Test
}
```

