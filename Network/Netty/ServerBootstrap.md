# ServerBootstrap

## 一、group

类的构建没有什么可看的，主要看 group

**当双参数的时候，分别设置父级的 group 和当前自己的 group**

**当单参数的时候，父级和自己共用一个 group**



### 1. 单线程模型

Group 在实例化的时候，线程数为 1，在 group 参数中只需要填入一个线程组，大体就是下面几行代码

```java
EventLoopGroup group = new NioEventLoopGroup(1);
ServerBootstrap sb = new ServerBootstrap();
sb.group(group);
```

### 2. 多线程模型

```java
EventLoopGroup boss = new NioEventLoopGroup(1);
EventLoopGroup worker = new NioEventLoopGroup();
ServerBootstrap bootstrap = new ServerBootstrap();
bootstrap.group(boss, worker);
```

### 3. 主从线程模型

```java
EventLoopGroup boss = new NioEventLoopGroup();
EventLoopGroup worker = new NioEventLoopGroup();
ServerBootstrap bootstrap = new ServerBootstrap();
bootstrap.group(boss, worker);
```



## 二、 channel

```java
bootstrap.channel(NioServerSocketChannel.class)
```

内部就是用一个 ReflectiveChannelFactory 来创建实例

不仅仅是 ``NioServerSocketChannel``，还有其他多种实例可以选择，例如 ``EpollServerSocketChannel``



Option

涉及到 TCP/UDP 编程的参数



## 四、childHandler

选择我们要进行处理数据的主类

