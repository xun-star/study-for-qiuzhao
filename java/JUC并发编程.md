# JUC概述

JUC就是java.util.concurrent工具包的简称。这是一个处理线程的工具包。

**进程**：是计算机中的程序关于某数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，是操作系统结构的基础；简而言之：进程就是系统中正在运行的一个应用程序，程序一旦运行就是进程

**线程**：是操作系统进行运算调度的最小单位；简而言之：系统分配处理器时间资源的基本单位。

**协程**：可以在一个线程内部创建多个协程，这些协程之间可以共享同一个线程的资源，协程是在一个线程内部运行的，不需要操作系统的介入，可以在用户空间内实现协作式多任务处理，因此，协程的创建和销毁开销很小，可以高效的利用资源。(java19之后才支持虚拟线程，也就是协程)

进程之间不共线全局变量，线程之间共享全局变量

```java
//线程的状态

新建状态(new)->创建线程对象->线程没有运行start方法

就绪状态(Runnable)->start方法->状态的线程有可能正在执⾏，也有可能没有正在执⾏，等待被分配CPU资源

阻塞状态(blocked)->无法获得锁对象->

等待状态(waiting)->wait方法->	

计时状态(timed_waiting)->sleep方法->

结束状态(terminated)->全部代码运行完毕->run方法执行完毕或者捕获到了异常，终止了run方法
```

```bash
//wait与sleep的区别

sleep是Thread的静态方法，wait是object的方法，任何对象实例都能调用。

sleep不会释放锁，他也不会占用锁。wait会释放锁，但调用它的前提是当下线程占有锁(代码要在sychronized中)

他们都可以被interrupted方法中断；
```

**并发**：底层程序交替执行，给人的感觉是同时发生

**并行**：同一时刻，程序可以分配到多个处理器，实现多任务并行执行。底层程序是同时执行。

# java线程的实现

```java
public void start() {
        synchronized (this) {
            // zero status corresponds to state "NEW".
            if (holder.threadStatus != 0)
                throw new IllegalThreadStateException();
            start0();//这里启动了一个新的线程
        }
    }
//start0的声明如下：
private native void start0();
```

也就是创建新线程启动的时候调用的是` native start0()`方法，而这些`native`方法的注册是在`Thread`对象初始化的时候完成的。

```java
public class Thread implements Runnable {
    /* Make sure registerNatives is the first thing <clinit> does. */
    private static native void registerNatives();
    static {
        registerNatives();
    }
    ...
}
```

Thread类有一个` registerNatives()`方法，该方法的主要作用就是注册一些本地方法供Thread类使用，如`start0(),stop0()`等

` registerNatives()`方法被放在静态代码块当中，所以在该类被加载的时候，方法就会执行。

# 线程常用的方法



