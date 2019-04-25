本文内容：

* 线程池的状态
* 线程池的参数





在使用工厂方法，创建出来的线程池，底层都是 ThreadPoolExecutor，实现方法如下

```java
ExecutorService.newFixedThreadPool(int nThreads); // 
ExecutorService.newCachedThread(); // 
ExecutorService.SingleThreadExecutor();
```





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

1. 指定核心线程的数量
2. 指定最大线程的数量
3. 允许线程空闲时间
4. 时间对象
5. 阻塞队列
6. 线程工厂
7. 任务拒绝策略






