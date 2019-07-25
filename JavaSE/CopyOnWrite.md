# 为什会有 Copy On Write

COW 在不同的操作系统，或者框架中都会有相应的实现



### 优点

* COW 技术可以减少分配和赋值大量资源带来的瞬时延迟
* COW 可以减少不必要的资源分配。比如 fork 进程时，并不是所有的页面都需要赋值。父进程的代码段和只读数据段都不被允许修改，所以无需复制

### 缺点

* 如果在 fork 之后，父子进程都还需要继续进行写操作，那么会产生大量的分页错误，这样就得不偿失

# 回顾

我们都知道 Vector 是用于替代 ArrayList，Vector 是线程安全的容器。因为它几乎在每个方法声明处都加了 synchronized 关键字来保证容器安全。

如果使用 ``Collections.synchronizedList(new ArrayList())`` 来使 ArrayList 变成线程安全的话，也就是每个方法都加上 synchronized 关键字，只不过不是在方法的声明处，而是在方法的内部



下面有一段代码

```java
public static Object getLast(Vector list){
    int lastIndex = list.size() - 1;
    return list.get(lastIndex);
}

public static void deleteLast(Vector list){
    int lastIndex = list.size() - 1;
    return list.remove(lastIndex);
}
```

在多线程下是否有问题



答案是有的，因为这两个方法并不是原子性的，要保证这个线程安全不能仅仅给方法加锁，还要在遍历前给 vector 加锁。在遍历中，假设对 vector 的结构进行了破坏，例如 clear，则后续的操作可能并没有第一时间可见，继续进行操作，例如 get 读取数据，从而造成程序异常，所以最好的方法是遍历前个给 vector 加锁。



# 什么是 COW

多个调用者同时请求相同的资源，它们会共同获取相应的指针指向相同的资源，知道某个调用者试图修改资源内容时，系统才会真正赋值一个专用副本给调用者，而其他调用者所见到的最初的资源任然保持不变。

在 Java 中 COW 的一个应用就是 ``CopyOnWriteArrayList`` 

* CopyOnWriteArrayList 相对于 ArrayList 线程安全，底层通过复制数组的方式来实现
* 在遍历使用时不会抛出 ConcurrentmodificationException 并且遍历的时候就不用额外加锁
* 元素可以为 null



## Java 中 CopyOnWriteArrayList 的实现

在 CopyOnWriteArrayList 中

```java
private E get(Object[] a, int index) {
    return (E) a[index];
}
```

对于 get 方法，直接返回数据

而对于 add 方法

```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}
```

clear 方法

```java
public void clear() {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        setArray(new Object[0]);
    } finally {
        lock.unlock();
    }
}
```

set 方法

```java
public E set(int index, E element) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        E oldValue = get(elements, index);
        if (oldValue != element) {
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len);
            newElements[index] = element;
            setArray(newElements);
        } else {
            // Not quite a no-op; ensures volatile write semantics
            setArray(elements);
        }
        return oldValue;
    } finally {
        lock.unlock();
    }
}
```

都要进行上锁，add 和 set 方法中还执行了 ArrayCopy 方法，进行拷贝，返回的是一个副本。



## 为什么在遍历时不需要显式加锁

查看源码发现 CopyOnWriteArrayList 实现 Iterator 的具体是 ``COWIterator`` 

```java
@SuppressWarnings("unchecked")
public E next() {
    if (! hasNext())
        throw new NoSuchElementException();
    return (E) snapshot[cursor++];
}
```

在返回数据的时候是 snapshot 这个数组中的数据就是快照(snapshot)吗？

```java
private COWIterator(Object[] elements, int initialCursor) {
    cursor = initialCursor;
    snapshot = elements;
}
```

很明显不是的，构造函数中传入的就是原来的那个数组

[CopyOnWriteArrayList你都不知道，怎么拿offer？](https://zhuanlan.zhihu.com/p/48848893)

> 原文在这里，我还是不太明白

想了想，解释一下

因为对 List 具有结构性改变的操作都是按照 COW 实现的，即都是对数组的副本进行操作。

而我们遍历的时候用的是源本的数组，所以遍历的时候并不会造成影响

这篇文章也解决了我一直在意的一件事，为什么在对表遍历的时候，会抛出异常

其 COWIterator 的内部类实现如下

```java
/**
 * Not supported. Always throws UnsupportedOperationException.
 * @throws UnsupportedOperationException always; {@code remove}
 *         is not supported by this iterator.
 */
public void remove() {
    throw new UnsupportedOperationException();
}
/**
 * Not supported. Always throws UnsupportedOperationException.
 * @throws UnsupportedOperationException always; {@code set}
 *         is not supported by this iterator.
 */
public void set(E e) {
    throw new UnsupportedOperationException();
}
/**
 * Not supported. Always throws UnsupportedOperationException.
 * @throws UnsupportedOperationException always; {@code add}
 *         is not supported by this iterator.
 */
public void add(E e) {
    throw new UnsupportedOperationException();
}
```

全部抛出不支持操作异常，怪不得



## CopyOnWriteArrayList 的缺点

CopyOnWriteArrayList 同样有 COW 的缺点，本文开始就说了，只不过那时在操作系统中



* COW 会造成数据错误，不能实时保证数据一致性，但是可以保证最终一致性，可以保证最终一致性

  > 例如一个线程 get 了一个 value 走了，另外一个进去 remove 了同一个 value,
  >
  > 实时上这个里面没有这个 value，但别的线程继续拿着这个 value 进行处理。

* 因为设计表结构的操作都要 copy，所以会造成内存占用偏高



## CopyOnWriteArraySet

两者原理相同

```java
public CopyOnWriteArraySet() {
    al = new CopyOnWriteArrayList<E>();
}
```



# 最后

推荐这篇文章的原本博主 Java3y，感觉真的很强，看他的文章收获也很高

[Java3y](https://segmentfault.com/u/java_3y)