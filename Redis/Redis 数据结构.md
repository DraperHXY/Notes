# Redis 数据结构

## 一、String

动态字符串，Redis 申请的内存空间会比实际的大一些，在字符串小于 **1M** 时，扩容都是成倍的增加，字符串最大容量为 **512M**

可以使用 ``expire`` 过期，例如 ``expire name 5`` 

> 这里有个有趣的问题，Redis 是支持持久化的，假设在 set 后，expire 前，redis 挂了，持久化回来，有一个字符串**迟迟不过期**，该如何处理
>
> ```
> setex name 5 Draper
> ```
>
> 使用 ``setex`` 等于 ``set`` + ``expire``，相当于把一个操作原子化了



计算场景下 ``set age 30`` 使用 ``incr age`` 增加 1，使用 ``incrby age 5`` 增加 5，**使用 ``incrby age -5``** 减 5。

**字符串由多个字节组成，每个字节 8bit**，一个字符串可以看成多个 bit 的组合，这便是 ``bitmap(位图)`` 的数据结构



## 二、List



## 三、Hash



## 四、Set



## 五、Zset

