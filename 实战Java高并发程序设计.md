

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

### 一、同步控制

在上面的章节中学习了synchronize关键字作为线程同步控制的方式，接下来要学习重入锁

#### 1.1 重入锁概念

**重入锁**:支持一个线程多次获得锁，何时获得锁以及何时释放锁都为显示操作，相比synchronize关键字更加灵活，并且支持线程重复获得锁

#### 1.2 实战重入锁

##### 1.2.1 创建重入锁

重入锁需要**显示**的获得锁以及释放锁，并且重入锁**支持线程多次获得锁**，切记，线程获得过几次锁，就必须释放几次锁

```java
package com.thread.java.util.concurrent.locks;

/**
 * @Auther: loren
 * @Date: 2019/3/26 20:52
 * @Description: 重入锁
 */
public class ReentrantLock implements Runnable {

    private java.util.concurrent.locks.ReentrantLock reentrantLock = new java.util.concurrent.locks.ReentrantLock();

    private static int i = 0;

    @Override
    public void run() {
        for (int j = 0; j < 90000; j++) {
            reentrantLock.lock();
            try {
                i ++;
            } finally {
                reentrantLock.unlock();
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        ReentrantLock reentrantLock = new ReentrantLock();
        Thread t1 = new Thread(reentrantLock);
        Thread t2 = new Thread(reentrantLock);

        t1.start();
        t1.join();

        t2.start();
        t2.join();

        System.err.println("i " + i);
    }
}
```

##### 1.2.2 中断响应

重入锁支持中断响应操作，意味着当一个线程在等待锁的过程中可以根据需求取消对锁的请求

```java
package com.thread.java.util.concurrent.locks;

import java.util.concurrent.locks.ReentrantLock;

/**
 * @Auther: loren
 * @Date: 2019/3/27 20:27
 * @Description: 重入锁 响应中断
 */
public class Interruptibly implements Runnable {

    private static ReentrantLock lock1 = new ReentrantLock();

    private static ReentrantLock lock2 = new ReentrantLock();

    private int lock;

    public Interruptibly(int lock) {
        this.lock = lock;
    }

    @Override
    public void run() {
        try {
            if (lock == 1) {
                lock1.lockInterruptibly();
                Thread.sleep(500);
                lock2.lockInterruptibly();
            } else {
                lock2.lockInterruptibly();
                Thread.sleep(500);
                lock1.lockInterruptibly();
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            if (lock1.isHeldByCurrentThread()) {
                lock1.unlock();
            }
            if (lock2.isHeldByCurrentThread()) {
                lock2.unlock();
            }
            System.err.println("Thread id " + Thread.currentThread().getId() + " exit");
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Interruptibly l1 = new Interruptibly(1);
        Thread t1 = new Thread(l1);
        Thread t2 = new Thread(l1);

        t1.start();
        t2.start();

        Thread.sleep(500);
        t2.interrupt();
    }
}
```

##### 1.2.3 锁申请等待限时

重入锁**支持锁申请等待限时**，通过tryLock()方法，当超过设定的时间，线程依然没有获得锁，则返回false，反之放回true

```java
package com.thread.java.util.concurrent.locks;

import java.util.concurrent.locks.ReentrantLock;

/**
 * @Auther: loren
 * @Date: 2019/3/29 17:06
 * @Description:
 */
public class TryLock2 implements Runnable {

    private static ReentrantLock lock1 = new ReentrantLock();
    private static ReentrantLock lock2 = new ReentrantLock();

    private int lock;

    public TryLock2(int lock) {
        this.lock = lock;
    }

    @Override
    public void run() {
        if (lock == 1) {
            while (true) {
                if (lock1.tryLock()) {
                    try {
                        Thread.sleep(100);

                        if (lock2.tryLock()) {
                            System.err.println("lock2 Thread " +
                                    Thread.currentThread().getId() +
                                    " my job done");
                        }
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        if (lock1.isHeldByCurrentThread()) {
                            lock1.unlock();
                        }
                        if (lock2.isHeldByCurrentThread()) {
                            lock2.unlock();
                        }
                    }
                }
            }
        } else {
            while (true) {
                if (lock2.tryLock()) {
                    try {
                        Thread.sleep(100);

                        if (lock1.tryLock()) {
                            System.err.println("lock1 Thread " +
                                    Thread.currentThread().getId() +
                                    " my job done");
                        }
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        if (lock1.isHeldByCurrentThread()) {
                            lock1.unlock();
                        }
                        if (lock2.isHeldByCurrentThread()) {
                            lock2.unlock();
                        }
                    }
                }
            }
        }
    }

    public static void main(String[] args) {
        TryLock2 lock1 = new TryLock2(1);
        TryLock2 lock2 = new TryLock2(2);

        Thread t1 = new Thread(lock1);
        Thread t2 = new Thread(lock2);

        t1.start();
        t2.start();
    }
}
```

