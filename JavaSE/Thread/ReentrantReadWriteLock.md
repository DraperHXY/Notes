本文内容：

* ReentrantReadWriteLock 简介
* ReentrantReadWriteLock 的使用
  * 读读共享
  * 写写互斥
  * 读写互斥
  * 写读互斥

## 一、ReentrantReadWriteLock 简介

使用 ReentrantLock 具有完全互斥排他的效果，即同一时间只有一个线程在执行 ReentrantLock.lock() 方法后面的任务。这样做虽然保证了实例变量的线程安全性，但效率是十分低下的。

而 ReentrantReadWriteLock 就是一个读写锁，用在提高运行效率，在某些不需要操作实例变量的方法中，完全可以使用读写锁。

读写锁表示有两个锁，一个是读操作相关的锁，也称作共享锁；另一个是写操作相关的锁，也叫排它锁。也就是在多个读锁之间不互斥，读锁与写锁互斥，写锁与写锁互斥。在没有现成 Thread 进行写入操作时，进行读取操作的多个 Thread 都可以获取该锁，而进行写入操作的 Thread 只有在获取写锁后才能进行写入操作。即多个 Thread 都可以同时进行读取操作，但同一时刻只能允许一个 Thread 进行写入操作。



## 二、ReentrantReadWriteLock 的使用

只要出现写操作，就要给资源加锁

```java
@Override
public void read() {
    try {
        try {
            lock.readLock().lock();
            System.out.println("获得读锁\t" + Thread.currentThread().getName() + "\t" + System.currentTimeMillis());
            Thread.sleep(2000);
        } finally {
            lock.readLock().unlock();
        }
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

```java
@Override
public void write() {
    try {
        try {
            lock.writeLock().lock();
            System.out.println("获得写锁\t" + Thread.currentThread().getName() + "\t" + System.currentTimeMillis());
            Thread.sleep(2000);
        } finally {
            lock.readLock().unlock();
        }
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

