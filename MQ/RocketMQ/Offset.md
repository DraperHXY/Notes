# Offset

一句话就是用来存储队列中位置信息。

RocketMQ 中，一种类型的消息会放到一个 Topic 里，为了能够并行，一般一个 Topic 会有多个 ``Message Queue`` ，也可以设置成一个，Offset 是指某个 Topic 下的一条消息在某个 Message Queue 里的位置，**通过 Offset 的值可以定位到这条消息，或者指示 Consumer 从这条消息向后继续处理**



注意，在 ``DefaultMQPushConsumer `` 里的 ``BROADCASTING`` 模式下，每个 Consumer 都收到了这个 Topic 的全部消息，**各个 Consumer 间相互没有干扰，RocketMQ 使用 LocalFileOffsetStore 将 Offset 存储到本地**

