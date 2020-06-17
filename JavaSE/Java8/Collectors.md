# Collectors

``Collector<T, A, R>``

首先来看下这里面的泛型代表的意思

* ``T``: stream 在调用 collect 方法收集前的数据类型
*  ``A``: A 是 T 的累加器，遍历 T 的时候，会把 T 按照一定的方式添加到 A 中，换句话说就是把一些 T 通过一种方式变成 A
* ``R``: R 可以看成是 A 的累加器，是最终的结果，是把 A 汇聚之后的数据类型，换句话说就是把一些 A 通过一种方式变成 R



再看下 Collectors 的实现方法

```java
Supplier<A> supplier()            // 创建并返回一个新的易变的容器
BiConsumer<A, T> accumulator()    // 将一个值变成对象的一部分
BinaryOperator<A> combiner()      // 接收两个结果并合并他们，可能隐藏部分属性直接返回或者返回一个新的对象
Function<A, R> finisher()         // 将 A 转化成一个 R
Set<Characteristics> characteristics()  // 返回一个不可变的集合
```



运行的顺序

``supplier.get()`` -> ``biConsumer.accept()``  -> ``

