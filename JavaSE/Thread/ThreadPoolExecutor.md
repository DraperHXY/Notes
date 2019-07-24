

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






