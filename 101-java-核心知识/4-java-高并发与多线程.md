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