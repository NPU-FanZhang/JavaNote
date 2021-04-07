

#  JAVA学习笔记



## 基础知识

### JDK、JRE、JVM

**JDK：Java Development Kit**

**JRE：Java Runtime Environment**

**JVM：Java Virtual Machine**

### java环境安装

环境变量：**JAVA_HOME**：jdk安装路径

**PATH**：%JAVA_HOME%\bin和%JAVA_HOME%\jre

检查方法 cmd：

`java -version`

```  java
java version "13.0.1" 2019-10-15
Java(TM) SE Runtime Environment (build 13.0.1+9)
Java HotSpot(TM) 64-Bit Server VM (build 13.0.1+9, mixed mode, sharing)
```

`javac -version`

```java
javac 13.0.1
```



------

## 规范

### 包名规范

一般利用公司域名倒置作为包名

baidu.com对应的包名应为com.baidu

`import` 引入包名

```java
//package必须写在import之前
package com.baidu.operator;
import java.util.Date;
import java.util.*;//通配符，导入util中所有的包
```

### JavaDoc

javadoc命令是用来生成自己的API文档的。

```c
E:\JavaProject\First\Test\src\com\baidu\operator>javadoc -encoding  UTF-8  -charset UTF-8 Demo1.java
```

```java
package com.baidu.operator;

/**
 * @author F4N
 * @version 1.0
 * @since 13
 */
public class Demo1 {
    /**\
     * @author
     * @param name
     * @return
     * @throws Exception
     */
    public String test(String name) throws Exception{
        return name;
    }
```



------

## 输入简写

**psvm**

```java
public static void main(String[] args) {
    
}
```

**sout**

```java
System.out.println();
```

**java文档注释 JavaDoc **

``` java
/**
 * @Description Frist
 * @author F4N
 */
```

**`100.for`**

```java
for (int i = 0; i < 100; i++) {}
```

**`array.for`**

```java
for (int i : array) {}
```

**alt + enter补全对象声明**

```java
new Method();// alt + enter 自动补全
Method method = new Method();
```




## 基础语法

### 数据类型

> 浮点类型是**有限 离散 存在舍入误差的**
>
> **避免使用浮点数进行比较运算**

**进制转换**

> 低------------------------------------------------------------> 高
>
> byte short char --> int --> long-->float --> double 
>
> - 高精度转低精度会强制转换 （会出现内存溢出和精度问题）
>- 低精度转高精度会自动转换
> - 不能对布尔类型进行转换
> - byte short char 运算后自动转为int

**基础类型：**

``` java
     	//整型
        byte num1 = 10; //1字节Byte -128-127
        short num2 = 10; //2字节
        int num3 = 10; //4字节
        long num4 = 30L;//8字节，long类型要在数字后面加L
        //浮点型
        float num5 = 3.14F;//4字节，float类型要在数字后面加F
        double num6 = 3.141592656456123456;//8字节
        //字符型
        char num7 = '中';//2字节
        //字符串不是关键字，是类
        //String  str = "Fuck";
        //布尔值
        boolean num8 = true;//1位 1bit
```

**进制**:

``` java
		int num1 = 10;// 十进制 10
        int num2 = 010;// 八进制 8
        int num3 = 0x10;//十六进制 16
```

**强制转换**

```java
        char a = 'a';
        System.out.println(a);//a
        System.out.println((int)a);//97
        char b = '中';
        System.out.println(b);//中
        System.out.println((int)b);//20013
        //unicode编码 2个字节 字符本质都是数字
        char c = '\u0061';
        System.out.println(c);//a
        System.out.println((int)c);//97
```

**字符串比较**

``` java
		String str1 = new String("hello");
        String str2 = new String("hello");
        System.out.println(str1 == str2);//false 比较地址
        String str3 ="hello";
        String str4 ="hello";
        System.out.println(str3 == str4);//true 字符池中
```

### 变量

> 变量分为类变量、实例变量、局部变量。

``` java
public class Demo1 {
    //类变量:属于类 可以直接应用
    static int num1 = 1;
    //实例变量：从属于对象
    String str ;//null，除过基本类型，其余均为null
    int age;//默认0
    boolean flag;//false
    
    public static void main(String[] args) {
        
        System.out.println(num1);
        //局部变量:必须声明和初始化
        int num2 = 1;
        
        Demo1 demo1 = new Demo1();
        System.out.println(demo1.str);
        System.out.println(demo1.age);
        System.out.println(demo1.flag);
        
    }
}
```

**常量**

> 关键字 `final` 不区分先后

``` java
static final double pi1 = 3.14;
final static double pi2 = 3.14;
```

### 运算符

**++ 和 --**

``` java
int a = 3;
int b = a++; //先给b赋值，后a+1
//b = 3 , a = 4
int c = ++a; //先给b赋值，后a+1
//c = 5 , a = 5
```

**短路运算**

```java
//短路运算
int c = 5;
boolean d = (c< 5) && (c++<4);
System.out.println(c);// 5 未执行上一行的c++
System.out.println(d);// false
```

**位运算**

``` 
&----------位与     ~----------位取反
|----------位或     ^----------位异或
<<---------左移 *2   >> --------右移/2 
```

**字符串连接**

