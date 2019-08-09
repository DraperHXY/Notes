# JVM命令

jps
jstat
jmap
jhat
jstack
jinfo

## 一、JPS

显示指定系统内所有的 HotSpot 虚拟机进程



## 二、jstat

监视虚拟机运行时状态信息的命令，它可以显示出虚拟机进程中的类加载、内存、垃圾收集、JIT 编译等进行时数据



## 三、jmap

用于生成 heap dump 文件，如果不使用这个命令，还可以使用 ``-XX:+HeapDumpOnOutOfMemoryError`` 参数让虚拟机出现 OOM 的时候，自动生成 dump 文件。

jmap 不仅能生成 dump 文件，还可以查询 finalize 执行队列、Java 堆和永久代的详细信息，如当前使用率、当前使用的是哪种收集器等。



## 四、jhat

与 jmap 搭配使用，jhat 内置一个微型的 HTTP/HTML 服务器，生成 dump 的分析结果后，可以在浏览器中查看。



## 五、jstack

用于生成当前时刻 java虚拟机当前时刻的线程快照。线程快照是当前 java 虚拟机内每一条线程正在执行的方法堆栈的集合，生成线程快照的主要目的是定位咸亨出现长时间停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待等。



## 六、jinfo

实时查看和调整虚拟机运行参数



[jvm系列(四):jvm调优-命令篇](https://mp.weixin.qq.com/s?__biz=MzI4NDY5Mjc1Mg==&mid=2247483966&idx=1&sn=dfa3375d36aa2c0c25a775522e381e62&chksm=ebf6da41dc815357e0d53c73865a23f41219e75bac5a4d510bfa31cc51594b59a20e2e4f6cb8&scene=21#wechat_redirect)