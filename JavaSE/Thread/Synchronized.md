# Synchronized

一个重量级锁，在 JDK1.6 在 JVM 进行了许多优化



## 一、给方法加，对象锁

```java
synchronized public void method(){
  
}
```

使其在同一时刻只能被一个线程访问

* 当一个线程 A 先持有对象的锁，另一个线程 B 可以异步调用对象的非 synchronized 方法
* 如果一个线程先持有了对象的锁，另一个线程这时想调用 synchronized 方法则需要等待，即同步



### 脏读

> 不仅在数据库中会有，只要涉及多线程，就有这个概念

当读取实例变量时，这个变量已经被其他线程修改过了，即脏读



当一个线程 A 调用 object 对象加入 synchronized 关键字的方法 X 时，A 线程就获得了 X 方法锁，更准确的说是对象锁，所以其他线程必须等待 A 线程执行完毕才能调用 X 方法，但是其他线程可以随意调用其他的非 synchronized 方法，往往脏读发生在这里

所以，B 线程想要避免脏读，调用任意一个 synchronized 的其他方法，则进入同步，需要等待 A 线程执行完 X 方法。



### 锁重入

当一个线程获取了对象的锁后，再次请求此对象的锁，是可以得到该对象的锁。如果不重入的话，会造成死锁，自己拿着锁跟自己抢锁。

例如在一个 synchronized 方法/块的内部调用本类的其他 synchronized 方法/块时，是永远可以得到锁的。



当存在父子类继承关系时，子类可以通过 可重入锁 调用父类的同步方法。



* 出现异常，锁自动释放
* 不能继承父类的同步方法，必须添加 synchronized 关键字



## 二、使用方法块加锁

使用 synchronized 关键字的方法是有弊端的，当 A 线程调用同步方法执行一个长时间的任务，那么 B 线程需要等待比较长的时间。

```java
public void method(){
  synchronized (this) {
    // ...
  }
}
```

**不在 synchronized 就是异步，在 synchronized 就是同步**，synchronized (this) 是锁定当前对象的。

如果是其他对象的话，例如

```java
public class User {
  private int credit;
 	
  public void add() {
    synchronized (credit) {
      ++ credit;
    }
  } 
}
```

这个的作用就是在给 credit 这个对象加锁，并非是 this 了。

锁非 this 对象具有一定的优点：如果在一个类中有很多个 synchronized 方法，这时虽然能够实现同步，但会受到阻塞，所以影响效率；但是使用代码块锁非 this 对象，则是异步的，不与其他锁 this 方法争抢 this 锁，则可以大大提高运行效率，主要是因为对象在 JVM 中的对象监视器不一样造成的。详情看 synchronized 在 JVM 中的具体实现。



但在 synchronized 混用的情况下程序是异步的，极易出现脏读。



小结：

* 当多个线程同时执行 synchronized(x){} 同步方法时呈现同步效果。

* 当其他线程执行 x 对象中 synchronized 同步方法呈现同步效果。

* 当其他线程执行 x 对象方法里面的 synchronized(this) 代码块时也呈现同步效果。

  > 但是要注意，如果其他线程调用不加 synchronized 的关键字方法，也是异步的。

* 当线程执行 x 对象方法里面的 synchronized(this) 代码块时，其他线程调用非 synchronized 的方法中的 synchronized (其他对象) 可能会导致脏读，

## 静态同步 synchronized 方法与 synchronized(class) 代码块

关键字 synchronized 也可以给 static 静态方法加，这样写会给 *.java 文件对应的 Class 类进行持锁

例如 

```java
synchronized public static void print(){
  System.out.println("This thread = " + Thread.currentThread().getName());
}
```

**对象锁可以给当前对象加锁，而 Class 锁可以给该类的所有对象实例起到作用。**



**数据类型 String 的常量池特性**，导致的后果是一旦以 string 作为对象锁，导致其他线程无法执行(堵塞)

对于内部类和静态内部类，理解好上面加锁的原理，道理是一样的。
只要锁的对象不变，即使对象的属性被改变，运行结果还是同步的。







