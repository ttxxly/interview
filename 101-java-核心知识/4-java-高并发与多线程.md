### 这里介绍java并发与多线程的知识

#### 并发编程三要素？

```text
（1）原子性
原子性指的是一个或者多个操作，要么全部执行并且在执行的过程中不被其他操作打断，要么就全部都不执行。
（2）可见性
可见性指多个线程操作一个共享变量时，其中一个线程对变量进行修改后，其他线程可以立即看到修改的结果。
（3）有序性
有序性，即程序的执行顺序按照代码的先后顺序来执行。
```
#### 实现可见性的方法有哪些？

```text
synchronized 或者 Lock：
  保证同一个时刻只有一个线程获取锁执行代码，锁释放之前把最 新的值刷新到主内存，实现可见性。
```

#### 在 java 中守护线程和本地线程区别？

```text
java 中的线程分为两种：守护线程（Daemon）和用户线程（User）。

任何线程都可以设置为守护线程和用户线程，通过方法 
  Thread.setDaemon(boolon);
  true 则把该线程设置为守护线程，反之则为用户线程。
注意：Thread.setDaemon()必须在 Thread.start()之前调用，否则运行时会抛出异常。

两者的区别：
唯一的区别是判断虚拟机(JVM)何时离开，Daemon 是为其他线程提供服务，如果
全部的 User Thread 已经撤离，Daemon 没有可服务的线程，JVM 撤离。
```
#### Java 中用到的线程调度算法是什么？

```text
采用时间片轮转的方式。
可以设置线程的优先级，会映射到下层的系统上面的优先级上，如非特别需要，尽量不要用，防止线程饥饿。
```
#### 创建线程有几种不同的方式？

```text
1. **继承 Thread 类创建线程类**
（1）定义Thread类的子类，并重写该类的run方法，该run方法的方法体就代表了线程要完成的任务。
  因此把run()方法称为执行体。
（2）创建Thread子类的实例，即创建了线程对象。
（3）调用线程对象的start()方法来启动该线程。

package com.thread;
public class FirstThreadTest extends Thread{
    int i = 0;
    //重写run方法，run方法的方法体就是现场执行体
    public void run()
    {
        for(;i<100;i++){
        System.out.println(getName()+"  "+i);

        }
    }
    public static void main(String[] args)
    {
        for(int i = 0;i< 100;i++)
        {
            System.out.println(Thread.currentThread().getName()+"  : "+i);
            if(i==20)
            {
                new FirstThreadTest().start();
                new FirstThreadTest().start();
            }
        }
    }
}

2. **通过Runnable接口创建线程类**
（1）定义runnable接口的实现类，并重写该接口的run()方法，该run()方法的方法体
  同样是该线程的线程执行体。
（2）创建 Runnable实现类的实例，并依此实例作为Thread的target来创建Thread对象，
  该Thread对象才是真正的线程对象。
（3）调用线程对象的start()方法来启动该线程。

package com.thread;
public class RunnableThreadTest implements Runnable
{
    private int i;
    public void run()
    {
        for(i = 0;i <100;i++)
        {
            System.out.println(Thread.currentThread().getName()+" "+i);
        }
    }
    public static void main(String[] args)
    {
        for(int i = 0;i < 100;i++)
        {
            System.out.println(Thread.currentThread().getName()+" "+i);
            if(i==20)
            {
                RunnableThreadTest rtt = new RunnableThreadTest();
                new Thread(rtt,"新线程1").start();
                new Thread(rtt,"新线程2").start();
            }
        }
    }
}

3. **实现Callable接口**
4. **使用Executor框架创建线程池**。Executor框架是juc里提供的线程池的实现。

一般情况下，常见的是第二种。
* Runnable接口有如下好处：
*①避免单继承的局限，一个类可以实现多个接口。
*②适合于资源的共享
```
#### 线程状态流转图？

