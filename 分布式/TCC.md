# TCC

try-confirm-cancel

实际上就是把 2PC 的数据库事务放到应用层面来执行，从而导致 TCC 架构对项目具有较强的侵入性

为了保持分布式的数据一致性， TCC 采用的是同步的方式



还有一种是异步方式，使用的是 MQ，在使用 MQ 的时候要保证 MQ100% 的可用性，除非采用其他的降级措施。

[分布式服务接口的幂等性如何设计](https://www.cnblogs.com/mengchunchen/p/10077366.html)
[终于有人把“TCC分布式事务”实现原理讲明白了！](<https://mp.weixin.qq.com/s/9A6ZnpBmAbQYC7kLr1iZCQ>)