| 方法                                        | 功能                                                         | 说明                                                         |
| ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| public void start()                         | 启动⼀个新线程；Java虚拟机调⽤此线程的run⽅法                | start ⽅法只是让线程进⼊就绪，里面的代码不一定立刻运行(时间片还没有分配给它)。每个线程的start方法只能调用一次。 |
| public void run()                           | 线程启动后调⽤该⽅法                                         | 如果在构造 Thread 对象时传递了Runnable参数，则线程启动后会调用Runnable中的run方法，否则默认不执行任何操作，但可以创建子对象来覆盖该方法的默认行为 |
| public void setName(String name）           | 给当前线程设置名字                                           |                                                              |
| public void getName()                       | 获取当前线程的名字，线程存在默认名字：Thread-索引，主线程就是main |                                                              |
| public static Thread currentThread()        | 获取当前线程对象                                             |                                                              |
| public static void sleep(long time)         | 让当前线程休眠多少毫秒再继续执行                             |                                                              |
| public static native void yield()           | 提示线程调度器尽⼒让出当前线程对CPU的使用                    |                                                              |
| public final int getPriority()              | 获取当前线程的优先级                                         |                                                              |
| public final void setPriority(int priority) | 更改此线程的优先级,默认是5                                   | java中规定线程优先级是1~10的整数，较大的优先级能提高该线程被CPU调度的机率 |
| public void interrupt()                     | 中断这个线程                                                 | 仅仅是设置线程的中断状态为true，不会停⽌线程                 |
| public static boolean interrupted()         | 判断当前线程是否被打断，清除打断标记                         | 返回当前线程的中断状态，通过检查中断标志位，判断当前线程是否被中断 |
| public boolean isInterrupted()              | 判断当前线程是否被打断，不清楚打断标记                       | 2、将当前线程的中断状态设为false                             |
| public state getState()                     | 获取线程状态                                                 | Java 中线程状态是⽤ 6 个enum表示,分别是NEW,RUNNABLE,BLOCKED,WAITING,TIME_WAITING,TERMINATED |
| public long getId()                         | 获取线程长整型的id                                           | id唯一                                                       |
| public final void setDaemon(boolean on)     | 将此线程设置为守护线程                                       |                                                              |
| public final native boolean isAlive()       | 判断线程是否存活                                             |                                                              |
| public final void join()                    | 等待这个线程结束                                             | join() ⽅法的作⽤是调⽤线程等待该线程完成后，才能继续往下运⾏ |

## start与run

run方法是同步方法，而start方法是异步方法

run方法的作用是存放任务代码，而start方法是启动线程

执行run方法不会产生新的线程，而start方法会产生新的线程

run⽅法可以被执⾏⽆数次，⽽star⽅法只能被执⾏⼀次，原因就在于线程不能被重复启动。

## 线程的让步

`Thread.yield() `方法的作用是：暂停当前正在执⾏的线程对象（及放弃当前拥有的cup资源），并执⾏其他线程

* sleep与yield的区别：

  sleep是是当前线程进入停滞状态，执行sleep方法，指定时间内线程不会被执行。sleep ⽅法使当前运⾏中的线程睡眼⼀段时间，进⼊不可运⾏状态，这段时间的⻓短是由程序设定的

  yield是使当前线程回到可执行状态，该线程可能会被马上执行。yield ⽅法使当前线程让出 CPU 占有权，但让出的时间是不可设定的

# 线程状态间的转换

* Blocked 进⼊ Runnable

  想要从 Blocked 状态进⼊ Runnable 状态，我们上⾯说过必须要线程获得 monitor 锁，但是如果想进⼊其他状态那么就相对⽐较特殊，因为它是没有超时机制的，也就是不会主动进⼊

* Waiting 进⼊ Runnable
  1. 只有当执⾏了 LockSupport.unpark()，或者 join 的线程运⾏结束，或者被中断时才可以进⼊Runnable 状态
  2. 如果通过其他线程调⽤ notify() 或 notifyAll()来唤醒它，则它会直接进⼊ Blocked 状态，这⾥⼤家可能会有疑问，不是应该直接进⼊ Runnable 吗？这⾥需要注意⼀点 ，因为唤醒 Waiting 线程的线程如果调⽤ notify() 或 notifyAll()，要求必须⾸先持有该 monitor 锁，这也就是我们说的 wait()、notify 必须在 synchronized 代码块中
  3. 所以处于 Waiting 状态的线程被唤醒时拿不到该锁，就会进⼊ Blocked 状态，直到执⾏了notify()/notifyAll() 的唤醒它的线程执⾏完毕并释放 monitor 锁，才可能轮到它去抢夺这把锁，如果它能抢到，就会从 Blocked 状态回到 Runnable 状态
* Timed Waiting 进⼊ Runnable
  1. 同样在 Timed Waiting 中执⾏ notify() 和 notifyAll() 也是⼀样的道理，它们会先进⼊ Blocked 状态，然后抢夺锁成功后，再回到 Runnable 状态
  2. 但是对于 Timed Waiting ⽽⾔，它存在超时机制，也就是说如果超时时间到了那么就会系统⾃动直接拿到锁，或者当 join 的线程执⾏结束/调⽤了LockSupport.unpark()/被中断等情况都会直接进⼊Runnable 状态，⽽不会经历 Blocked 状态

总结：

1. 线程的状态是按照箭头⽅向来⾛的，⽐如线程从 New 状态是不可以直接进⼊ Blocked 状态的，它需要先经历 Runnable 状态。
2. 线程⽣命周期不可逆：⼀旦进⼊ Runnable 状态就不能回到 New 状态；⼀旦被终⽌就不可能再有任何状态的变化
3. 所以⼀个线程只能有⼀次 New 和 Terminated 状态，只有处于中间状态才可以相互转换。也就是这两个状态不会参与相互转化

# 线程池

线程池就是一个可以容纳多个线程的对象，其中的线程可以反复利用，省去了频繁创建线程对象的操作，从而过多的消耗资源

## 线程池的优势

1. 降低资源消耗。通过重复利用已经创建好的线程，来降低线程的创建和销毁的开销
2. 提高响应速度。当任务到达时，可以直接使用线程，而不需要去创建
3. 提高线程的可管理性。使用线程池，可以进行统一的分配，调优和监控

# 并发容器

## BlockingQueue

在所有的并发容器中，BlockingQueue是最为常见的一种，它是一个带阻塞功能的队列，当入队列时，若队列已满，则阻塞调用者；当出队列时，若队列已空，则阻塞调用者

![image-20240126195730416](D:\study-for-qiuzhao\java\assets\image-20240126195730416.png)

* ArrayBlockingQueue（核心是一把锁，两个条件）

  它是一个用数组实现的环形队列，在构造函数中，要求传入数组的容量

  ```java
  public class ArrayBlockingQueue<E> extends AbstractQueue<E>
          implements BlockingQueue<E>, java.io.Serializable {
      /** The queued items */
      @SuppressWarnings("serial") // Conditionally serializable
      final Object[] items;//数组
      
          /** items index for next take, poll, peek or remove */
      int takeIndex;//队列的头索引
      
          /** items index for next put, offer, or add */
      int putIndex;//队列的尾索引
      
          /** Number of elements in the queue */
      int count;//数组大小
      
          /** Main lock guarding all access */
      final ReentrantLock lock;//锁
      
          /** Condition for waiting takes */
      @SuppressWarnings("serial")  // Classes implementing Condition may be serializable.
      private final Condition notEmpty;
  
      /** Condition for waiting puts */
      @SuppressWarnings("serial")  // Classes implementing Condition may be serializable.
      private final Condition notFull;
  
      /**
       * Shared state for currently active iterators, or null if there
       * are known not to be any.  Allows queue operations to update
       * iterator state.
       */
      transient Itrs itrs;
      
      //构造函数
      public ArrayBlockingQueue(int capacity) {
          this(capacity, false);
      }
  
      /**
       * Creates an {@code ArrayBlockingQueue} with the given (fixed)
       * capacity and the specified access policy.
       * 
       * @param capacity the capacity of this queue
       * @param fair if {@code true} then queue accesses for threads blocked
       *        on insertion or removal, are processed in FIFO order;
       *        if {@code false} the access order is unspecified.
       * @throws IllegalArgumentException if {@code capacity < 1}
       */
      public ArrayBlockingQueue(int capacity, boolean fair) {
          if (capacity <= 0)
              throw new IllegalArgumentException();
          this.items = new Object[capacity];
          lock = new ReentrantLock(fair);
          notEmpty = lock.newCondition();
          notFull =  lock.newCondition();
      }
      
      //put函数
      public void put(E e) throws InterruptedException {
          Objects.requireNonNull(e);
          final ReentrantLock lock = this.lock;
          lock.lockInterruptibly();//可中断的lock
          try {
              while (count == items.length)
                  notFull.await();//若队列满了，则阻塞
              enqueue(e);
          } finally {
              lock.unlock();
          }
      }
      //take函数
      public E take() throws InterruptedException {
          final ReentrantLock lock = this.lock;
          lock.lockInterruptibly();
          try {
              while (count == 0)
                  notEmpty.await();//若队列为空，则阻塞
              return dequeue();
          } finally {
              lock.unlock();
          }
      }
  }
  ```

* LinkedBlockingQueue

  是一种基于单向链表的阻塞队列，因为队头和队尾是两个指针分开操作的，所以用了两个条件+两把锁，同时有一个Atomicleteger的原子变量记录count数

  ```java
  public class LinkedBlockingQueue<E> extends AbstractQueue<E>
          implements BlockingQueue<E>, java.io.Serializable {
      
      /** The capacity bound, or Integer.MAX_VALUE if none */
      private final int capacity;//容量
  
      /** Current number of elements */
      private final AtomicInteger count = new AtomicInteger();
  
      /**
       * Head of linked list.
       * Invariant: head.item == null
       */
      transient Node<E> head;
  
      /**
       * Tail of linked list.
       * Invariant: last.next == null
       */
      private transient Node<E> last;
  
      /** Lock held by take, poll, etc */
      private final ReentrantLock takeLock = new ReentrantLock();
  
      /** Wait queue for waiting takes */
      @SuppressWarnings("serial") // Classes implementing Condition may be serializable.
      private final Condition notEmpty = takeLock.newCondition();
  
      /** Lock held by put, offer, etc */
      private final ReentrantLock putLock = new ReentrantLock();
  
      /** Wait queue for waiting puts */
      @SuppressWarnings("serial") // Classes implementing Condition may be serializable.
      private final Condition notFull = putLock.newCondition();
  
      
  }
  
  ```

  