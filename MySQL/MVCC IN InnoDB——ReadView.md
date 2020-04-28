# MVCC IN InnoDB —— ReadView



## RR

 在 RR 隔离级别下，每个事务开始时，会将当前系统中的所有的活跃事务拷贝到一个列表生成 ReadView

## RC

在 RC 隔离级别下，每个语句开始时，会将当前系统中的所有活跃事务拷贝到一个列表中生成 ReadView。



二者的区别就在于生成 ReadView 的时间点不同，一个是事务开始一个是语句开始。

[MySQL InnoDB MVCC 机制的原理及实现](https://mp.weixin.qq.com/s/pc275Wwdt932Q982BaBDRA)

