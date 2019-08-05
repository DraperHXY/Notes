# Thinking about consistency in distributed systems



至 7月26将近一周时间，看了分布式的相关理论，感觉有点难

算了，仔细思考现在的处境，根本没有环境去学习这个分布式，好吧，那就先这样，我打算看看别的..但是要做个总结，以后忘了分布式相关就血亏了

我的学习大概有以下几个阶段

1. [什么是传统的事务管理](./什么是传统的事务管理.md)
2. [CAP 和 BASE 理论](./CAP和BASE理论.md)
3. [分布式下的一致性问题](./分布式下的一致性问题.md)
4. [2PC 和 3PC](./2PC和3PC.md)
5. [刚性事务和柔性事务]()
6. [TCC](./TCC.md)
7. [Paxos]()



## Theorem

1. 一致性

   > 一致性模型有
   >
   > * 强一致性
   >   * 同步
   >   * Paxos
   >   * Raft (multi-paxos)
   >     * [动画原理演示](<http://thesecretlivesofdata.com/raft/>)
   >     * [复杂场景演示](<https://raft.github.io/>)
   >   * ZAB (multi-paxos)
   >
   > * 弱一致性
   >   * DNS
   >   * Gossip

2. 事务

3. XA

4. CAP 和 BASE

5. 2PC，3PC

6. Paxos

7. 柔性事务

   * 异步确保



强一致性算法

- 主从同步
  - 会导致一个节点宕机，导致整个集群不可用
- 多数派
  - 在并发情况下，无法保证系统正确性





Paxos

- Basic Paxos
  - 活锁
- Multi Paxos
- Fast Paxos

问题：

难实现，效率低(2轮RPC)、活锁



[左耳朵耗子推荐：分布式系统架构经典资料](<https://www.infoq.cn/article/2018/05/distributed-system-architecture>)