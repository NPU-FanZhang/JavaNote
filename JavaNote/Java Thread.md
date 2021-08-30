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

```java
	When code running in some thread creates a new {@code Thread} object, the new thread has its priority initially set equal to the priority of the creating thread, and is a daemon thread if and only if the creating thread is a daemon.
```

线程创建的三种方法：

- 继承Thread类。
- 实现Runnable接口。
- 实现Callable接口。



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

- 实现runnable接口，传入Thread对象以实现多线程。
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



### 实现Callable接口

五个步骤：

- 实现Callable接口，重写Call方法。
- 实例化 callable实现类的对象
- 创建线程池
- 提交执行，获取结果
- 关闭服务

```java
// 实现 Callable接口有返回值
// 可以抛出异常
public class ThreadCallable implements Callable<Boolean> {
    private String url;
    private String name;

    public ThreadCallable(String url, String name) {
        this.url = url;
        this.name = name;
    }

    //实现Callable接口需要实现call方法
    @Override
    public Boolean call() throws Exception{
        Downloader webDownloader = new Downloader();
        webDownloader.downloader(url, name);
        System.out.println("下载的文件为：" + name);
        return true;
    }

    public static void main(String[] args) throws ExecutionException, InterruptedException {

        ThreadCallable ThreadCallable1 = new ThreadCallable("https://w.wallhaven.cc/full/8o/wallhaven-8oky1j.jpg", "imageDown1.jpg");
        ThreadCallable ThreadCallable2 = new ThreadCallable("https://w.wallhaven.cc/full/e7/wallhaven-e7ek7k.jpg", "imageDown2.jpg");

        // 启动方法不同
        ExecutorService threadPool = Executors.newFixedThreadPool(3);//线程池大小3

        //提交执行
        Future<Boolean> submit1 = threadPool.submit(ThreadCallable1);
        Future<Boolean> submit2 = threadPool.submit(ThreadCallable2);
        //获取结果
        boolean rs1 = submit1.get();
        boolean rs2 = submit2.get();
        //关闭服务
        threadPool.shutdownNow();

    }
}

class Downloader {
    public void downloader(String url, String name) {
        try {
            FileUtils.copyURLToFile(new URL(url), new File(name));
        } catch (IOException e) {
            System.out.println("IO 异常");
            e.printStackTrace();
        }
    }

}
```



## 线程状态

Java中线程的状态比操作系统进程状态稍微细分了一下：

- `New`			--新建状态
- `RUNNABLE`      --可运行状态、就绪状态
- `BLOCKED`       --阻塞状态
- `WAITING`       --等待状态
- `TIMED_WAITING`  --超时等待
- `TERMINATED`     --终止状态

```java
 public enum State {
        /**
         * Thread state for a thread which has not yet started.
         */
        NEW,

        /**
         * Thread state for a runnable thread.  A thread in the runnable
         * state is executing in the Java virtual machine but it may
         * be waiting for other resources from the operating system
         * such as processor.
         */
        RUNNABLE,

        /**
         * Thread state for a thread blocked waiting for a monitor lock.
         * A thread in the blocked state is waiting for a monitor lock
         * to enter a synchronized block/method or
         * reenter a synchronized block/method after calling
         * {@link Object#wait() Object.wait}.
         */
        BLOCKED,

        /**
         * Thread state for a waiting thread.
         * A thread is in the waiting state due to calling one of the
         * following methods:
         * <ul>
         *   <li>{@link Object#wait() Object.wait} with no timeout</li>
         *   <li>{@link #join() Thread.join} with no timeout</li>
         *   <li>{@link LockSupport#park() LockSupport.park}</li>
         * </ul>
         *
         * <p>A thread in the waiting state is waiting for another thread to
         * perform a particular action.
         *
         * For example, a thread that has called <tt>Object.wait()</tt>
         * on an object is waiting for another thread to call
         * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
         * that object. A thread that has called <tt>Thread.join()</tt>
         * is waiting for a specified thread to terminate.
         */
        WAITING,

        /**
         * Thread state for a waiting thread with a specified waiting time.
         * A thread is in the timed waiting state due to calling one of
         * the following methods with a specified positive waiting time:
         * <ul>
         *   <li>{@link #sleep Thread.sleep}</li>
         *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
         *   <li>{@link #join(long) Thread.join} with timeout</li>
         *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
         *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
         * </ul>
         */
        TIMED_WAITING,

        /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */
        TERMINATED;
    }
```

