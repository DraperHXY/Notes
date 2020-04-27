# 竞态条件、条件队列和Lock、Condition 

## 一、竞态条件

当程序的运行的正确结果依赖于程序的执行顺序，线程的交替顺序。换句话说，程序的正确与否依赖是否足够幸运。在这种情况下往往会出现竞态条件，也被称作竞争条件。常常做的操作是 ``检查再运行``，比如下面一段代码。

```java
// 经典的线程不安全的懒加载单例
public class Singleton {
    private static Singleton singleton;
    private Singleton() {}
    public static Singleton getInstance() {
        if (singleton == null) {
            singleton = new Singleton();
        }
        return singleton;
    }
}
```

在上面 ``第6行`` 就对**共享变量**（singleton）进行了**检查**，根据检查的结果来决定是否创建对象，所以这里就出现了竞态条件。是否创建对象依赖于检查的结果，但上面的检查这个操作，在多线程情况下，往往就是掩耳盗铃。

再来一个有点儿难度的，下面的这个有竞态条件么？

```java
public void addIfNotExist(Vector vector, Object o) {
    if (!vector.contains(o)) {
        vector.add(o);
    }
}
```

把代码拆开就像下面的两个步骤。


1. 调用 vector.index （在 vector 中）
2. 获取结果 true 或 false（在 addIfNotExist 中）

线程在访问时，第一步加锁串行化，再第二步都返回 false，两个线程通过检查，同时去添加元素，所以会造成线程不安全的现象，总结一下，**单个操作线程安全，组合起来未必线程安全**

所以遇到这种情况一般的解决方法是扩大被锁资源的范围，在判断之前进行加锁。

---

## 二、条件队列

去吃自助往往有这种情形，当我们排队买票进门的时候，会对前台服务员一个互斥占有。

决定是否去限量菜品排队还是去厕所方便的条件，被称为条件变量

当人多起来了，到处都需要排队，而在条件变量的队列则是条件队列。

所以说在略微复杂的业务下，就需要多个条件变量来控制程序的执行顺序。

---









