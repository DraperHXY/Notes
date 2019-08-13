# RocketMQ核心概念

## 一、Producer

向 Broker 发送消息的生产者，这些消息由业务系统应用生成

RocketMQ 提供多种发送的方式：

* 同步
* 异步
* one-way



## 二、Producer Group

生产者组，相同角色的生产者为一组。相同生产者组中的不同 producer 的实例可能与 broker 通信，在事务后，producer 崩溃，broker 可以选择事务的提交或者回滚

**Warning**:考虑到生产者发送消息能力的足够强大，仅允许一个 producer group 有一个实例，避免没有必要的 producer 实例初始化



## 三、Consumer 

consumer 从 broker 拉取信息，并将其送入程序。从用户的视角来看，可以提供两种类型的 consumer

### 1. PullConsumer

PullConsumer 从 broker 主动的拉取信息。用户应用的消费过程可以批量拉取信息



### 2. PushConsumer

PushController 提供一个回调接口，当信息到达时进行回调



### Consumer Group

与前面说的 producer group 相似，消费者将明确的具有相同角色的 consumer 聚合在一起。

对于完成负载均衡以及容错性的目标来说， consumer group 是一个特别好的概念



## 四、Topic

Topic 是一个生产者投递消息，以及消费者拉取信息的类别。Topic 与 producers 和 consumers 有着非常低的耦合。特殊情况下，一个 topic 可以接收 0个，1个或者多个生产者发送的消息。







### 