<img src="asset/Java Thread.assets/7e76cc17-0ad5-3ff3-954e-1f83463519d1.jpg" alt="img" style="zoom: 67%;" />

```java
public class ThreadState {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("------");
            }
        });

        Thread.State state = thread.getState();

        System.out.println(state);//NEW

        thread.start();
        state = thread.getState();
        System.out.println(state);//RUNNABLE

        while (state != Thread.State.TERMINATED) {
            Thread.sleep(100);
            state = thread.getState();
            System.out.println(state);
        }
    }
}
NEW
RUNNABLE
TIMED_WAITING
------
TIMED_WAITING
TIMED_WAITING
------
TIMED_WAITING
TIMED_WAITING
------
TIMED_WAITING
TIMED_WAITING
------
TIMED_WAITING
TIMED_WAITING
------
TERMINATED
```



### 线程停止

 * 1.建议线程正常停止-> 不推荐使用死循环等方法，推荐使用次数或标志位等来实现。
 * 2.不推荐使用自带的stop、destroy等过时的JDK方法。

```java
/*
 * 测试stop
 * 1.建议线程正常停止-> 不推荐使用死循环等方法，推荐使用次数或标志位等来实现。
 * 2.不推荐使用自带的stop、destroy等过时的JDK方法。
 * */
public class ThreadStop implements Runnable {
    private boolean flag = true;

    @Override
    public void run() {
        int i = 0;
        while (flag) {
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(" Thread run -- " + i++);
        }
    }

    public void stop() {
        this.flag = false;
    }

    public static void main(String[] args) {
        ThreadStop threadStop = new ThreadStop();
        new Thread(threadStop).start();


        for (int i = 0; i < 15; i++) {
            System.out.println("main -- "+ i);
            if (i == 9){
                threadStop.stop();
                System.out.println("thread stop.");
            }
        }
    }
}
```



### 线程休眠 sleep

- sleep 指定当前线程阻塞的毫秒数。
- sleep 存在异常 `interruptedException`
- sleep 可以模拟网络延迟，倒计时。
- sleep 时间到后，线程进入就绪状态。
- 每一个对象都有一把锁，sleep不会释放锁。

```java
import java.text.SimpleDateFormat;
import java.util.Date;

public class ThreadSleep implements Runnable {
    private static int ticket = 10;

    @Override
    public void run() {
        while (ticket >= 0) {
            try {
                //模拟网络延迟，可以看到线程不安全
                Thread.sleep(20);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "" +
                    " get No." + ticket-- + " ticket");
        }
    }
    public static void main(String[] args) throws InterruptedException {
        ThreadSleep threadSleep = new ThreadSleep();
        new Thread(threadSleep, "Zhang").start();
        new Thread(threadSleep, "Fan").start();
        new Thread(threadSleep, "wang").start();

        //倒计时
        tenDown();
    }

    //模拟倒计时
    public static void tenDown() throws InterruptedException {
        int num = 10;
        while (true) {
            Thread.sleep(1000);
            System.out.println(num--);
            if (num <= 0) {
                break;
            }
        }
    }
}

```



### 线程礼让 yield

让当前正在执行的线程暂停，但不阻塞。将线程从运行状态转为就绪状态。

- 让CPU重新调度，但礼让`不一定成功`，需要看调度策略。

```java
Thread.yield();
```



### 线程强行执行 join

Join 合并线程，等此线程执行完成后，再执行其他线程，在此过程中，其他线程阻塞。

可以理解为`插队`。

