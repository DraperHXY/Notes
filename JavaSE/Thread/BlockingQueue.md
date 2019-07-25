# BlockingQueue



> BlockingQueue 在 Java 1.5 中像 ``ConcurrentHashMap``、``Counting Semaphore``、``CopyOnWriteArrayList`` 等被添加到 Concurrent 工具中。
>
> BlockingQueue 支持流控制。



## 功能

当**向一个满容量的队列**中添加 element，那么该**线程将会挂起**，直到队列腾出新的空间

当**向一个空的队列**中获取 element，那么该**线程将会挂起**，直到队列中添加了新的 element

而这里的挂起，指的是线程的挂起，而线程的挂起被称为线程的阻塞，所以这就是 BlockingQueue 的由来



## 核心 API

``add(E e);``
有空间即添加，没空间抛 IllegalStateException

``offer(E e)``
如果能立即插入 element 返回 true，否则 false

``put(E e)``
有空间插入，**没空间则挂起**

``take()``
**查询并移除**队列中的 element，挂起直到队列中的对象可用

``drainTo(Collection c)``
将里面所有可用 elements 添加到给定的 collection

``drainTo(Collection c, int maxElements)``
尽可能移除给定数量的 element to collection

``remainingCapaciry()``
返回 Queue 容量限制，如果在容量内 put，则会无阻塞

## 实现
### ArrayBlockingQueue
FIFO，固定数量

### DelayQueue
延迟队列

### PriorityBlockingQueue
具有优先级的阻塞队列

### SynchronousQueue
同步队列，这是一个特殊的队列，内部仅能容纳一个元素，当内部有一个元素的时候，再往内部添加元素，则会造成当前线程堵塞，直到另外一个线程拿走那个对象。
所以说把这个称谓**队列**有点夸张，更多的**像是同步点**