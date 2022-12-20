# MyBatis缓存

## 一、一级缓存

1. MyBatis 一级缓存的生命周期与 SqlSession 一致
2. MyBatis 一级缓存内部设计简单，只是一个没有容量限定的 HashMap，在缓存的性能上有所欠缺
3. MyBatis 一级缓存的最大范围是 SqlSession 的内部，如果在分布式或者多个SqlSession 的分布式环境下，建议设定缓存级别为 Statement

## 二、二级缓存

配置
``` xml
<settings>
    <setting name="cacheEnabled" value="true"/>
</settings>
```

如果需要在多个 SqlSession 间共享缓存，那么需要开启二级缓存

查询的顺序是：二级缓存 -> 一级缓存 -> 数据库

总结：

1. MyBatis 的二级缓存相对于一级缓存来说，实现了 ``SqlSession`` 之间缓存数据的共享，同时粒度更加的细，能够到 ``namespace`` 级别，即每个 mapper, 通过 Cache 接口实现类不同的组合，对 Cache 操作空间比较大
2. MyBatis 在多表查询时，极大可能会出现脏数据，有设计上的缺陷，安全使用二级缓存的条件比较苛刻。
3. 在分布式环境下，由于默认的 MyBatis Cache 实现都是基于本地的，分布式环境下必然会出现读取脏数据，需要使用集中式缓存将 MyBatis 的 Cache 接口实现，有一定的开发成本，安全性也更高

参考资料

[聊聊MyBatis缓存机制](https://tech.meituan.com/2018/01/19/mybatis-cache.html)