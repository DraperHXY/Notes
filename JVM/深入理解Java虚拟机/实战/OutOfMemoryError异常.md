# OutOfMemoryError异常

## 一、堆溢出

1. 将 ``-Xms`` 和 ``-Xmx`` 参数设置为一样即可避免堆自动扩展
2. ``-XX:+HeapDumpOnOutOfMemoryError`` 可以将虚拟机出现内存溢出异常时 Dump 当前内存堆转储快照以便分析

> 可能会出现 java.lang.OutOfMemoryError: GC overhead limit exceeded 异常
>
> 这是因为超过 98% 的时间用来 GC ，回收了不到 2% 的内存时的一个警告
>
> 所以要加上参数 ``-XX:-UseGCOverheadLimit`` 但并不能对 GC 产生任何影响，仅仅是警告

3. ``-XX:-UseGCOverheadLimit``

---

## 二、虚拟机栈和本地方法栈溢出

Java 虚拟机栈中，对这个跟区域规定了两种异常情况：StackOverflowError 和 OutOfMemoryError

### 1. StackOverflowError

线程请求栈的深度大于虚拟机所允许的深度

### 2. OutOfMemoryError

虚拟机栈如果可以动态拓展时无法申请到足够的内存，就会抛出 OutOfMemoryError 异常

在 HotSpot 虚拟机中并不区分虚拟机栈和本地方法栈，因此对于 HotSpot 来说 ``-Xoss`` 参数（设置本地方法栈大小）存在，但是是无效的，栈的容量仅有 ``-Xss`` 参数设定。

### 3. 出现 OOM 的解释

首先，在单线程情况下，出现的大部分是 StackOverflowError，无论是减少虚拟机栈的内存 (``-Xoss``) 还是尽可能增加每个栈帧中的局部变量表，都仅会出现 StackOverflowError。但是在多线程情况下，就有可能出现 OOM，这是因为如果操作系统进程减去了设置了 ``-Xss`` 和 MaxPermSize(方法区的最大内存)，程序计数器则是很小的一部分，几乎不耗内存，剩下的则是被本地方法栈和虚拟机栈瓜分。**每个线程分配的栈容量越大，则线程的创建数则越小**，所以容易出现建立线程时把内存耗尽的情况。

### 4. 解决方法

正常来讲，栈内存出现 StackOverflow 时相对容易找出问题出现在哪里，调用次数过多，递归死循环（通常递归在 1000 到 2000 完全没问题）比较容易排查。但是出现 OOM 则要考虑是否线程创建过多导致的内存溢出，在不能减少线程数目或者更换 64 位虚拟机的情况下，就只能通过减少最大堆和减少栈容量来换取更多的线程。

这也是一个通过减少内存的手段来解决内存溢出的问题。

---

## 三、方法区和运行时常量池溢出

### 1. string.intern();

### 2. 字节码增强

通常来讲方法区会存放一些关于类的元数据，如类名、访问修饰符、常来那个吃、字段描述、方法描述等。所以在使用一些字节码增强工具，在运行时生成了大量的动态类，会导致大量内存被占用。这样的应用在实际应用中很常见，比如说 Spring、Hibernate，在对类进行增强时都会使用到 CDLIB 这类字节码技术，增强的类越多，就需要越大的方法区来保证动态类的 Class 可以被加载入内存。另外 JVM 上的动态语言（例如 Groovy 等）通常会持续创建类来实现语言的动态性。

### 3. 解决方法

方法区溢出也是一种常见的内存溢出异常，一个类要被垃圾收集器回收掉，判定的条件是比较苛刻的。在经常动态生成大量 Class 的应用中，就需要特别注意类的回收状况。

---

## 四、 本机直接内存溢出

DirectMemory 容量可以通过 ``-XX:MaxDirectMemorySize`` 指定，如果不指定，则默认与 Java 堆最大值 (``-Xmx`` 指定) 一样。

``DirectByteBuffer`` 分配内存也会出现内存溢出的异常，但是并没有真正向操作系统申请分配内存，而是通过计算得知内存无法分配，于是手动抛出异常，真正申请分配内存的方法是 ``unsafe.allocateMemory()``

由 ``DirectMemory`` 导致内存溢出，一个明显的特征就是 Heap Dump 文件汇总不会有明显的异常，如果发现 OOM 后 Dump 文件很小，而程序中又直接或间接使用了 NIO 那就可以检查以下是不是这方面的原因。

