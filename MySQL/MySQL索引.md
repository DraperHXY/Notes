# MySQL 索引

## 一、索引的数据结构

常见有两种：``Hash 索引``和 ``B+Tree`` 索引

如果使用的 ``InnoDB引擎``，则默认是 B+树



## 二、B+Tree 相对于 Hash 的优点

Hash 索引的底层是哈希表，哈希表是一种 Key-value 存储的数据结构，所以在多个数据存在的情况下，数据的存储关系是没有任何顺序关系的，所以**对于区间类型的 Hash 索引，是没有办法进行直接查询的，这就需要全表检索**。

而 **B+Tree 是一种多路平衡查询树，所以节点天然是有序的**，左节点小于父节点，父节点小于右子节点，对于范围查询来说，不需要进行全表扫描。

**Hash 索引没有办法利用索引完成排序，不支持多列联合索引的最左匹配规则**，如果有大量重复键值的情况下，Hash 索引的效率会很低，因为存在 Hash 碰撞的问题。



## 三、B+Tree 叶节点可以存储的数据(聚簇、非聚簇)

可以存储整行数据，也可以存储主键的值

**存储整行数据的是主键索引，也被称为聚簇索引**

**存储主键的值是非主键索引，也被称为非聚簇索引**



## 四、聚簇索引和非聚簇索引查询的区别

聚簇索引会更快一些，因为聚簇一次就查询了整行数据，而**非聚簇索引则需要再次通过主键查询一次(这个操作也叫做回表)**。

**但是回表也不一定，通过覆盖索引也可以只查询一次**

一个查询语句的执行只从索引中就能够取得，不必从数据表中读取，就可以称之为实现索引覆盖。当一条查询语句符合覆盖索引条件时，MySQL 只需要通过索引就可以返回查询所需要的数据，避免查询到索引后返回表操作，减少 I/O，提高效率。

例如 covering_index_sample 有一个普通索引 idx_key1_key2(key1,key2)

```sql
SELECT key2 FROM covering_index_sample WHERE key1 = 'keytest'
```

上述 SQL 就可以通过覆盖索引查询，无需回表



## 五、创建索引考虑的因素(联合索引、最左匹配)

联合索引、最左前缀匹配

**创建多列索引时，根据业务需求，WHERE 子句中使用最频繁的一列放到最左边**，因为 MySQL 索引查询会遵循最左前缀匹配的原则，即最左优先。**所以创建一个联合索引(key1,key2,key3)，相当于创建 (key1)、(key1,key2)、(key1,key2,key3) 三个索引，这就是最左匹配原则。**



## 六、索引下推、查询优化(MySQL5.6的优化)

例如表 people 有个联合索引(zipcode,lastname,address)，在使用下面 SQL

```SQL
SELECT * 
FROM people 
WHERE zipcode='76871' 
AND lastname LIKE '%Draper%' 
AND address LIKE '%Main Street%'
```

如果没有使用索引下推，则先查询 zipcode='76871' 的数据返回到服务端，然后进行后面的 lastname LIKE '%Draper%' 以及后面的 LIKE 进行匹配

如果使用索引下推，则先查询 zipcode='76871' 的**索引**，然后查看索引是否匹配后面的 LIKE，如果匹配则定位数据，如果不匹配，直接拒绝



## 七、查询优化器

一条 SQL 语句的查询，可以有不同的执行方案，值域最终选择哪一种方案啊，则需要通过优化器进行选择。**在一条单表查询语句真正执行之前，MySQL 先找出所有可能使用的方案，然后找出成本最低的方案就是所谓的执行计划**。优化过程大致如下

1. 更根据搜索条件，找出所有可能使用的索引
2. 计算全表扫描的代价
3. 计算使用不同索引执行查询的代价
4. 对比各种执行方案的代价，找出成本最低的那一个





## 八、拓展

上面的理解有些都很片面，都是基础知识，有些问题基本是没有回答

1. Inno db 如何保存数据和表结构
2. 如何构建 B+Tree
3. 如何管理和读取文件