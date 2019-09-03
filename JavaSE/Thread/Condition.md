Condition

主要内容：

* 单个 Condition 实现等待通知
* 多个 Condition 实现等待通知(释放指定线程的锁)
* 多个 Condition 实现生产者消费者
* Condition 常用 方法



## 一、单个 Condition 实现等待通知

### 1. 介绍

使用 ``synchronized`` 、``wait`` 和 ``notify`` ``notifyAll`` 可以实现等待/通知模型

使用 ``ReetrantLock`` 也可以实现相同的功能，但是要结合 ``Condition`` 来使用



### 2. 使用

```java
public class MyService {

    private Lock lock = new ReentrantLock();

    private Condition condition = lock.newCondition();

    public void await() {
        try {
            lock.lock();
            System.out.println("A");
            condition.await();
            System.out.println("B");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
            System.out.println("锁释放了");
        }
    }

    public void signal() {
        try {
            lock.lock();
            System.out.println("signal 时间为 " + System.currentTimeMillis());
            condition.signal();
        } finally {
            lock.unlock();
        }
    }

}
```

正常线程

```java
public class ThreadA extends Thread {

    private MyService service;

    public ThreadA(MyService service) {
        this.service = service;
    }

    @Override
    public void run() {
        service.await();
    }

}
```

运行

```java
public class Run {

    public static void main(String[] args) throws InterruptedException {
        MyService service = new MyService();
        ThreadA a = new ThreadA(service);
        a.start();
        Thread.sleep(3000);
        service.signal();
    }

}
```

运行结果

> A
> signal 时间为 1555495863241
> B
> 锁释放了



### 3. 总结

* Object 类中的 wait() 方法相当于 Condition 类中的 await() 方法。

* Object 类中的 wait(long timeout) 方法相当于 Condition 类中的 await(long time, TimeUnit unit) 方法

* Object 类中的 notify() 方法相当于 Condition 类中的 signal() 方法

* Object 类中的 notifyAll() 方法相当于 Condition 类中的 signalAll() 方法



## 二、多个 Condition 实现等待通知(释放指定线程的锁)

### 1. 介绍

使用 condition 的好处就在于可以精确的唤醒指定的线程，所以在多线程中，也要通过多个 condition 来实现

### 2. 实现

Service 注意这里需要两个 condition 以区分不同的线程

```java
public class MyService {

    private Lock lock = new ReentrantLock();
    private Condition conditionA = lock.newCondition();
    private Condition conditionB = lock.newCondition();

    public void awaitA() {
        try {
            lock.lock();
            System.out.println("A 加锁");
            conditionA.await();
            System.out.println("A 释放锁");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void awaitB() {
        try {
            lock.lock();
            System.out.println("B 加锁");
            conditionB.await();
            System.out.println("B 释放锁");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void signalA() {
        lock.lock();
        System.out.println("A signal");
        conditionA.signal();
        System.out.println("A signal after");
        lock.unlock();
    }

    public void signalB() {
        lock.lock();
        System.out.println("B signal");
        conditionB.signal();
        System.out.println("B signal after");
        lock.unlock();
    }

}
```

ThreadA

```java
public class ThreadA extends Thread {

    private MyService service;

    public ThreadA(MyService service) {
        this.service = service;
    }

    @Override
    public void run() {
        service.awaitA();
    }

}
```

ThreadB

```java
public class ThreadB extends Thread {

    private MyService service;

    public ThreadB(MyService service) {
        this.service = service;
    }

    @Override
    public void run() {
        service.awaitB();
    }

}
```

Run

```java
public class Run {

    public static void main(String[] args) throws InterruptedException {
        MyService myService = new MyService();
        ThreadA threadA = new ThreadA(myService);
        ThreadB threadB = new ThreadB(myService);

        threadA.start();
        threadB.start();
        Thread.sleep(2000);

        myService.signalA();
        Thread.sleep(2000);
        myService.signalB();
    }

}
```



### 3. 总结

* 既然要释放指定线程的锁，那么这个 lock 也是特殊的(condition)



## 三、多个 Condition 实现生产者消费者

在 [github](https://github.com/DraperHXY/ThreadExample/tree/master/ConditionABC/src/main/java/com/draper) ab 包下，就是一个生产者消费者



## 四、Condition 常用 方法

在 wait-notify 实现生产者消费者的时候，notifyAll 是唤醒所有线程，然后再去争抢锁。仔细想一想是没有必要的，能不能唤醒指定线程。而 Condition 就可以实现这种效果。

```java
getHoldCount() //用来查询当前线程保持锁定的个数，也就是调用 lock 的个数
getQueueLength() //返回正等待获取此锁定的线程估计数
getWaitQueueLength(Condition condition) //返回与此锁定条件 condition 的线程估计数
hasQueuedThread(Thread thread) //查询指定的线程是否正在等待获取此锁定
hasQueuedThreads() //是否有线程真呢过在等待获取此锁定
hasWasWaiters(Condition condition) //是否有线程正在等待与此锁定有关的 condition 条件
isFair() //判断是不是公平锁
isHeldByCurrentThread(); // 查询当前线程是否保持此锁定
isLocked(); // 查询此锁定是否由任意线程保持
lockInterruptibly(); //如果当前线程未被中断，则获取此锁定，如果被中断，则抛出异常
tryLock(); // 仅在调用时锁定未被另一个线程保持的情况下才获取此锁定
tryLock(long timeout, TimeUnit unit); // 如果锁定在给定等待时间内没有被另一个线程保持，且当前线程未被中断，则获取该锁定。
awaitUninitrruptibly(); 
awaitUntilTest();
```



