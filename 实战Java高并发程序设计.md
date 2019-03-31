

## 第一章 走入并行世界

###  一、基础概念

#### 1.1同步(Synchronous)与异步(Asynchronous)
**同步** ：方法调用一旦开始，调用者必须等到方法调用返回后才能继续后续行为
**异步**：方法调用类似消息传递，一旦开始，方法调用就会立即返回，调用者就可以继续后续的操作
![](C:\Users\loren\Pictures\Saved Pictures\2019-03-22_105726.jpg)


#### 1.2 并发(Concurrenry)与并行(Parallelism)
都是值多个任务同时在CPU中执行(**除非有多个CPU或多核CPU支持才是真正的并行**)
但是并发偏重于多个任务交替执行(**在CPU中来回切换**)，而多个任务之间有肯能还是串行的，**非真正意义上的同时执行**

![](C:\Users\loren\Pictures\Saved Pictures\2019-03-22_110611.jpg)

#### 1.3临界区
临界区表示一种公共资源或者共享数据，可以被多个线程使用
但是**每一次只能有一个线程使用它**，一旦临界区资源被占用，其他线程必须等待

#### 1.5死锁(Deadlock)、饥饿(Starvation)和活锁
**死锁**：指多个线程相互抢占 临界区资源，导致都无法执行
**饥饿**：值某个或多个线程因为种种原因无法获得所需的资源，导致一直无法执行，如线程优先级过低
**活锁**：线程主动释放资源给其他线程使用，资源不断在两个线程中跳动，而没有一个线程可以同时拿到所有资源而正常执行

###  二、并行级别

#### 2.1阻塞(Blocking)
一个线程无法得到临界区的资源，就会进入阻塞状态被挂起，直到得到临界区资源的锁

#### 2.2无饥饿(Starvation-Free)
如果线程之间的优先级相差很大，那么优先级高的线程总是会被先执行，导致优先级低的线程一直无法执行就会产生线程饥饿
但如果**锁是公平的，满足先来后到**,那么饥饿就不会产生

#### 2.3无障碍
允许多个线程同时进入临界区

#### 2.4无锁(Lock-Free)
无锁的并行都是无障碍的，在无锁的情况下，所有的线程都能尝试对临界区进行访问
但无锁的并发**保证必然有一个线程能够在有限步内完成操作**离开临界区

#### 2.5无等待(Wait-Free)
要求所有的线程都必须在有限步内完成，这样就不会引起饥饿问题

***



#### 2.6原子性、可见性、有序性

**原子性**：指操作不可中断，即使在多个线程一起执行的时候，一个操作一旦开始，就不会被其他线程干扰

**可见性**：指一个线程修改了某个共享变量的值，其他线程是否能够立即感知。

**有序性**：~~现在比较模糊~后面再看~



## 第二章 JAVA并发程序基础

### 一、进程与线程

**进程(Process)**：是计算机的程序关于某数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，是操作系统结构的基础

**线程(Thread)**：是轻量级进程，是程序执行的最小单位，进程存在于线程之中

### 二、线程生命周期

![](C:\Users\loren\Pictures\Saved Pictures\18152411-a974ea82ebc04e72bd874c3921f8bfec.jpg)

#### 2.1基本概念

**新建**：线程对象创建后就进入了新建状态。

**就绪**：也称为“可执行状态”，线程对象被创建后，其他线程调用了该对象的start()方法，从而来启动该线程，

处于就绪状态的线程，随时可能被CPU调度执行。

**运行**：线程获取CPU权限进行执行。需要注意的是，线程只能从就绪状态进入运行状态。

**阻塞**：阻塞状态是线程因为某种原因放弃CPU使用权，暂时停止运行，直到线程进入就绪状态，才有机会转到运行状态。阻塞分三种情况：

> 等待阻塞：通过调用对象的 await()方法，让线程等待某工作的完成
>
> 同步阻塞：线程在获取synchronize同步锁失败(因为锁被其他线程占用)，会进入同步阻塞状态
>
> 其他阻塞：通过调用线程的sleep()或join()或发出了I/O请求时，线程会进入到阻塞状态。当sleep()状态超时，join()等待线程终止或者超时、或者I/0处理完毕时，线程重新进入就绪状态。

**死亡**：线程执行完了或者异常退出了run()方法，该线程结束生命周期。