```text
（1）新建状态（New）：
当线程对象对创建后，即进入了新建状态，如：Thread t= new MyThread()；

（2）就绪状态（Runnable）：
  当调用线程对象的 start()方法（t.start();），线程即进入就绪状态。
  处于就绪状态的线程，只是说明此线程已经做好了准备，随时等待 CPU 调度执行，
  并不是说执行了t.start()此线程立即就会执行；

（3）运行状态（Running）：
  当 CPU 开始调度处于就绪状态的线程时，此时线程才得以真正执行，即进入到运行状态。
  注：就 绪状态是进入到运行状态的唯一入口，也就是说，线程要想进入运行状态执行，
  首先必须处于就绪状态中；

（4）阻塞状态（Blocked）：
  处于运行状态中的线程由于某种原因，暂时放弃对 CPU的使用权，停止执行，此时进入阻塞状态，
    直到其进入到就绪状态，才 有机会再次被 CPU 调用以进入到运行状态。
  根据阻塞产生的原因不同，阻塞状态又可以分为三种：
    1）等待阻塞：运行状态中的线程执行 wait()方法，使本线程进入到等待阻塞状态；
    2）同步阻塞：线程在获取 synchronized 同步锁失败(因为锁被其它线程所占用)，
      它会进入同步阻塞状态；
    3）其他阻塞：通过调用线程的 sleep()或 join()或发出了 I/O 请求时，
      线程会进入到阻塞状态。当 sleep()状态超时、join()等待线程终止或者超时、
      或者 I/O 处理完毕时，线程重新转入就绪状态。

（5）死亡状态（Dead）：
  线程执行完了或者因异常退出了 run()方法，该线程结束生命周期。
```

### 具体方法、接口以及类

#### 现在有 T1、T2、T3 三个线程，你怎样保证 T2 在 T1 执行完后执行，T3 在 T2 执行完后执行？

```Java
考察是否熟悉 join 方法。
public class JoinTest2 {
 
    // 1.现在有T1、T2、T3三个线程，你怎样保证T2在T1执行完后执行，T3在T2执行完后执行
    public static void main(String[] args) {
        final Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("t1");
            }
        });
        final Thread t2 = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    //引用t1线程，等待t1线程执行完
                    t1.join();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("t2");
            }
        });
        Thread t3 = new Thread(new Runnable() {
 
            @Override
            public void run() {
                try {
                    //引用t2线程，等待t2线程执行完
                    t2.join();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("t3");
            }
        });
        t3.start();
        t2.start();
        t1.start();
    }
}

```

#### 在 java中lock接口比synchronized 块的优势是什么？

```Java
lock 接口在多线程和并发编程中最大的优势是它们为读和写分别提供了锁，
它能满足你写像ConcurrentHashMap 这样的高性能数据结构和有条件的阻塞。
```

#### 你需要实现一个高效的缓存，它允许多个用户读，但只允许一个用户写，以此来保持它的完整性，你会怎样去实现它？

```Java
考察 ReentrantReadWriteLock 的使用。

import java.util.Date;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

/**
 * 你需要实现一个高效的缓存，它允许多个用户读，但只允许一个用户写，以此来保持它的完整性，你会怎样去实现它？
 * @author user
 *
 */
public class Test2 {

  public static void main(String[] args) {
    for (int i = 0; i < 3; i++) {
      new Thread(new Runnable() {
        
        @Override
        public void run() {
          MyData.read();
        }
      }).start();
    }
    for (int i = 0; i < 3; i++) {
      new Thread(new Runnable() {
        
        @Override
        public void run() {
          MyData.write("a");
        }
      }).start();
    }
  }
}

class MyData{
  //数据
  private static String data = "0";
  //读写锁
  private static ReadWriteLock rw = new ReentrantReadWriteLock();
  //读数据
  public static void read(){
    rw.readLock().lock();
    System.out.println(Thread.currentThread()+"读取一次数据："+data+"时间："+new Date());
    try {
      Thread.sleep(1000);
    } catch (InterruptedException e) {
      e.printStackTrace();
    } finally {
      rw.readLock().unlock();
    }
  }
  //写数据
  public static void write(String data){
    rw.writeLock().lock();
    System.out.println(Thread.currentThread()+"对数据进行修改一次："+data+"时间："+new Date());
    try {
      Thread.sleep(1000);
    } catch (InterruptedException e) {
      e.printStackTrace();
    } finally {
      rw.writeLock().unlock();
    }
  }
}

```

#### sleep( ) 和 wait( n)、wait( ) 的区别？

```text
sleep 方法： 是 Thread 类的静态方法，当前线程将睡眠 n 毫秒，线程进入阻塞状态。
  当睡眠时间到了，会解除阻塞，进行可运行状态，等待 CPU 的到来。睡眠不释放锁（如果有的话）；

wait 方法： 是 Object 的方法，必须与 synchronized 关键字一起使用，线程进入阻塞状态，
  当 notify 或者 notifyall 被调用后，会解除阻塞。但是，只有重新占用互斥锁之后才会进入可运行状态。
  睡眠时，释放互斥锁。
```

#### synchronized 关键字？

