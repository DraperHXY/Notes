# Redis集群原理-主从同步和从从同步基础

谈到 Redis 集群，就离不开 CAP 理论，CAP 理论就相当于物理里面的牛顿定律

在分布式环境下，集群亦可，整个系统有三个属性

* Consistent 一致性
* Avilability 可用性
* Partitation 分区性

**CAP 理论简单一句话就是三个属性里面不可兼得，只能取两种**

P 是必须的，在 CA 中 Redis 的选择是 A，对于一致性只能满足最终一致性



## 一、

从从同步是 Redis 后面版本新加的功能，以减轻主节点的同步负担。为后面描述的方便，统一理解成主从同步



同步分为两种



### 1. 增量同步

Redis 同步的是指令流，主节点会将那些对自己的状态产生修改性影响的指令记录在本地的内存 buffer 中，然后异步将 buffer 中的指令同步到从节点，从节点一遍执行同步的指令流来达到和主节点一致的状态。

因为内存 buffer 是有限的，所以主节点不能将所有的指令记录在 buffer 中。Redis 的复制内存 buffer 是一个定长的环形数组，如果数组内容满了，就会葱油开始覆盖前面的内容。

如果网络状态不好，从节点短时间无法连接主节点，从节点将无法同步主节点的数据，Redis 中有可能后续的指令已经被覆盖了，所以需要更复杂的同步机制——快照同步



### 2. 快照同步

快照同步是一个非常消耗资源的操作，需要在主节点上进行一次 bgsave，将当前内存的数据全部快照到磁盘文件中，然后再将快照文件的内容传送到从节点。从节点将快照文件接收完毕后，立即执行一次全量加载，加载之前要先将当前内存的数据清空，加载完毕后通知主节点继续进行增量同步。

在整个快照同步进行的过程中，主节点的复制 buffer 还在不停的向前移动，如果快照同步的时间过长或者 buffer 太小，都会导致同步期间的增量指令在复制 buffer 中被覆盖，这样就会导致快照同步完成后无法进行增量复制，然后会再次发起快照同步，如此机油可能hi小茹快照同步的死循环

当集群刚刚增加从节点时，必须先进行一次快照同步，同步完成后再进行增量同步。

