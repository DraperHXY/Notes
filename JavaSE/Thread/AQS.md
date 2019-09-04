# AQS

> 前置条件：要有线程基础，学习过 lock，LockSupport，理解 CAS。

## 一、核心概念

**AQS 的实现主要是依赖一个 int 变量来作为同步状态，以及一个 FIFO 的队列构成等待队列。**它的子类必须重写 AQS 的几个 protect 方法来修改同步状态。其他的方法主要是实现排队和阻塞机制。

在修改同步状态的方法主要有三个：``setState``、``getState``、``compareAndSetState``。

在覆写上面所说的子类的时候，推荐使用内部静态类来覆写。**因为这个子类没有实现任何同步接口，而是定义了若干同步状态的获取和释放，以此来方便我们自定义同步组件的使用。**

上面内容不是很多，复杂没懂的东西可以先记录下来。



## 二、从设计模式理解 AQS

**AQS 的设计模式使用的是模板方法设计模式**，简单的说就是子类覆写的方法，会在父类被调用。

比如下面的几部分代码

1.``AbstractQueuedSynchronizer#tryAcquire`` 方法

```java
protected boolean tryAcquire(int arg) {
    throw new UnsupportedOperationException();
}
```

可以看到父类不支持这个操作

2.1``ReentrantLock.NonfairSync#tryAcquire`` 方法。1 中的方法被覆写

```java
protected final boolean tryAcquire(int acquires) {
    return nonfairTryAcquire(acquires);
}
```

2.2``ReentrantLock.FairSync#tryAcquire`` 方法。1 中方法同样也被覆写

```java
protected final boolean tryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {
        if (!hasQueuedPredecessors() &&
            compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    else if (current == getExclusiveOwnerThread()) {
        int nextc = c + acquires;
        if (nextc < 0)
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}
```

3.``AbstractQueuedSynchronizer#acquire`` 父类调用被子类覆写的方法

```java
public final void acquire(int arg) {
    if (!tryAcquire(arg) &&
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}
```

小结

1. 同步组件（就是各种各样的 Lock，CountDownLatch 包括后面的我们自己实现的小李子） 的实现依赖 AQS，使用 AQS 的时候推荐使用静态内部类来继承 AQS
2. AQS 采用模板方法来设计，继承 AQS 的类重写的方法主要是为了**改变同步状态**。重写的方法有 ``getState()``、``setSate()``、``compareAndSetState()``。
3. AQS 的功能：负责同步状态的管理，线程的排队，等待和唤醒这些底层擦七座，而 Lock 等同步最贱主要专注于实现同步的语义。



## 三、官方 Demo

[Github](https://github.com/DraperHXY/ThreadExample/tree/master/OfficialAQS/src/main/java)

仔细看下静态内部类 Sync 中的方法，大部分都是关于锁争抢和释放的同步状态的改变。就是 CAS，而维护等待队列则是在父类中实现。



## 四、AQS 原理

[深入理解AbstractQueuedSynchronizer(AQS)](https://www.jianshu.com/p/cc308d82cc71)



## 五、深入理解

### 1. addWaiter 和 enq 方法中新增一个节点为什么先将新节点的 prev 置为 tail，再尝试 cas，而不是 cas 成功之后来构造节点之间的双向连接？

``AbstractQueuedSynchronizer#addWaiter``

[Java AbstractQueuedSynchronizer源码阅读2-addWaiter()](https://www.jianshu.com/p/c806dd7f60bc)

### 2. 唤醒节点为什么从 tail 往前遍历？

我的理解就是避免漏掉 Node

### 3. unparkSuccessor 有新线程争锁是否有漏洞？

AQS 框架的可靠性还是依赖子类的实现，若是子类不可靠，AQS 也完蛋

### 4. AQS 如何保证队列的活跃

### 5. Propagate 状态的节点存在的意义？


PS:太TM难了



参考资料

[初识Lock与AbstractQueuedSynchronizer(AQS)](https://www.jianshu.com/p/7a65ab32de2a)

[Doug Lea AQS](http://gee.cs.oswego.edu/dl/papers/aqs.pdf)

[AbstractQueuedSynchronizer源码解读](https://www.cnblogs.com/micrari/p/6937995.html)



