#### 2.2 例子

##### 2.2.1 线程创建

```java
package com.thread.lifecycle;

/**
 * @Auther: loren
 * @Date: 2019/3/23 15:13
 * @Description:
 * 创建线程有两种方式：
 *     ①继承{@link Thread} 类
 *     ②实现 {@link Runnable} 接口
 */
public class ThreadCreate {

    /**
     * 继承{@link Thread} 创建线程
     */
    public static class T1 extends Thread {
        @Override
        public void run() {
            super.run();
            System.out.println("继承{@link Thread} 创建线程...");
        }
    }

    /**
     * 实现 {@link Runnable} 接口 创建线程
     */
    public static class T2 implements Runnable{
        @Override
        public void run() {
            System.out.println("实现 {@link Runnable} 接口 创建线程 ...");
        }
    }

    public static void main(String[] args) {
        T1 t1 = new T1();
        // 实现 Runnable 接口创建线程的线程需要使用 new Thread()构造方法来示例划
        Thread t2 = new Thread(new T2());

        // 错误的调用方式
        //t1.run(); 这是方法调用 而非启动一个线程

        t1.start();
        t2.start();
    }
}

```



##### 2.2.2线程终止

```java
package com.thread.lifecycle;

import com.thread.vo.User;

import java.util.UUID;

/**
 * @Auther: loren
 * @Date: 2019/3/23 15:41
 * @Description: 实验如何安全的让一个线程停止
 * 让一个线程停止，不可以调用{@link Thread#stop()}方法 该方法过于暴力
 * 会直接停止线程 并释放锁 会导致数据不一致问题
 */
public class ThreadStop {

    private static User user = new User();

    /**
     * 改变用户线程
     */
    public static class ChangeUserThread extends Thread {
        volatile boolean stopMe = false;

        public void stopMe() {
            stopMe = true;
        }

        @Override
        public void run() {
            while (true) {
                if (stopMe) {
                    System.err.println("stopMe is true bye bye... ");
                    break;
                }
                synchronized (user) {
                    String id = UUID.randomUUID().toString().substring(0, 5);
                    user.setId(id);
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    user.setName(id);
                }
            }
            Thread.yield();
        }
    }

    /**
     * 读用户线程
     */
    public static class ReadUserThread extends Thread {

        @Override
        public void run() {

            while (true) {
                synchronized (user) {
                    if (!user.getId().equals(user.getName())) {
                        System.err.println(user.toString());
                    }
                }
                Thread.yield();
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        ReadUserThread readUserThread = new ReadUserThread();
        readUserThread.start();

        while (true) {
            ChangeUserThread changeUserThread = new ChangeUserThread();
            changeUserThread.start();
            Thread.sleep(1500);

            //changeUserThread.stop(); 不安全终止线程方式

            //安全的线程终止
            changeUserThread.stopMe();
        }
    }
}
```

##### 2.2.3线程中断

```java
package com.thread.lifecycle;

/**
 * @Auther: loren
 * @Date: 2019/3/23 16:24
 * @Description: 线程中断
 * 解释几个方法：
 *     {@link Thread#isInterrupted()} 判断线程是否被中断
 *     {@link Thread#interrupt()} 中断线程
 *     {@link Thread#interrupted()} 判断线程是否被中断 并清除中断状态
 *
 * 关于线程中断：
 *    当一个线程被标记为中断状态后，该线程并不会立即停止
 *    而是取决于被中断线程的线程中断逻辑
 */
public class ThreadInterrupt {

    public static void main(String[] args) throws InterruptedException {

        /**
         * 匿名内部类实现线程
         */
        Thread threadInterrupt = new Thread() {

            @Override
            public void run() {

                while (true) {
                    if (Thread.currentThread().isInterrupted()) {
                        System.err.println("Me interrupted ... ");
                        break;
                    }
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        // 当一个sleep中的线程被标记为 interrupted 则sleep()方法会抛出 InterruptedException 异常
                        // 并清除当前线程的 interrupted 状态
                        // 所以这里需要重新设置线程的中断状态
                        Thread.currentThread().interrupt();
                        e.printStackTrace();
                    }
                    Thread.yield();
                }
            }
        };
        threadInterrupt.start();
        Thread.sleep(2000);
        //中断线程
        threadInterrupt.interrupt();
    }

}

```

