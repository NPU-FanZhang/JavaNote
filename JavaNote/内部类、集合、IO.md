# 内部类、集合、IO



## 内部类

内部类就是`在一个类的内部再定义一个完整的类`。

特点：

- 编译后会生成独立的字节码文件。

  ![image-20210902110904048](asset/内部类、集合、IO.assets/image-20210902110904048.png)

- 内部类可以直接访问外部类的私有成员，而不破坏封装。

- 可以为外部类提供必要的内部功能组件。

- 创建内部类对象时，必须依赖外部类对象。

- 内部类也可以有访问修饰符，private、public



内部类包含： 

- 成员内部类

  

- 静态内部类

- 局部内部类

- 匿名内部类





## I/O

### 1. IO理解分类 - 从传输方式上

从数据传输方式或者说是运输方式角度看，可以将 IO 类分为:

- 字节流
- 字符流

`字节`是个计算机看的，`字符`才是给人看的

### 2. 字节流和字符流的区别

- 字节流读取单个字节，字符流读取单个字符(一个字符根据编码的不同，对应的字节也不同，如 UTF-8 编码是 3 个字节，中文编码是 2 个字节。)
- 字节流用来处理二进制文件(图片、MP3、视频文件)，字符流用来处理文本文件(可以看做是特殊的二进制文件，使用了某种编码，人可以阅读)。



<img src="asset/%E5%86%85%E9%83%A8%E7%B1%BB%E3%80%81%E9%9B%86%E5%90%88%E3%80%81IO.assets/image-20220311172058164.png" alt="image-20220311172058164" style="zoom:50%;" />

```java
/*
 *  流的体系结构
 *  抽象基类             节点流                缓存流
 *  InputStream         FileInputStream     BufferedInputStream
 *  OutputStream        FileOutputStream    BufferedOutputSteam
 *  Reader              FileReader          BufferedReader
 *  Writer              FileWriter          BufferedWriter
 * */
```

### 3. FileReader

```java
// 测试 FileReader
// 为了保证资源一定可以被关闭,使用try-catch-finally来关闭资源
public static void testFileReader() {
    FileReader fr = null;
    try {
        // 1. 实例化File类对象
        File file = new File("Interview\\hello.txt"); // 路径相较于当前工程
        // 2. 提供具体的流
        fr = new FileReader(file);
        // 3. 数据的读入过程
        // RETURN The character read, or -1 if the end of the stream has been reached
        // 返回一个读入的字符,如果到达文件末尾返回-1
        int data;
        while ((data = fr.read()) != -1) {
            System.out.print((char) data);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            // 4. 关闭流操作
            // JVM 虽然有垃圾回收机制,但对于物理连接无能为力,比如: 数据库连接、输入输出流、Socket
            if (fr != null) fr.close();
            // 因为 fr可能在前面由于打开失败而为null,不能直接close
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

```java
public static void testFileReader1() {
    FileReader fr = null;
    try {
        File file = new File("Interview//hello.txt");
        fr = new FileReader(file);
        char[] cBuf = new char[5];
        // RETURN   The number of characters read,
        //          or -1 if the end of the stream has been reached
        int len;
        while ((len = fr.read(cBuf)) != -1) {
            //错误写法1
            //System.out.println(Arrays.toString(cBuf));//
            //错误写法2
            //for (int i = 0; i < cBuf.length; i++) {
            //   System.out.print(cBuf[i]);
            //}
            // 正确1
            for (int i = 0; i < len; i++) {
                System.out.print(cBuf[i]);
            }
            // 正确2
            String s = new String(cBuf, 0, len);
            System.out.print(s);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (fr != null) fr.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 4. FileWriter



























