```java
public class ThreadJoin implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("Thread is running " + i);
        }
    }

    public static void main(String[] args) throws InterruptedException {

        ThreadJoin threadJoin = new ThreadJoin();
        Thread thread = new Thread(threadJoin);
        thread.start();

        for (int i = 0; i < 100; i++) {
            if (i == 50) {
                thread.join();// 此时main阻塞
            }
            System.out.println("Main is running .." + i);
        }
    }
}
```



## 线程优先级

​	Java提供一个`线程调度器`来监控程序中启动后进入就绪状态的所有线程，线程调度器按照优先级决定应该调度那个线程来执行。

线程的优先级用数字表示，范围 1-10。

- ```java
  Thread.MAX_PRIORITY = 10;
  ```

- ```java
  Thread.MIN_PRIORITY = 1;
  ```

- ```java
  Thread.NORM_PRIORITY = 5;
  ```

使用 `thread.getPriority()` 获取线程优先级。

使用 `thread.setPriority(int x)` 设置线程优先级。

- 先设置优先级再启动。

```java
优先级低只是意味着获得调度的概率低，并不是优先级低就不会被调用了，仍然要看CPU的调度。
```

```java
public class ThreadPriority implements Runnable {
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " --> " + Thread.currentThread().getPriority());
    }

    public static void main(String[] args) {
        ThreadPriority threadPriority = new ThreadPriority();
        Thread t1 = new Thread(threadPriority);
        Thread t2 = new Thread(threadPriority);
        Thread t3 = new Thread(threadPriority);
        Thread t4 = new Thread(threadPriority);
        System.out.println(Thread.currentThread().getName() + " --> " + Thread.currentThread().getPriority());
        
        t1.start();
        // 先设置优先级再启动。
        t2.setPriority(1);
        t2.start();
        t3.setPriority(5);
        t3.start();
        t4.setPriority(Thread.MAX_PRIORITY);
        t4.start();
        
//        main --> 5
//        Thread-0 --> 5
//        Thread-3 --> 10
//        Thread-2 --> 5
//        Thread-1 --> 1
    }
}
```



## 线程不安全案例

1. 常用类--线程不安全

   ```java
   ArrayList<String> Alist = new ArrayList<>();
   for (int i = 0; i < 1000; i++) {
       new Thread(()->{
           Alist.add(Thread.currentThread().getName());
       }).start();
   }
   System.out.println(Alist.size());//991
   ```

2. 买票

   ```java
   class BuyTicket implements Runnable {
       private int ticketNum = 10;
   
       private boolean flag = true;//外部停止方式
   
       @Override
       public void run() {
           while (flag) {
               try {
                   buy();
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
           }
   
       }
       private void buy() throws InterruptedException {
           if (ticketNum <= 0) {
               flag = false;
               return;
           } else {
               Thread.sleep(200);
               System.out.println(Thread.currentThread().getName() + " buy " + ticketNum--);
           }
       }
       public static void main(String[] args) {
           BuyTicket buyTicket = new BuyTicket();
   
           Thread t1 = new Thread(buyTicket, "F4N");
           Thread t2 = new Thread(buyTicket, "ZHANG");
           Thread t3 = new Thread(buyTicket, "Lisa");
           t1.start();
           t2.start();
           t3.start();
           Lisa buy 10
   //        ZHANG buy 9
   //        F4N buy 8
   //        ZHANG buy 7
   //        Lisa buy 7
   //        F4N buy 7
   //        Lisa buy 6
   //        ZHANG buy 5
   //        F4N buy 4
   //        Lisa buy 2
   //        ZHANG buy 3
   //        F4N buy 1
       }
   }
   //会出现多人买到同一张票
   ```

3. 银行取款

   

## 线程同步 synchronized

​	`多个线程`访问同一对象(内存)，并且某些线程想要修改这个对象(内存)时，会造成数据不一致的情况，这个时候就需要线程同步。线程同步其实是一种`等待机制`。



锁机制会导致的问题：

