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

List 是 Redis 用的比较多的数据结构，简单理解相当于 Java 中的 LinkedList 功能强大，实现多

**可以做队列（右进左出）**``rpush`` ``lpop``

**可以做栈（右进右出）**``rpush`` ``rpop``



### 慢操作

都知道数组支持随机访问，查询效率比较高

而在 Redis 中，``lindex`` 相当于 Java 中的 ``get(int index)``，需要对链表进行遍历，效率随着 index 的增大而变差

Redis ``ltrim`` 方法有两个参数 (start_index, end_index) 作用是声明一个定长的链表



### 快速链表(QuickList)

Redis 底层并不仅仅是一个 LinkedList，在 list 元素较少的情况下，会使用一块内存连续的存储，这个结构即使 ``ziplist``，当数据较多，则会改成 ``quicklist``。**quicklist 就是将 ziplist 链表增加双向指针，将多个 ziplist 串起来，使得其效率增加**



## 三、Hash

Hash 用的是链地址法，其 refresh 的方式有点特殊。在 Java 中，refresh 是一个耗时的操作，**在 Redis 中为了保证高效，不堵塞，使用了一种渐进式 refresh 的策略** ，当 redis 进行 refresh 操作，则会新建一个 hash 结构，此时存在两种 hash 结构，old Hash 和 new Hash，当进行操作时，新旧同时进行，一步步将 old Hash 中的数据逐渐迁移到 new Hash 中，当最后一个 hash 被移除后，数据结构则会被销毁，内存被回收



在 Hash 中，存储的 value 只能是 String 类型，但是也能使用 ``hincryby`` 进行自增操作



## 四、Set

Set 相当于 Java 中的 HashSet ,可以存储中奖用户的 id，用来保证同一个用户不会中奖两次。



## 五、Zset

Zset 可以说是 Redis 提供的最具有特色的数据结构，类似于 Java 中 SortedSet 和 HashMap 的结合体。

一方面 Set 保证 value 的唯一性，另外一个字段 score 代表内部的权重，代表其 vaule 的排序权重

比如 value 可以存储粉丝 id，score 存储关注时间；value 存储学生 id，score 存储分数



### 跳跃链表(跳表)

zset 的底层实现是一个跳表，因为 zset 要支持随机读取和插入，数组明显不合适。

比如有一个普通的链表，当对 score 进行排序，可以使用二分查找法进行排序，但是二分查找的对象必须是数组。比如一个公司主键扩大，人员管理逐渐复杂，则慢慢有了什么部长、组长。这些部长，组长则是我们跳跃查找的定位点。

定位插入点，先在顶层定位，其次潜入下一层。链表元素越多，则深入的层次越深。



## 六、通用容器约定

list、set、hash、zset 这四种数据结构是容器型数据结构，他们共享下面两条规则

1. ``create if not exists`` 如果容器不存在，那就创建一个，再进行操作。比如 rpush 操作刚开始是没有列表的，Redis 会自动创建一个，然后再 rpush 进去新元素

2. ``drop if not elements`` 如果容器里的元素没有了，则立即删除容器，释放内存



## 七、注意过期时间

Redis 所有的数据结构都可以设置过期时间，过期时间到了，Redis 会自动删除相应对象，需要注意的是，**过期是以大的对象为单位的，比如一个 hash 结构是整个 hash 对象的过期，而不是某个子 key 对象的过期**

**还有，如果一个字符串已经设置了过期时间，然后调用 set 方法修改了它，则过期时间就会消失**

