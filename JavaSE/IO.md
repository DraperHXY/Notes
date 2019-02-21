

# IO



Java IO 库需要很多性能的各种组合，如果这些性能都通过继承来实现，那么每一种组合都需要一个类，这样就会造成大量行为重复的类出现。如果采用装饰模式，那么类的数目就会大大减少，性能的重复可以减至最少。因此装饰模式是 Java IO 库的基本模式。装饰模式的引进，造成了灵活性和复杂性的提高。



Inputstream 有七个直接子类

* ByteArrayInputStream
* FileInputStream
* FilterInpuStream
* ObjectInputStream
* PipedInputStream
* SequenceInputStream
* StringBufferInputStream



这些都叫做流处理器



流处理器分为两种

* 原始流处理器
* 链接流处理器



## 原始流处理器

接受不同类型的流源对象(原始流源)，并生成一个 InputStream 流对象。

## 链接流处理器

可以接受一个(同种类)流对象(链接流源) 作为流源，并对功能进行拓展。