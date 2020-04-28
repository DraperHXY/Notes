# Guarded Suspension 模式

[Guarded Suspension 模式](https://time.geekbang.org/column/article/94097) 这个是极客时间上的课程，如果没看过这个系列的话登录也能免费送四篇。

这个我也是刚学，听起来挺唬人，所以捉摸不透的一步一步搞明白。当A线程发出异步请求后，悬挂 A 线程。在 B 线程满足一定的条件后，控制 A 线程的悬挂结束，使 A 继续运行。

这里的悬挂指的就是 suspension，至于中间的控制逻辑，就是 Guarded，可能是我们的业务，或者是一个条件。

## 一、核心代码

```java
public class GuardedObject<T> {

    T obj;
    final Lock lock = new ReentrantLock();
    final Condition done = lock.newCondition();
    final int timeout = 1;

    T get(Predicate<T> p) {
        lock.lock();
        try {
            while (!p.test(obj)) {
                done.await(timeout, TimeUnit.SECONDS);
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
        return obj;
    }

    void onChanged(T obj) {
        lock.lock();
        try {
            this.obj = obj;
            done.signalAll();
        } finally {
            lock.unlock();
        }
    }

}
```

如上面所说，**A 线程调用 ``get`` 方法阻塞线程，B 线程在满足一定条件(a)的情况下，调用 ``onChanged`` 的方法，然后符合再次满足一个条件(b)，A 线程恢复运行。**

我们可以看到上线的代码解决了什么问题？

1. B 线程异步
2. B 线程向 A 线程进行了通讯

这个就是并发编程的设计模式，在很多的情况下拓展这个代码可以解决很多问题而来的。



## 二、拓展

```java
public class ExtendGuardedObject<T> {

    T obj;
    final Lock lock = new ReentrantLock();
    final Condition done = lock.newCondition();
    final int timeout = 2;

    final static Map<Object, ExtendGuardedObject> gos = new ConcurrentHashMap<>();

    static <K> ExtendGuardedObject create(K key) {
        ExtendGuardedObject go = new ExtendGuardedObject();
        gos.put(key, go);
        return go;
    }

    static <K, T> void fireEvent(K key, T obj) {
        ExtendGuardedObject go = gos.remove(key);
        if (go != null) {
            go.onChanged(obj);
        }
    }

    T get(Predicate<T> p) {
        lock.lock();
        try {
            while (!p.test(obj)) {
                done.await(timeout, TimeUnit.SECONDS);
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
        return obj;
    }

    void onChanged(T obj) {
        lock.lock();
        try {
            this.obj = obj;
            done.signalAll();
        } finally {
            lock.unlock();
        }
    }

}
```

