# ThreadLocaL



## 应用场景







我觉得文章写得既没别人好，自己的理解笔记也得记录一下，所以在此记录

原文 [(Web)浅谈ThreadLocal](<https://zhuanlan.zhihu.com/p/60375306>)， 写得真的不错



一个 Thread 会有一个私有的 ThreadLocalMap 变量，这个 ThreadLocalMap 中，会存放许多个 ThreadLocal



## 一般通过 ThreadLocal 进行操作的流程

创建一个 ThreadLocal 对象，绑定到线程中，而线程中存在一个 Map（ThreadLocalMap），这个 Map 所有线程共享，但线程的操作权利（get，set），只能由绑定的线程进行操作。



博客中说道，就把 ThreadLocal 看成一个工具类，其中的 ThreadLocalMap 会存放所有线程绑定的实例，我们能进行的操作只有 ThreadLocal，对于其中的 map 无法操作（包访问权限），避免开发人员直接对 ThreadLocalMap 进行操作，破坏线程的隔离性。



## 拓展

在 ThreadLocalMap 中解决 Hash 冲突的方法是线性探测法，效率低，易冲突，所以每个线程尽可能仅保存一个变量。



我们对 ThreadLocal 操作非常有限（只有 get 和 set），没有 threadLocal = null 的这种操作，这意味着实例出来的 ThreadLocal 对象，一直都是强引用，被静态内部类 ThreadLocalMap 绑定，ROOT 不死，根据 GC 的可达性来说，对象一直不会被回收。这个问题其实是被解决的，

前置知识，[Java 中的四种引用](<https://github.com/DraperHXY/Notes/blob/master/JavaSE/Java%20%E5%9B%9B%E7%A7%8D%E5%BC%95%E7%94%A8.md>) 的弱引用

ThreadLocalMap 中还有一个内部静态类

```java
static class Entry extends WeakReference<ThreadLocal<?>> {
    /** The value associated with this ThreadLocal. */
    Object value;
    Entry(ThreadLocal<?> k, Object v) {
        super(k);
        value = v;
    }
}
```

这个是继承自弱引用

当 ThreadLocal 作为 key，没有强引用的时候，则会被垃圾回收。

为避免内存溢出在调用 remove()