```java
int a = 10;
int b = 20;
System.out.println(""+a+b);//1020
System.out.println(a+b+"");//30
```



## 流程控制

`Scanner`

```java
import java.util.Scanner;

Scanner scanner = new Scanner(System.in);//输入I am Zhang Fan
System.out.println("使用next接收：");
if(scanner.hasNext()){
    String str  =scanner.next();
    System.out.println("输出内容为："+str);//输出内容为：I, next不能得到带有空格的字符串
}
if(scanner.hasNextLine()){
    String str  =scanner.nextLine();
    System.out.println("输出内容为："+str);//输出内容为： am Zhang Fan ,nextLine()以回车为结束符
}
//IO流的类如果不关闭会一直占用资源
scanner.close();
```

**字符串比较**

```java
str.equals(str1)
```

**`switch`**

> **JDK7**以后可以匹配字符串，原理是通过hashCode转换成数字。

```java
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入内容：");
        String str =  scanner.next();
        switch (str){
            case "10":
                System.out.println("fuck 10");
                break;
            case "20":
                System.out.println("fuck 20");
                break;
            default:
                System.out.println("other");
                break;
        }
```

> 反编译后的代码，对应的.class字节码文件

```java
Scanner scanner = new Scanner(System.in);
System.out.println("请输入内容：");
String str = scanner.next();
byte var4 = -1;
switch(str.hashCode()) {//hash过程
case 1567:
    if (str.equals("10")) {
        var4 = 0;
    }
    break;
case 1598:
    if (str.equals("20")) {
        var4 = 1;
    }
}
switch(var4) {
case 0:
    System.out.println("fuck 10");
    break;
case 1:
    System.out.println("fuck 20");
    break;
default:
    System.out.println("other");
}
```

**`for`**

```java
//9*9乘法表
public static void main(String[] args) {
    for (int i = 1; i < 10; i++) {
        for (int j = 1;j<=i;j++){
            System.out.print(j+" * "+ i+" = " + i * j +'\t');
        }
        System.out.println();
    }
}
```

```java
//增强for循环，针对数组和集合类型，方便操作，JDK5引入
int[] number = {10,20,30,40,50};
for (int x:number) {
    System.out.println(x);
}
```

**`break` `continue`**

> `break`关键字终止当前循环。
>
> `continue`关键字跳过这一次循环后面的语句，执行下一次循环。



---

## 方法（函数）

> 方法包含于对象或类之中。
>
> Java是值传递，但是也有引用传递，传参为对象类型时才是引用传递，传递的时对象的地址。

```java
//修饰符 返回值类型 方法名（参数类型 参数名）{
//      方法体
//      return 返回值
//	}
public class Method {
    public static void main(String[] args) {
        System.out.println(add(10,20));
    }
    public static int add(int a, int b){
        return a + b;
    }
}
```

### 方法重载

> 重载是指在同一个类中，方法同名不同参的情况，与返回值无关。
>
> 重载方式：参数个数不同、参数类型不同、参数的顺序不同。

```java
public static void main(String[] args) {
    System.out.println(add(10, 20));//30
    System.out.println(add(10.1, 20.1));//30.200000000000003
}
public static int add(int a, int b){
    return a + b;
}
public static double add(double a, double b){
    return a + b;
}
```

**可变参数（不定项参数）**

> 一个方法中，只能指定一个可变参数，并且必须为最后一个参数。

```java
   public static void main(String[] args) {
        Method method = new Method();// alt + enter 自动补全
        method.add(1,2,3,4);
    }
    public void add(int...i){
        for (double x : i){
            System.out.println(x);
        }
    }
```

### **递归**

> 阶乘

```java
public static  int fun(int n){
    if(n == 1){
        return  1;
    }
    else{
        return fun(n-1)*n;
    }
}
```



## 数组

```java
//动态初始化
int[] nums1;//声明  --> 此时nums1变量在栈内存中
nums1 = new int[10];//分配空间 --> 此时nums1变量的内容在堆内存中开辟了空间
for (int i = 0; i < nums1.length; i++) {
    nums1[i] = i*i;
    System.out.println(nums1[i]);
}
//静态初始化，空间初始化后固定
int[] nums2 = {5,10,50,30};
```

> 不能用增强for循环初始化。

```java
//赋值失败
//num1.for快捷输入
for (int x : nums1) {
    x = 10;
}
```

**二维数组**

```java
int[][] array1 ={{1,2},{3,5},{5,7},{3,0}};
System.out.println(array1);//[[I@723279cf hashCode
System.out.println(array1[0]);//[I@10f87f48 hashCode
System.out.println(array1.length);//4 输出的是第一维数量
System.out.println(array1[0].length);//2 输出第二维数量
```

**Arrays类**

```java
//Arrays类
//nums1 = [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
//int[] nums2 = {0, 2, 2, 2, 2, 25, 36, 49, 64, 81};
Arrays.fill(nums1,1,5,2);//填充数组，可以加区间，左闭右开
Arrays.sort(nums1); //升序排序
System.out.println(Arrays.equals(nums1,nums2));//比较数组 返回true或false
System.out.println(Arrays.toString(nums1));//打印数组
System.out.println(Arrays.binarySearch(nums1,0));//未查找到输出负数，查找到输出下标
```

