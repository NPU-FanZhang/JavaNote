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

```java
	// 从内存中写出数据到磁盘的文件里。
	/*
     *  输出操作 对应File可以不存在,会自动创建,不会爆异常
     *          如果存在,可以根据第二个参数确定是否追加,默认不追加,进行覆盖
     *          FileWriter fw = new FileWriter(file,false/true);
     * */
public static void TestFileWriter() throws IOException {

    FileWriter fw = null;
    try {
        File file = new File("Interview//IO//helloWriter.txt");
        System.out.println(file.getAbsolutePath());
        // file文件本身没有写出的能力,
        fw = new FileWriter(file, false);
        fw.write("Fuck F4N!");
        fw.write("2022-3-11");
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        if (fw != null) fw.close();
    }
}
```

### 5. 复制操作

```java
// 对文件的读写,做一个复制操作
public static void TestFileReaderWriter() throws IOException {
    FileReader fr = null;
    FileWriter fw = null;
    try {
        File srcFile = new File("Interview//IO//hello.txt");
        File destFile = new File("Interview//IO//helloWriter.txt");


        fr = new FileReader(srcFile);
        fw = new FileWriter(destFile,true);
        char[] cBuf = new char[5];
        int len;
        while ((len = fr.read(cBuf)) != -1) {
            fw.write(cBuf, 0, len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (fw != null) fw.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

        try {
            if (fr != null) fr.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 6. FileInputOutputStream处理图片

处理图片、视频等需要用字节流，字节流处理字符可能出现乱码问题，尤其是中文。但==实际上，如果只进行复制操作，而在程序中不进行加工，就没什么影响。==

文本文件：.txt .java .c .cpp ...

非文本文件: .jpg .mp3 .doc .ppt ...

```java
 // 使用字节流处理图片
public static void ImgTestFileInputOutputStream   () throws IOException {
    FileInputStream fr = null;
    FileOutputStream fw = null;
    try {
        File srcFile = new File("Interview//IO//testimg.jpg");
        File destFile = new File("Interview//IO//copyimg.jpg");

        fr = new FileInputStream(srcFile);
        fw = new FileOutputStream(destFile);
        byte[] cBuf = new byte[5];
        int len;
        while ((len = fr.read(cBuf)) != -1) {
            fw.write(cBuf, 0, len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (fw != null) fw.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

        try {
            if (fr != null) fr.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 7. 缓冲流

```
BufferedInputStream
BufferedOutputSteam
BufferedReader
BufferedWriter
```

==缓冲流使用要先套接到已有的"流"之上==

```java
BufferedInputStream bis = new BufferedInputStream(new FileInputStream(new File("Interview//IO//testimg.jpg")));
```

如果使用`BufferedReader`和`BufferedWriter`,可以使用String进行读取。

开发会优先使用缓冲流， 因为==缓冲流内部提供了缓冲区== ，速度会快很多。

```java
public static void TestBufferStream() throws IOException {
    /*非文本文件的复制*/
    BufferedInputStream bis = null;
    BufferedOutputStream bos = null;
    try {
        // 造文件
        File srcFile = new File("Interview//IO//testimg.jpg");
        File destFile = new File("Interview//IO//CopyIMG.jpg");
        // 造节点流
        FileInputStream fis = new FileInputStream(srcFile);
        FileOutputStream fos = new FileOutputStream(destFile);
        // 造缓冲流
        bis = new BufferedInputStream(fis);
        bos = new BufferedOutputStream(fos);
        // 简写
        // bis = new BufferedInputStream(new FileInputStream(new File("Interview//IO//testimg" +
        //                    ".jpg")));
        // bos = new BufferedOutputStream(new FileOutputStream(new File("Interview//IO//CopyIMG" +
        //                    ".jpg")));

        byte[] buffer = new byte[10];
        int len;
        while ((len = bis.read(buffer)) != -1) {
            bos.write(buffer, 0, len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        // 资源关闭
        // 先关外层的流,再关内层的流
        bis.close();
        bos.close();
        // 其实,关闭外层流时,会自动关闭内层流,也就是说下面的内层的流可以省略
        // fis.close();
        // fos.close();
    }
}
```

###  8. 统计一个文件中字符出现次数

```java
// 统计 comment.txt中每个字符出现的次数
public static void wordCount() throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(new File("Interview//IO//comment.txt")));
    BufferedWriter bw = new BufferedWriter(new FileWriter(new File("Interview//IO//commentCount.txt")));
    HashMap<Character, Integer> hashMap = new HashMap<>();
    int ch;
    while ((ch = br.read()) != -1) {
        char c = (char) ch;
        hashMap.put(c, hashMap.getOrDefault(c, 0) + 1);
    }
    br.close();
    for (Map.Entry<Character, Integer> en : hashMap.entrySet()) {
        char c = en.getKey();
        switch (c){
            case ' ':
                bw.write("空格 = "+en.getValue());
                break;
            case '\t':
                bw.write("tab = "+en.getValue());
                break;
            case '\r':
                bw.write("回车 = "+en.getValue());
                break;
            case '\n':
                bw.write("换行 = "+en.getValue());
                break;
            default:
                bw.write(c+" = "+en.getValue());
                break;
        }
        bw.newLine();
    }
    bw.close();
}
```

### 9. 转换流

==转换流提供了 字节流 到 字符流 的转换。==

```
/*
 *  转换流:
 *  InputStreamReader ： 将一个字节的输入流 转换为 字符 的输入流
 *  OutputStreamWriter ：将一个字符的输出流 转换为 字节 的输出流
 *  实现了 字节流 到 字符流之间的转换。
 *
 *  从 字节 --> 字符 是一种解码
 *  从 字符 --> 字节 是一种编码
 *
 *  这个过程存在字符集的设置问题
 * */
```

```java
public static void main(String[] args) throws IOException {
    /* 实现字节流到输入流的转换 */
    FileInputStream fis = new FileInputStream("Interview//IO//comment.txt");
    FileOutputStream fos = new FileOutputStream("Interview//IO//commentGBK.txt");
    // 参数二指明字符集,根据读取的文件字符集来确定
    InputStreamReader isr = new InputStreamReader(fis, "UTF-8");
    OutputStreamWriter osw = new OutputStreamWriter(fos, "GBK");
    char[] cbuf = new char[20];
    int len;
    while ((len = isr.read(cbuf)) !   = -1) {
        String s = new String(cbuf, 0, len);
        System.out.print(s);
        osw.write(s);
    }
    isr.close();
    osw.close();
}
```













































