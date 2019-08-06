# 为什么要使用线程池

在并发条件下，一个请求一个线程会有很多的问题。

1. 线程的创建是占用内存空间的，过得的线程有可能导致**内存溢出**
2. 线程处理的速度不一样，有些处理的快，有些处理的慢，处理快的**空闲的线程等待被销毁，非常浪费线程的性能。**
3. 线程的**创建和销毁都要花很多时间**，如果使用线程的时间比原本花费的时间要长，那么很明显是不划算的

所以使用线程池来管理所有的线程是很有必要的。

可以完成以下的几个目标

1. 管理线程的数量
2. 省去线程创建的时间
3. 时刻管理线程，尽量使线程忙碌起来，提高 CPU 的运行效率。

## 构造方法

在使用工厂方法，创建出来的线程池，底层都是 ThreadPoolExecutor，实现方法如下

- newFixedThreadPool
  **固定线程数的线程池**，将会返回一个 corePoolSize 和 maximumPoolSize 相等的线程池
- newCachedThreadPool
  **非常有弹性的线程池**，对于新的任务，此时线程池里没有空闲线程，**线程池会毫不犹豫的创建一条新的线程处理这个任务**
- SingleThreadExecutor
  **使用单个 worker** 线程的 **Executor**



这个类是属于前面所说的线程池的具体实现

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler) {
    if (corePoolSize < 0 ||
        maximumPoolSize <= 0 ||
        maximumPoolSize < corePoolSize ||
        keepAliveTime < 0)
        throw new IllegalArgumentException();
    if (workQueue == null || threadFactory == null || handler == null)
        throw new NullPointerException();
    this.acc = System.getSecurityManager() == null ?
            null :
            AccessController.getContext();
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}
```

- corePoolSize 核心线程数量
- maximumPoolSize 最大线程数量
- keepAliveTime 允许线程空闲时间
- unit 时间对象
- workQueue 阻塞队列
- threadFactory 线程工厂
- handler 任务拒绝策略

在这几个参数中，有几个值得注意的地方

### 线程数量

- 如果运行线程的数量少于核心线程数量，则创建新的线程处理请求
- 如果运行线程的数量大于核心线程数量，小于最大线程数量，则当队列满的时候才创建新的线程
- 如果核心线程数量等于最大线程数量，则**创建固定大小**的连接池
- 如果设置了最大线程数量为**无穷**，那么允许线程池适合**任意**的并发数量

### 线程空闲时间

- 当线程数**大于**核心线程数，如果空闲时间已超过了，那么该线程将会**销毁**

### 排队策略

- 同步移交：**不会放到队列中，而是等待线程执行它**。如果当前线程没有执行，很可能会新开一个线程执行。
- 无界限策略：**如果核心线程都在工作，该线程将会放到队列中**。所以线程数不会超过核心线程数。
- 有界限策略：**可以避免资源耗尽**，但是在一定程度上减低了吞吐量

当线程关闭或者线程数量满了和队列饱和了，都有拒绝任务的情况

### 拒绝任务策略：

- 直接抛出异常
- 使用调用者的线程来处理
- 直接丢掉这个任务
- 丢掉最老的任务



### 关闭线程池

- shutdown()：线程池的状态**立刻变为 Shutdown**，而调用 shutdownNow()，线程池状态**立刻变为 STOP**
- shutdown()：**等待任务执行完**才中断线程，而 shutdownNow() **不等任务执行完就中断了线程



线程池就有点儿像一个生产者消费者模式



## 线程池的缺点

1. 死锁。所有的执行的线程都在等待阻塞队列中的结果
2. 线程泄漏。一个线程执行完却没有返回到到线程池中将会导致线程泄漏。例如一个线程抛出异常，但是线程池并没有捕获。然后线程池正常退出，线程池中的数量减一。如果重复多次，线程池最终会空，没有可用线程来执行请求。
3. 资源颠簸。如果一个线程池容量非常大，那么就会浪费很多时间进行上下文的切换。过多的线程比适量的线程更容易导致饥饿问题和资源颠簸。

## 引入的要点

1. 不要将等待其他线程任务的结果的任务排队。换句话说，如果一个线程在等待其他线程的结果，那么就不要放到阻塞队列中，否则可能会导致死锁。
2. 对于长操作小心使用线程。可能会导致其他的线程永远等待，甚至造成资源泄漏。
3. 线程池最终应该被显式关闭，否则程序在线程执行完成后可能永远不会执行 ``shutdown()`` 。
4. 有效的使用线程池，对于类型差异非常大的任务，应该使用不同的线程池来处理不同的类型。(异构任务分别处理)