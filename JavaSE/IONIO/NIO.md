# NIO

NIO (Non-Blocking IO)

非阻塞 IO 给我们带来了什么

- 事件驱动模型
- 避免多线程
- 单线程处理多任务
- 非阻塞I/O，I/O读写不再阻塞，而是返回0
- 基于block的传输，通常比基于流的传输更高效
- 更高级的IO函数，zero-copy
- IO多路复用大大提高了Java网络应用的可伸缩性和实用性



IO 没有事件驱动，若数据在网卡中则读取，若不在，则阻塞

NIO 包含事件驱动，无论数据是否在网卡中，都安排任务读取网卡中的数据到缓冲区里，然后去进行其他任务。读好了事件回调，没好则一直监听



NIO 采用了 Reactor 模式的工作方式，Selector 是注册各种 IO 事件的地方，当事件发生时，这个对象将会告诉我们所发生的事件