##### 2.2.4线程wait与notify

```java
package com.thread.lifecycle;

/**
 * @Auther: loren
 * @Date: 2019/3/23 14:42
 * @Description: 实验 {@link Object#wait()} 与 {@link Object#notify()} 方法
 *
 * 解释一些方法：
 * {@link Object#notify()} 随机唤醒一个在等待队列中的线程
 * {@link Object#notifyAll()} 唤醒所有在等待队列中的线程
 * {@link Object#wait()} 会使得当前线程在该对象上等待直到被唤醒 并释放锁
 * {@link Thread#sleep(long)} 会使得当前线程等待指定的时间长度 但不会释放资源(锁)
 */
public class ThreadWaitNotify {

    //定义静态对象
    private static Object object = new Object();

    // 线程T1
    public static class T1 extends Thread {

        @Override
        public void run() {
            synchronized (object) {
                // 线程启动提示
                System.err.println(System.currentTimeMillis() + " T1 start..." );
                try {
                    System.err.println(System.currentTimeMillis() + " T1 wait for object...");
                    // object对象调用wait方法 线程进入等待阶段 并释放锁
                    // 需要object对象调用notify方法来唤醒
                    object.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.err.println(System.currentTimeMillis() + " T1 end...");
            }
        }
    }

    // 线程 T2
    public static class T2 extends Thread {

        @Override
        public void run() {
            synchronized (object) {
                // object对象调用notify方法 该方法会随机唤醒一个在此对象上等待的线程
                // 但此时还没有释放锁 所以 t1需要等待2s后才能执行
                System.err.println(System.currentTimeMillis() + " T2 notify one thread... ");
                object.notify();
                System.err.println(System.currentTimeMillis() + " T2 thread end... ");
                try {
                    // 线程睡两秒
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                // 执行完毕之后 才会释放锁
            }
        }
    }

    public static void main(String[] args) {
        T1 t1 = new T1();
        T2 t2 = new T2();

        t1.start();
        t2.start();
    }


}

```

##### 2.2.5join与yeild

有时，一个线程的输入可能非常依赖另外一个或者多个线程的输出，那么依赖线程就要等待被依赖线程执行完毕再继续执行。

```java
package com.thread.lifecycle;

/**
 * @Auther: loren
 * @Date: 2019/3/23 17:09
 * @Description: 实验线程 {@link Thread#join()} {@link Thread#yield()}
 * 解释几个方法：
 * {@link Thread#join()} 一直阻塞当前线程 直到目标线程执行完毕
 * {@link Thread#join(long)} 阻塞当前线程直到指定的时间 不一定会等到目标线程执行完毕
 * {@link Thread#yield()} 让出当前线程CPU资源 但是还会进行CPU资源的争夺
 */
public class ThreadJoinYield {

    public volatile static int i = 0;

    public static class AddThread extends Thread {
        @Override
        public void run() {
            try {
                for (i = 0; i < 5; i++)
                    Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        AddThread addThread = new AddThread();
        // adThread 依赖 main 方法主线程启动
        addThread.start();
        // 调用join方法后，表示主线程需要等待子线程终止后才继续执行
        // 本质上是调用了 wait() 方法 使得主线程等待
        // 子线程执行完毕后会调用notifyAll()方法
        addThread.join();

        //如果主线程不 join 则会输出很小的值
        System.out.println(i);
    }
}

```

##### 2.2.6 suspend与resume

​        如果使用suspend()方法使得线程挂起，此时该线程并不会释放任何锁资源，任何其他线程想要访问被它占用的锁时，都无法继续执行，直到对应的线程调用了resume()方法，被挂起的线程才能继续执行，而万一suspend()方法在resume()方法之前调用，则会导致系统无法运行。

#### 2.3 volatile关键字

1. 保证了不同线程对这个变量进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的
2. 禁止进行指令重排序

#### 2.4 守护线程

#### 2.5 synchronized关键字

**作用**：

1. 指定加锁对象：对给定对象加锁，进入同步代码前，要获得给定对象的锁。
2. 直接作用于示例方法：相当于对当前实例加锁，进入同步代码前要获得当前实例的锁。
3. 直接作用于静态方法：相当于对当前类加锁，进入同步代码前要获得当前类的锁。



## 第三章 JDK并发包

