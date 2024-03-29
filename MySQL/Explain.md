# Explain

explain 翻译就是解释的意思，在 MySQL 中被称作执行计划。

通常是在慢查询前面加入  explain 来进行分析，所以分析里面的字段有些是我们必须要懂的，这里就来解释一些比较重要的字段





## 一、type

type 表示 MySQL 访问数据的方式，常见的有以下几种

* ``all`` 全表扫描
* ``index`` 遍历索引
* ``range`` 区间查询
* ``ref``、``eq_ref`` 常量和等值查询
* ``const`` 等值查询
* ``system`` 当表中只有一条记录时

下面是效率的降序排序

```
system > const > eq_ref > ref > range > index > all
```



## 二、 key

表示查询过程实际会用到的索引名称



## 三、rows

表示查询过程中可能需要扫描的行数，这个数据不一定准确，是 MySQL 抽样统计的一个数据



## 四、extra

表示一些额外信息，通常会显示是否使用了索引，是否需要排序，是否用到临时表等。

```
Using where; Using index; Using filesort; Using temporary
```



看一条 SQL 的性能，主要看**回表**、**使用索引**、**索引覆盖**

[这句简单的sql，如何加索引？颠覆了我多年的认知](https://juejin.im/post/5d6881d4f265da03ab426341)