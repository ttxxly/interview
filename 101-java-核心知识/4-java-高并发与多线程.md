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
#### 我们为什么要使用线程池？核心线程池内部实现了解吗？

```text
1. 减少创建和销毁线程的次数，每个工作线程都可以被重复利用，可执行多个任务。
 可以根据系统的承受能力，调整线程池中工作线程的数目，放置因为消耗过多的内存，
 而把服务器累趴下（每个线程大约需要 1 MB 内存，线程开的越多，消耗的内存也就越大，最后死机）

2. 对于核心的几个线程池，无论是 newFixedThreadPool() 方法，
newSingleThreadExecutor() 还是 newCachedThreadPool() 方法，
虽然看起来创建的线程有着完全不同的功能特点，但其实内部实现均使用了 ThreadPoolExecutor 实现，
其实都只是 ThreadPoolExecutor 类的封装。
```

#### 如果你提交任务时，线程池队列已满，这时会发生什么？

```text
（1）如果使用的是无界队列 LinkedBlockingQueue，也就是无界队列的话，
  没关系，继续添加任务到阻塞队列中等待执行，因为 LinkedBlockingQueue 可以
  近乎认为是一个无穷大的队列，可以无限存放任务
（2）如果使用的是有界队列比如 ArrayBlockingQueue，任务首先会被添加到ArrayBlockingQueue 中，
  ArrayBlockingQueue 满了，会根据maximumPoolSize 的值增加线程数量，
  如果增加了线程数量还是处理不过来，ArrayBlockingQueue 继续满，
  那么则会使用拒绝策略RejectedExecutionHandler 处理满了的任务，默认是 AbortPolicy
```

#### ReentrantReadWriteLock 介绍？

```Java
ReentrantReadWriteLock，它 可以实现读写分离，多个线程同时进行读取，但是最多一个写线程存在
```

### 各种锁
#### 什么是乐观锁和悲观锁?

```text
（1）乐观锁：
  对于并发间操作产生的线程安全问题持乐观状态，乐观锁认为竞争不总是会发生，
  因此它不需要持有锁，将比较-替换这两个动作作为一个原子操作尝试去修改内存中的变量，
  如果失败则表示发生冲突，那么就应该有相应的重试逻辑。
（2）悲观锁：
  对于并发间操作产生的线程安全问题持悲观状态，悲观锁认为竞争总是会发生，
  因此每次对某资源进行操作时，都会持有一个独占的锁，就像 synchronized，
  不管三七二十一，直接上了锁就操作资源了。
```

#### 什么是可重入锁？

```Java
当一个线程要获取一个被其他线程占用的锁时候，该线程会被阻塞，
那么当一个线程再次获取它自己已经获取的锁时候是否会被阻塞那?
  如果不需要阻塞那么我 们说该锁是可重入锁，
  也就是说只要该线程获取了该锁，那么可以无限制次数进 入被该锁锁住的代码。

```

#### 独占锁与共享锁？

```Java
根据锁能够被单个线程还是多个线程共同持有，锁又分为独占锁和共享锁。

独占锁保证任何时候都只有一个线程能读写权限，ReentrantLock 就是以独占方式实 现的互斥锁。
共享锁则可以同时有多个读线程，但最多只能有一个写线程，读和 写是互斥的。
  例如 ReadWriteLock 读写锁，它允许一个资源可以被多线程同时进 行读操作，或者被一个线程 写操作，但两者不能同时进行。
  
独占锁是一种悲观锁，每次访问资源都先加上互斥锁，这限制了并发性，
  因为读 操作并不会影响数据一致性，而独占锁只允许同时一个线程读取数据，其他线程 必须等待当前线程释放锁才能进行读取。 
共享锁则是一种乐观锁，它放宽了加锁的条件，允许多个线程同时进行读操作
```
#### 公平锁与非公平锁？

```Java
根据线程获取锁的抢占机制锁可以分为公平锁和非公平锁。
公平锁表示线程获取 锁的顺序是按照线程加锁的时间多少来决定的，也就是最早加锁的线程将最早获取锁，
  也就是先来先得的 FIFO 顺序。
非公平锁则运行闯入，也就是先来不一 定先得
ReentrantLock 提供了公平和非公平锁的实现： 
  * 公平锁 ReentrantLock pairLock = new ReentrantLock(true); 
  * 非公平锁 ReentrantLock pairLock = new ReentrantLock(false); 
  如果构造函数不传递参数，则默认是非公平锁。

```

### 笔试部分

#### 用java 实现阻塞队列

```Java

```

如果他用wait()和 notify()方法来实现阻塞队列，你可以要求他用最新的Java5 中的并发类来再写一次

#### 用 java 解决生产者-消费者问题

```Java

```

更有甚者会问怎么实现哲学家进餐问题

#### 用java写一个线程安全的单例模式（Singleton）？

```Java

```

### 未分类

#### 什么是 Busy spin？我们为什么要使用它？

```Java
Busy spin是一种在不释放 CPU 的基础上等待事件的技术。
它经常用于避免丢失 CPU 缓存中的数据（如果线程先暂停，之后在其他 CPU 上运行就会丢失）。
如果你的工作要求低延迟，并且你的线程目前没有任何顺序，这样你就可以通过
  循环检测队列中的新消息来代替调用 sleep() 或 wait() 方法。
它唯一的好处就是你只需等待很短的时间，如几微秒或几纳秒。

LAMX 分布式框架是一个高性能线程间通达的库，该库有一个 BusySpinWaitStrategy 类就是
  基于这个概念实现的，使用 Busy spin 循环 EventProcessors 等待屏障

```

#### 什么是竞争条件？你怎样发现和解决竞争？

```Java

```

#### Synchronized用过吗 ，其原理是什么 ？

```text
Synchronized是由JVM实现的一种实现互斥同步的一种方式。

从字节码指令来看：
被Synchronized修饰过的程序块，在编译前后被编译器生成了monitorenter和monitorexit两个字节码指令。
这两个指令是什么意思呢？
  在虚拟机执行到monitorenter指令时，首先要尝试获取对象的锁：
    如果这个对象没有锁定，或者当前线程已经拥有了这个对象的锁，把锁的计数器+1；
    如果获取对象失败了，那当前线程就要阻塞等待，直到对象锁被另外一个线程释放为止。
  当执行monitorexit指令时将锁计数器-1；当计数器为0时，锁就被释放了。
Java中Synchronize通过在对象头设置标记，达到了获取锁和释放锁的目的。
```
#### 你刚才提到获取对象的锁，这个“锁”到底是什么？如何确定对象的锁？

```text
“锁”的本质其实是monitorenter和monitorexit字节码指令的一个Reference类型的参数，即要锁定和解锁的对象。

我们知道，使用Synchronized可以修饰不同的对象，因此，对应的对象锁可以这么确定。

如果Synchronized明确指定了锁对象，比如Synchronized（变量名）、Synchronized(this)等，说明加解锁对象为该对象。
如果没有明确指定：
  若Synchronized修饰的方法为非静态方法，表示此方法对应的对象为锁对象；
  若Synchronized修饰的方法为静态方法，则表示此方法对应的类对象为锁对象。
注意，当一个对象被锁住时，对象里面所有用Synchronized修饰的方法都将产生堵塞，
  而对象里非Synchronized修饰的方法可正常被调用，不受锁影响。
```