```text
底层实现：
进入时，执行 monitorenter，将计数器 +1，释放锁 monitorexit 时，计数器-1；
当一个线程判断到计数器为 0 时，则当前锁空闲，可以占用；反之，当前线程进入等待状态。
含义：（monitor 机制）
Synchronized 是在加锁，加对象锁。
对象锁是一种重量锁（monitor），synchronized 的锁机制会根据线程竞争情况在运行时会有偏向锁（单一线程）
、轻量锁（多个线程访问 synchronized 区域）、对象锁（重量锁，多个线程存在竞争的情况）、自旋锁等。

该关键字是一个几种锁的封装。
```

#### volatile 关键字？能使得一个非原子操作变成原子操作吗？

```text
该关键字可以提供顺序和可见性保证。
功能：
* 主内存和工作内存，直接与主内存产生交互，进行读写操作，保证可见性；
* 禁止 JVM 进行的指令重排序。

解析：关于指令重排序的问题，可以查阅 DCL 双检锁失效相关资料。
```

#### java中能创建 Volatile 数组吗？

```Java
能。
java中可以创建 Volatile 类型数组，不过只是一个指向数组的引用。

```
#### Volatile 类型变量提供什么保证？

```Java
volatile 变量提供顺序和可见性保证。
  JVM 或者JIT 为了获得更好的性能会对语句进行重排序，但是 volatile 类型变量即使在没有同步块的
    情况下赋值也不会与其他语句重排序。
  volatile 提供 happens-before 的保证，确保一个线程的修改能对其他线程是可见的。
  某些情况下，volatile 还能提供原子性，如读 64位数据类型
    long 和 double 都不是原子的，但是 volatile 类型的 long 和 double就是原子的。

```

可参考资料：happens-before 原则

#### ThreadLocal（线程局部变量）关键字？

```text
使用 ThreadLocal 维护变量时，其为每个使用该变量的线程提供独立的变量副本，
所以每一个线程都可以独立的改变自己的副本，而不会影响其他线程对应的副本。

ThreadLocal 内部实现机制：
  每个线程内部都会维护一个类似 HashMap 的对象，称为 ThreadLocalMap，里边会包含若干
   Entry（K-V 键值对），相应的线程被称为这些 Entry 的属主线程；
  Entry 的 Key 是一个 ThreadLocal 实例，Value 是一个线程特有对象。
  Entry 的作用即是：为其属主线程建立起一个 ThreadLocal 实例与一个线程特有对象之间的对应关系；
  Entry 对 Key 的引用是弱引用；Entry 对 Value 的引用是强引用。
```

#### 为什么我们调用 start()方法时会执行 run()方法，为什么我们不能直接调用 run()方法？

```Java
当你调用 start()方法时你将创建新的线程，并且执行在 run()方法里的代码。
但是如果你直接调用 run()方法，它不会创建新的线程也不会执行调用线程的代码
```

#### AbstractQueuedSynchronizer 介绍？

```Java
AbstractQueuedSynchronizer 提供了一个队列，大多数开发者可能从来不会直接 用到 AQS，
  AQS 有个变量用来存放状态信息 state,可以通过 protected 的 getState,
  setState,compareAndSetState 函数进行调用。
  对于 ReentrantLock 来说， state 可以用来表示该线程获可重入锁的次数
  对于semaphore 来说 state 用来表示当 前可用信号的个数，
  FutuerTask 用来表示任务状态（例如还没开始，运行，完成， 取消）。
```

### JDK5 并发包

#### 在 Java 中 CycliBarriar 和 CountdownLatch 有什么区别？

```Java
CountDownLatch 的计数器只能使用一次。
CyclicBarrier 的计数器可以使用 reset() 方法重置。

所以 CyclicBarrier 能处理更为复杂的业务场景，比如如果计算发生错误，可以 重置计数器，并让线程们重新执行一次。

```

为什么我们调用 start()方法时会执行 run()方法，为什么我们不能直接调用 run()方法？

#### CountDownLatch 原理？

### 线程池

#### 什么是线程池？有哪几种创建方式？

```text
线程池就是提前创建若干个线程，如果有任务需要处理，线程池里的线程就会处理任务，
处理完之后线程并不会被销毁，而是等待下一个任务。由于创建和销毁线程都是消耗系统资源的，
所以当你想要频繁的创建和销毁线程的时候就可以考虑使用线程池来提升系统的性能。
java 提供了一个 java.util.concurrent.Executor 接口的实现用于创建线程池。

创建方式：
（1）newCachedThreadPool 创建一个可缓存线程池
（2）newFixedThreadPool 创建一个定长线程池，可控制线程最大并发数。
（3）newScheduledThreadPool 创建一个定长线程池，支持定时及周期性任务执行。
（4）newSingleThreadExecutor 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务。
```