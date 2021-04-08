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

> 反射机制是在运行状态中，
>
> 对于任意一个类，都能够知道这个类的所有属性和方法；
>
> 对于任意一个对象，都能够调用它的任意一个方法和属性；
>
> 这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。

> 可以实现代码的动态编译，但是会牺牲性能。
>
> 由于class文件的字节码内容加载到内存中，并将静态数据转换成方法区的运行时数据结构，然后生成一个代表这个类的`Java.lang.Class`对象，因此**Class对象只能获取**，因为它是加载到内存之中才形成的。

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

所有类型的CLass：

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

### 类内存加载机制

> 类加载顺序主要为: 加载--> 链接 --> 初始化

> Java内存分为两类：
>
> - 栈内存 基本类型 局部变量
> - 堆内存 new的对象都在堆内存中
>   - 一般堆
>   - 方法区 class，static数据，代码等



```java
public static void main(String[] args) {
    /*
     1.字节码文件加载到内存，，将静态数据转换成方法区的运行时数据结构
     产生一个类对应的Class对象
     2.链接，将Java类的二进制代码合并到JVM的运行状态之中的过程。
        验证是否符合规范、
        为static分配内存并设置初始值、
        解析将常量名(符号引用)更换为直接引用(地址)
     3.初始化，执行类构造器<clinit>()方法，并触发父类的初始化，
     虚拟机会保证一个类的<clinit>()方法在多线程环境中被加锁和同步。
     */
    A a = new A();//先输出 A Class static code 再 A constuctor
    System.out.println(A.m);//100  m的值与类中静态代码块和m初始化位置的先后有关

}
```
```java
class A{

    static {
        System.out.println("A Class static code");
        m =  300;
    }
    static int m = 100;
    public A()
    {
        System.out.println("A constuctor");
    }
}
```

### 类初始化

> 类会初始化的情况
>
> - JVM启动时，初始化`main`方法所在的类
> - `new`一个类的对象
> - 调用类的静态成员（`final`常量除外）和静态方法。
> - 使用Java.lang.reflect包的方法对类进行反射调用
> - 当初始化一个类，如果他的父类没有初始化，会先初始化他的父类

> 类不会初始化的情况：
>
> - 访问一个静态域时，只有真正声明这个域的类才会被初始化。例如当通过子类引用父类的静态变量，不会导致子类初始化。
> - 通过数组定义类引用，不会触发此类的初始化
> - 引用常量不会触发此类的初始化（常量在链接阶段就存入调用类的常量池中了）



### 类加载器

```java
public class Loader {
    public static void main(String[] args) throws ClassNotFoundException {
        //获取系统类加载器 （AppClassLoader）
        ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();//获取系统类加载器
       	System.out.println(systemClassLoader);
        //jdk.internal.loader.ClassLoaders$AppClassLoader@3fee733d

        //获取系统类加载器的父类加载器--扩展加载器 （PlatformClassLoader）
        ClassLoader parent = systemClassLoader.getParent();
        System.out.println(parent);
        //jdk.internal.loader.ClassLoaders$PlatformClassLoader@723279cf

        //获取扩展类加载器的父类加载器--根（核心）加载器（c/c++编写的，也是核心加载器，无法直接获取）
        ClassLoader parent1 = parent.getParent();
        System.out.println(parent1);//null无法获取

        //测试当前类时哪个加载器加载的 --系统类加载器（AppClassLoader）
        ClassLoader classLoader = Class.forName("com.Reflect.Loader").getClassLoader();
        System.out.println(classLoader);
        //jdk.internal.loader.ClassLoaders$AppClassLoader@3fee733d

        //测试JDK --根加载器加载
        ClassLoader classLoader1 = Class.forName("java.lang.Object").getClassLoader();
        System.out.println(classLoader1);//null 根加载器加载
    }
}
```



### 类对象的结构

> 可以获得类的名称、方法、属性、以及构造器等等。

