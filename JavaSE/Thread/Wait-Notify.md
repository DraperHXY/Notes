# Wait-Notify

wait 的作用是在持有锁的前提下释放锁，开始等待被唤醒

```java
synchronized (lock) {
    System.out.println("开始 wait time = " + System.currentTimeMillis());
    lock.wait();
    System.out.println("结束 wait time = " + System.currentTimeMillis());
}
```

当 lock.wait(); 的时候，锁被释放

```java
synchronized (lock) {
    System.out.println("开始 notify time = " + System.currentTimeMillis());
    lock.notify();
    System.out.println("结束 notify time = " + System.currentTimeMillis());
}
```

当调用了 lock.notify() 表示唤醒在该锁 lock 上等待的线程，但是并没有完全释放，**必须得将这个 synchronized 块代码执行完，上面等待的线程才会获取到锁**，这个特性比较有意思，可以实现一些特殊的效果。

```java
public void run() {
    synchronized (lock) {
        try {
            while (true) {
                while (list.size() < size) {
                    lock.wait();
                }
                list.remove(list.size() - 1);
                System.out.println(Thread.currentThread().getName() + "消费至" + list.size());
                Thread.sleep(1000L);
                lock.notifyAll();
                lock.wait();
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

比如说在第11行和第12行可以看到我再 notfy 后又 wait，相当于当前锁彻底选择 wait。有点儿包含的意思是偏向锁我也不使用了，彻底放弃，待会儿和大家重新竞争。

这里有个小 [demo](https://github.com/DraperHXY/ThreadExample/tree/master/LimitWaitNotify/src/main/java/com/draper)，可以试下把 producer 和 consumer notify 后的 wait 注释掉看看有什么不同

**总之就是 wait 释放锁，notify 不释放锁**

* 当 wait 遇见 interrupt

会抛出异常 InterruptedException。



## 什么时候对象会被唤醒

假设多个锁都在 wait，等待被唤醒。使用 notify 仅有一个且是随机的会被唤醒。会出现有的 lock 一直 wait 下去的情况，即使别的线程释放锁也不会参与竞争，所以会用到 notifyAll 方法来唤醒所有在该对象上等待的线程，并进行竞争。

避免锁会永远 wait 下去，也可以使用带参数的 wait(long) 方法，在等待一定时间后，如果没有被唤醒，则会自动被唤醒。

其次要掌握好 notify 的时机，



## 生产者，消费者模型

任务、同步、互斥。通常来讲多线程编程的核心就是这个，我理解的还不是很多。

什么是任务，什么是同步，什么又是互斥。

[学习一下](生产者消费者.md)













