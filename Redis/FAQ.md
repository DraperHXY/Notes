# FAQ

## 为什么要用 Redis

因为常见的关系型数据库 MySQL 并不能适应所有的场景，例如秒杀库存的扣减，或者APP首页流量的高峰，很容易把数据库打崩



## Redis 数据结构

String Hash List Set ZSet

除此之外，还有一些高级的数据结构


* ``HyperLogLog`` ：基数统计，访问 IP，页面实时 PV 
* ``Geo``：实现周围的人，摇一摇
* ``Pub/Sub``

还有一些更高级的数据结构

* ``Bloom Filter``
* ``RedisSearch``
* ``Redis-ML``



## Redis 分布式锁

``setnx`` 原子性，setnx = set + expire



## 线上服务 Keys 会有什么问题

Redis 是单线程，由此会造成 Redis 堵塞一段时间，可以使用 ``scan``



## Redis 做异步队列

list 有两个操作 ``rpush`` + ``lpop`` 即可实现异步队列，客户端需要 ``sleep`` 一会儿再重试

若要实现堵塞使用 ``blpop``



## Redis 做队列有什么问题？

在消费者下线的情况下，会导致消息丢失，应该使用更高级的消息队列，例如 RocketMQ



## Redis 如何实现延时队列

使用 ``zset``,拿时间戳作为 ``score``，消息内容作为 key 调用 zadd 来生产消息，消费者用 ``zrangebyscore`` 指令来获取 N 秒之前的数据轮询进行处理



## Redis 如何实现持久化

* RDB 镜像全量持久化
* AOF 增量持久化



## 机器突然掉电怎么办

取决于 AOF 的同步时间，在每一条信息



## Redis 集群

* Sentinel 着眼于高可用，在 master 不可用时，将 slave 提升至 master
* Cluster 着眼于拓展性，当单个 Cluster 不可用时，将 Cluster 分片存储



## Redis 持久化

持久化分为 AOF 和 RDB 两种模式

AOF 优先于 RDB，先加载 AOF，若 AOF 不存在，则加载 RDB。

* AOF:  bin log
* RDB : 全表快照