```java
//获取Class对象的属性
Class aClass = Class.forName("com.company.Person");
System.out.println(aClass.getName());//com.company.Person
System.out.println(aClass.getSimpleName());//Person
//获得类的属性
Field[] fields = aClass.getFields();//只能找到public属性
System.out.println("------------");
fields = aClass.getDeclaredFields();
for (Field field : fields) {
    System.out.println(field);
    //输出：
    //java.lang.String com.company.Person.name
    //int com.company.Person.age
    //char com.company.Person.gender
}
//获取指定的属性
Field name = aClass.getDeclaredField("name");
System.out.println(name);
//java.lang.String com.company.Person.name
//获取类的方法
System.out.println("===================");
Method[] methods = aClass.getMethods();//获得本类及其父类的全部public方法
for (Method method : methods) {
    System.out.println(method);
}
//public java.lang.String com.company.Person.toString()
//public final native void java.lang.Object.wait(long) throws java.lang.InterruptedException
//public final void java.lang.Object.wait(long,int) throws java.lang.InterruptedException
//public final void java.lang.Object.wait() throws java.lang.InterruptedException
//public boolean java.lang.Object.equals(java.lang.Object)
//public native int java.lang.Object.hashCode()
//public final native java.lang.Class java.lang.Object.getClass()
//public final native void java.lang.Object.notify()
//public final native void java.lang.Object.notifyAll()

System.out.println("----------");//获得本类的方法
methods = aClass.getDeclaredMethods();
for (Method method : methods) {
    System.out.println(method);
}
//public java.lang.String com.company.Person.toString()

//获取指定的方法
System.out.println("++++++++++++++++++++++++++");
//由于方法存在重载，所以获得指定方法时需要指定参数列表，即除过方法名外的第二个参数
Method methods1 = aClass.getMethod("toString",null);
System.out.println(methods1);
//public java.lang.String com.company.Person.toString()

//获得构造器
Constructor[] constructors = aClass.getConstructors();
Constructor[] declaredConstructors = aClass.getDeclaredConstructors();
Constructor declaredConstructor = aClass.getDeclaredConstructor(String.class, int.class, char.class);

System.out.println(declaredConstructor);
//public com.company.Person(java.lang.String,int,char)
```



### 动态创建对象（即通过类对象创建对象）

> 1. 先获得`class对象`
> 2. 这个`class对象`通过`newInstance` 创建一个对应类的对象
> 3. 通过`class对象`的`getDeclaredMethod` `getDeclaredField`方法来获得方法和属性
> 4. 通过`invoke` set`来激活方法`
> 5. 调用方法

```java
public static void main(String[] args) throws ClassNotFoundException, IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException, NoSuchFieldException {
    //获得class对象
    Class c1 = Class.forName("com.Reflect.Person");
    //构造一个对象
    Person person = (Person) c1.newInstance();//本质调用无参构造器, 没有无参构造器会报错
    System.out.println(person);//Person{name='null', age=0, gender= }

    //通过构造器构造对象
    Constructor declaredConstructor = c1.getDeclaredConstructor(String.class, int.class, char.class);
    Person zhang = (Person) declaredConstructor.newInstance("Zhang", 15, '男');
    System.out.println(zhang);//Person{name='Zhang', age=15, gender=男}

    //通过反射获取方法
    Method toString = c1.getDeclaredMethod("toString", null);

    //invoke-激活,反射获取的方法要激活才能使用
    toString.invoke(zhang,null);
    System.out.println(zhang.toString());//Person{name='Zhang', age=15, gender=男}

    //反射操作属性,不能直接操作私有属性，得关闭权限检查
    Person fan = (Person) c1.newInstance();
    Field name = c1.getDeclaredField("name");
    //关闭权限检查，不然对private属性访问会报错
    //cannot access a member of class com.Reflect.Person with modifiers "private"
    name.setAccessible(true);
    name.set(fan,"fan");
    System.out.println(fan.getName());//fan
}
```