# Spring 事务传播行为



事务的传播（嵌套）是为了组合丰富复杂的业务

| 事务传播行为类型         | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| PROPAGATION_REQUIRED     | 如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。这是最常见的选择。 |
| PROPAGATION_SUPPORTS     | 支持当前事务，如果当前没有事务，就以非事务方式执行           |
| PROPAGATION_MANDATORY    | 使用当前的事务，如果当前没有事务，就抛出异常                 |
| PROPAGATION_REQUIRES_NEW | 新建事务，如果当前存在事务，就把当前事务挂起                 |
| PROPAGATION_NOT_SUPPORT  | 以非事务方式执行操作，如果大年存在事务，就把当前事务挂起     |
| PROPAGATION_NEVER        | 以非事务方式执行，如果当前存在事务，则抛出异常               |
| PROPAGATION_NESTED       | 如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与 PROPAGATION_REQUIRED 类似的操作 |

[

Spring 中七种事务传播行为](https://mp.weixin.qq.com/s/IglQITCkmx7Lpz60QOW7HA) 