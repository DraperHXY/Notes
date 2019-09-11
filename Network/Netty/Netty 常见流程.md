# Netty 常见流程

1. EventLoop 和 EventLoopGroup
2. Bootstrap 启动引导类
3. Channel 生命周期，状态
4. ChannelHandler 和 ChannelPipline



高性能 RPC 框架的三个要素

1. IO 模型
2. 数据协议
3. 线程模型



EventLoop 好比一个线程，一个 EventLoop 可以服务多个 Channel，一个 Channel 只有一个 EventLoop，所以创建 EventLoopGroup 来优化

EventLoopGroup -> EventLoop

EventLoop -> Selector

