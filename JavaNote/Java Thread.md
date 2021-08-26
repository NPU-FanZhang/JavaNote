# Java Thread

[TOC]

## 进程-Process

​	`进程（Process）`是计算机中的程序关于某数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，是操作系统结构的基础。

​	程序是指令、数据及其组织形式的描述，进程是程序的实体。

​	狭义定义：进程是正在运行的程序的实例（an instance of a computer program that is being executed）。

​	广义定义：进程是一个具有一定独立功能的程序关于某个数据集合的一次运行活动。它是操作系统动态执行的 `基本单元`，在传统的 操作系统 中，进程既是基本的 分配单元 ，也是基本的执行单元。

​	**进程是资源分配的最小单位，线程是CPU调度的最小单位**

## 线程-Thread

​	`线程（thread）`是操作系统能够进行运算调度的最小单位。它被包含在进程之中，是进程中的实际运作单位。

​	线程是调度和执行的基本单位。

## 进程和线程的区别

做个简单的比喻：进程=火车，线程=车厢

- 线程在进程下行进（单纯的车厢无法运行）
- 一个进程可以包含多个线程（一辆火车可以有多个车厢）
- 不同进程间数据很难共享（一辆火车上的乘客很难换到另外一辆火车，比如站点换乘）
- 同一进程下不同线程间数据很易共享（A车厢换到B车厢很容易）
- 进程要比线程消耗更多的计算机资源（采用多列火车相比多个车厢更耗资源）
- 进程间不会相互影响，一个线程挂掉将导致整个进程挂掉（一列火车不会影响到另外一列火车，但是如果一列火车上中间的一节车厢着火了，将影响到所有车厢）
- 进程可以拓展到多机，进程最多适合多核（不同火车可以开在多个轨道上，同一火车的车厢不能在行进的不同的轨道上）
- 进程使用的内存地址可以上锁，即一个线程使用某些共享内存时，其他线程必须等它结束，才能使用这一块内存。（比如火车上的洗手间）－"互斥锁"
- 进程使用的内存地址可以限定使用量（比如火车上的餐厅，最多只允许多少人进入，如果满了需要在门口等，等有人出来了才能进去）－“信号量”





## 线程创建

线程创建的三种方法：

- 继承Thread类。
- 实现Runnable接口。
- 实现Callable接口。

```
	When code running in some thread creates a new {@code Thread} object, the new thread has its priority initially set equal to the priority of the creating thread, and is a daemon thread if and only if the creating thread is a daemon.
```



### 继承Thread类

- 子类继承Thread类从而可以进行多线程实现。
- 启动线程 `thread.start()`
- ==不建议使用==--避免OOP单继承的局限性。

```java
/*
* 创建线程方法一：继承Thread
* 重写run()方法，调用start开启线程。
* */
public class Thread1 extends Thread {
    @Override
    public void run() {
        //run方法的线程体
        //super.run();
        for (int i = 0; i < 10; i++) {
            System.out.println("Son Thread -- "+i );
        }
    }

    public static void main(String[] args) {
        Thread1 thread1 = new Thread1();
        //调用 start 方法开启线程，会和main线程同时执行。
        //但是线程不一定立马执行，由CPU调度。
        thread1.start();
        //调用 run 方法会先执行 thread, 再执行main剩下的部分。
        thread1.run();
        for (int i = 0; i < 10; i++) {
            System.out.println("Main Thread -- "+ i);
        }
    }
}
```



### 实现Runnable接口

- 实验runnable接口，传入Thread对象以实现多线程。
- 启动线程： 传入目标thread对象，`new thread(runnable).start()`。
- ==推荐使用==--可以避免单继承的局限性，灵活方便。

```java
public class ThreadRunnableBuild implements Runnable {
    //必须重写run方法
    /*
     * runnable 接口中就一个 run()方法
     * 推荐使用runnable接口
     * */
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("Son Thread -- " + i);
        }
    }

    public static void main(String[] args) {
        // 创建Runnable接口实现类的对象
        ThreadRunnableBuild threadRunnableBuild1 = new ThreadRunnableBuild();
        ThreadRunnableBuild threadRunnableBuild2 = new ThreadRunnableBuild();
        ThreadRunnableBuild threadRunnableBuild3 = new ThreadRunnableBuild();
        // 创建线程对象来启动线程，是一个代理
        Thread thread = new Thread(threadRunnableBuild1);
        thread.start();
        // 等价于new Thread(threadRunnableBuild).start();
        // 可以传入不同的 接口实现类
        new Thread(threadRunnableBuild1).start();
        new Thread(threadRunnableBuild2).start();
        new Thread(threadRunnableBuild3).start();

        for (int i = 0; i < 10; i++) {
            System.out.println("main Thread -- " + i);
        }
    }
}
```