- 一个线程持有锁会导致其他所有需要此锁的`线程挂起`。
- 多线程竞争情况下，加锁、释放锁还会导致较多的`上下文切换`和`调度延迟`，引起性能问题。
- 如果一个优先级高的线程等待一个优先级低的线程释放锁，会导致`优先级倒置`，引起性能问题。



代码使用 `synchronized` 关键字可以进行加锁，实现线程同步。

- `synchronized`直接用于方法的定义，默认 `锁this`。形成 `同步方法`。

  ```java
  private synchronized  void method(){}
  ```

- `synchronized` 锁一个对象，形成`同步块`。

  ```java
  ArrayList<String> Alist = new ArrayList<>();
  for (int i = 0; i < 1000; i++) {
      new Thread(()->{
          synchronized (Alist){
              Alist.add(Thread.currentThread().getName());
          }
      }).start();
  }
  Thread.sleep(1000);
  System.out.println(Alist.size());//1000
  ```

  







## 守护线程 daemon

- 线程分为 `用户线程` 和 `守护线程`。
- 虚拟机必须确保用户线程执行完毕。
- `虚拟机不用等待守护线程执行完毕。会在其他线程执行完毕后自动结束。`如： 后台记录操作日志、监控内存、垃圾回收...

常见的用户线程：`main()`

常见的守护线程: `GC()`

```java
// 测试 守护线程
public class ThreadDaemon implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 36; i++) {
            System.out.println(i+" -- Have a good day.");
        }
        System.out.println("goodbye world!");
    }
    public static void main(String[] args) {
        ThreadDaemon threadDaemon = new ThreadDaemon();
        God god = new God();

        Thread GodThread = new Thread(god);
        //if {@code true}, marks this thread as a daemon thread
        GodThread.setDaemon(true);
        GodThread.start();
        Thread threadYou = new Thread(threadDaemon);
        threadYou.start();
    }
}

class God implements Runnable {
    @Override
    public void run() {
        while (true) {
            System.out.println("GOD bless you!");
        }
    }
}

```



# Lambda 表达式

为什么用 Lambda--$\lambda$ 表达式：

- 避免匿名内部类定义过多。
- 让代码看起来更加简洁。
- 去掉了一些没有意义的代码，只留下核心逻辑。



`函数式接口` 是学习Java 8 Lambda 表达式的关键。

`函数式接口`：任何接口，如果只包含了一个唯一的抽象方法，那么它就是一个函数式接口。

```java
public interface Runnable{
    public abstract void run();
}
```

Lambda表达式的格式：

```java
ILike like5 = ()->{
            System.out.println("this is lambda...");
        };
```



例子：

```java
public class Lambda {
    // 3.静态内部类
    static class Like2 implements ILike {
        @Override
        public void lambda() {
            System.out.println("Static inner class....");
        }
    }
    public static void main(String[] args) {
        //接口指向实现类对象
        ILike like = new Like();
        like.lambda();

        ILike like2 = new Like2();
        like2.lambda();

        // 4. 局部内部类
        class Like3 implements ILike{
            @Override
            public void lambda() {
                System.out.println("part Inner class..");
            }
        }
        ILike like3 = new Like3();
        like3.lambda();

        // 5.匿名内部类 没有类名称，需要借助接口或父类实现

        ILike like4 = new ILike() {
            @Override
            public void lambda() {
                System.out.println("anonymous inner class...");
            }
        };
        like4.lambda();

        // 6.lambda简化,必须借助函数式接口

        ILike like5 = ()->{
            System.out.println("this is lambda...");
        };
        like5.lambda();
        //简化，一行代码才可以简化成这样
        // 传入参数可以省略数据类型
        like5 = ()->System.out.println("fuc");
        like5.lambda();

    }
}

// 1.定义一个函数式接口
interface ILike {
    void lambda();
}

// 2.实现类
class Like implements ILike {
    @Override
    public void lambda() {
        System.out.println("outer class...");
    }
}
```



