##### 1.2.4 公平锁

公平锁 多个线程进行排队依次获得锁并执行
公平锁需要维护一个有序队列，因此公平锁实现成本很高，性能相对也比较低下

```java
package com.thread.java.util.concurrent.locks;

import java.util.concurrent.locks.ReentrantLock;

/**
 * @Auther: loren
 * @Date: 2019/4/1 10:40
 * @Description: 实验公平锁
 */
public class FairLock implements Runnable {

    /**
     * 公平锁 多个线程进行排队依次获得锁并执行
     * 公平锁需要维护一个有序队列，因此公平锁实现成本很高，性能相对也比较低下
     */
    private static ReentrantLock fairLock = new ReentrantLock(true);

    @Override
    public void run() {
        while (true) {
            try {
                fairLock.lock();
                System.err.println(Thread.currentThread().getName() + "get lock ...");
            } finally {
                fairLock.unlock();
            }
        }
    }

    public static void main(String[] args) {
        FairLock fairLock = new FairLock();

        Thread t1 = new Thread(fairLock, "thread-0");
        Thread t2 = new Thread(fairLock, "thread-1");

        t1.start();
        t2.start();
    }
}
```

#### 1.3 Condition

**Condition**与重入锁(ReentrantLock)搭配使用，Condition提供了await()、awaitUnInterruptibly() 、singal()、singalAll()等方法

```java
package com.thread.java.util.concurrent.condition;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @Auther: loren
 * @Date: 2019/4/1 20:13
 * @Description: 实战 重入锁搭档 {@link Condition}
 * 常用方法：
 *     {@link Condition#await()} 使线程等待并释放锁
 *     {@link Condition#signal()} 唤醒一个在该对象上等待的线程 调用该方法之前需要获得锁
 *     {@link Condition#signalAll()} 唤醒全部在该对象上等待的线程 调用该方法之前需要获得锁
 *
 */
public class ReentrantLockCondition implements Runnable {

    /**
     * 创建重入锁
     */
    private static ReentrantLock reentrantLock = new ReentrantLock();

    /**
     * 将 reentrantLock 与 condition 绑定
     */
    private static Condition condition = reentrantLock.newCondition();

    @Override
    public void run() {
        reentrantLock.lock();
        try {
            // 使得当前线程在 condition对象上等待 并释放锁
            condition.await();
            System.err.println(Thread.currentThread().getName() + " exit... ");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            reentrantLock.unlock();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        ReentrantLockCondition reentrantLockCondition = new ReentrantLockCondition();
        Thread re1 = new Thread(reentrantLockCondition, "re1");
        re1.start();
        Thread.sleep(2000);
        // 通知re1继续执行
        reentrantLock.lock();
        condition.signal();
        reentrantLock.unlock();
    }
}
```



#### 1.4 信号量(Semaphore)

**信号量**可以指定多个线程同时访问某一资源，与之相对应的，无论是内部锁(synchronized)还是重入锁**(ReentrantLock)**一次都只允许一个线程访问一个资源

```java
package com.thread.java.util.concurrent.condition;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Semaphore;

/**
 * @Auther: loren
 * @Date: 2019/4/4 15:23
 * @Description:实战信号量
 */
public class SemaphoreDemo implements Runnable {

    /**
     * 意味着颁发了五个许可
     */
    public static Semaphore semp = new Semaphore(5);

    @Override
    public void run() {
        try {
            // 从讯号量中询问许可 当前线程是否可以执行
            semp.acquire();
            Thread.sleep(2000);
            // 每隔2秒输出五次
            System.err.println(Thread.currentThread().getId() + " done! ...");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            // 释放许可
            semp.release();
        }
    }

    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(20);
        for (int i = 0; i < 20; i++) {
            SemaphoreDemo demo = new SemaphoreDemo();
            executorService.submit(demo);
        }
    }
}

```



#### 1.5 读写锁

读写锁允许多个线程同时读，但是写写操作和读写操作的线程依然堵塞的

读写锁约束情况如下表：

|      | 读     | 写   |
| ---- | ------ | ---- |
| 读   | 非阻塞 | 阻塞 |
| 写   | 阻塞   | 阻塞 |

