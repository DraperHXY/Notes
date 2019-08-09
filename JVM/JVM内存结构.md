# JVM内存结构



## 1. JVM 内存分为哪几个区域

* 程序计数器(线程私有)
* 本地方法栈(线程私有)
* 虚拟机栈(线程私有)
* 方法区(线程共享)
* 堆(线程共享)

详细请看 [JVM内存区域](JVM内存区域.md)



## 2. 堆中空间的划分

**堆中大体分为新生代，老年代**

![img](../img/堆内存模型2.png)



* 新生代 Young
  * Eden 新生代
  * From Survivor
  * To Survivor
* 老年代 Old



### 3.  堆空间的控制参数

- -Xms设置堆的最小空间大小
- -Xmx设置堆的最大空间大小
- -XX:NewSize设置新生代最小空间大小
- -XX:MaxNewSize设置新生代最大空间大小
- -XX:PermSize设置永久代最小空间大小
- -XX:MaxPermSize设置永久代最大空间大小
- -Xss设置每个线程的堆栈大小



### 4.  认识 OOM 

```
Exception in thread “main”: java.lang.OutOfMemoryError: Java heap space
```

原因：对象不能分配到内存中

```
Exception in thread “main”: java.lang.OutOfMemoryError: PermGen space
```

原因：类或者方法不能被加载到老年代。它可能出现在一个程序加载很多类的时候，比如引用了很多第三发的库

```
Exception in thread “main”: java.lang.OutOfMemoryError: Requested array size exceeds VM limit
```

原因：创建的数组大于对内存的空间

```
Exception in thread “main”: java.lang.OutOfMemoryError: request <size> bytes for <reason>. Out of swap space?
```

原因：分配本地分配失败。JNI、本地库或者 Java 虚拟机都会从本地堆中分配内存空间

```
Exception in thread “main”: java.lang.OutOfMemoryError: <reason> <stack trace>（Native method）
```

原因：同样是本地方法内存分配失败，只不过是 JNI 或者本地方法栈或者 JVM 发现



[jvm系列(二):JVM内存结构](<https://mp.weixin.qq.com/s?__biz=MzI4NDY5Mjc1Mg==&mid=2247483949&idx=1&sn=8b69d833bbc805e63d5b2fa7c73655f5&chksm=ebf6da52dc815344add64af6fb78fee439c8c27b539b3c0e87d8f6861c8422144d516ae0a837&scene=21#wechat_redirect>)
[使用 VisualVM 进行性能分析及调优](<https://www.ibm.com/developerworks/cn/java/j-lo-visualvm/>)