```java
package com.thread.java.util.concurrent.condition;

import java.util.Random;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

/**
 * @Auther: loren
 * @Date: 2019/4/4 15:40
 * @Description: 实践读写锁
 * 在读写锁中，读读之间是可以同时访问临界资源的
 */
public class ReentrantReadWriteLockDemo {

    /**
     * 普通的重入锁
     */
    private static ReentrantLock reentrantLock = new ReentrantLock();

    /**
     * 被操作的值
     */
    private int value;

    /**
     * 读写锁
     */
    private static ReentrantReadWriteLock reentrantReadWriteLock = new ReentrantReadWriteLock();

    /**
     * 模拟读
     * @param lock
     * @return
     * @throws InterruptedException
     */
    public int handlerRead(Lock lock) throws InterruptedException {
        try {
            lock.lock();
            Thread.sleep(1000);
            System.err.println("readThread value = " + value);
            return value;
        } finally {
            lock.unlock();
        }
    }

    /**
     * 模拟写
     * @param lock
     * @param index
     * @throws InterruptedException
     */
    public void handlerWrite(Lock lock, int index) throws InterruptedException {
        try {
            lock.lock();
            Thread.sleep(1000);
            value = index;
            System.err.println("writerThread value = " + value);
        } finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) {
        ReentrantReadWriteLockDemo demo = new ReentrantReadWriteLockDemo();
        //读锁
        ReentrantReadWriteLock.ReadLock readLock = reentrantReadWriteLock.readLock();
        //写锁
        ReentrantReadWriteLock.WriteLock writeLock = reentrantReadWriteLock.writeLock();

        Runnable readThread = new Runnable() {
            @Override
            public void run() {
                try {
//                    demo.handlerRead(reentrantLock);
                    demo.handlerRead(readLock);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };

        Runnable writerThread = new Runnable() {
            @Override
            public void run() {
                try {
//                    demo.handlerWrite(reentrantLock, new Random().nextInt());
                    demo.handlerWrite(writeLock, new Random().nextInt());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };

        for (int i = 0; i < 18; i++) {
            new Thread(readThread).start();
        }

        for (int i = 0; i < 2; i++) {
            new Thread(writerThread).start();
        }
    }
}

```



#### 1.6 倒计数器CountDownLatch

通常用来控制线程等待，可以**让某个线程等待直倒计数结束，再开始执行**

可以用在使点火线程**等待所有检查线程全部完工***后再执行场景中

```java
package com.thread.java.util.concurrent.condition;

import java.util.Random;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * @Auther: loren
 * @Date: 2019/4/4 16:22
 * @Description: 实战倒计数器
 * 常用方法：
 * {@link CountDownLatch#await()} 该方法会使当前线程等待 直到计数器为0
 * {@link CountDownLatch#countDown()} 该方法会使计数器减1 当计数器为0时，会唤醒所有在等待中的线程
 */
public class CountDownLatchDemo implements Runnable {

    /**
     * 初始化倒计数器
     */
    public static CountDownLatch countDownLatch = new CountDownLatch(10);

    @Override
    public void run() {
        try {
            // 模拟检查任务
            Thread.sleep(new Random().nextInt(10) * 1000);
            System.err.println(Thread.currentThread().getId()  + " check over! ... ");
            countDownLatch.countDown();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        CountDownLatchDemo countDownLatchDemo = new CountDownLatchDemo();
        ExecutorService executorService = Executors.newFixedThreadPool(10);
        for (int i = 0; i < 10; i++) {
            executorService.submit(new Thread(countDownLatchDemo));
        }
        // 等待倒计数完毕
        countDownLatch.await();
        System.err.println("Fire !!!");
        executorService.shutdown();
    }
}
```

#### 1.7 RateLimiter

采用**令牌桶算法**，该算法在**每个单位时间产生一定量的令牌**存入桶中，拥有令牌的线程才可执行

```java
package com.thread.java.util.concurrent.condition;

import com.revinate.guava.util.concurrent.RateLimiter;

/**
 * @Auther: loren
 * @Date: 2019/4/4 19:05
 * @Description: 实战限流器
 * 常用方法：
 * {@link RateLimiter#create(double)} 指定每秒能执行几个线程 使用令牌桶算法
 * {@link RateLimiter#acquire()} 获取一个许可，当前线程能否执行
 * 该类来自于 google guava 工具包
 */
public class RateLimiterDemo {

    /**
     * 初始化限流器 指定每秒最多有两个线程执行
     */
    public static RateLimiter limiter = RateLimiter.create(2);

    public static class Task implements Runnable {

        @Override
        public void run() {
            System.err.println(System.currentTimeMillis());
        }
    }

    public static void main(String[] args) {
        for (int i = 0; i < 50; i++) {
            // 拥有令牌才可执行
            limiter.acquire();
            new Thread(new Task()).start();
        }
    }
}
```

