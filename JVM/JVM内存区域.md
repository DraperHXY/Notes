# JVM 内存区域



包括有五个部分

* 方法区(线程共享)
* 堆(线程共享)
* 虚拟机栈
* 本地方法栈
* 程序计数器



## 一、程序计数器

程序计数器是一块较小的内存空间。自己吗解释器工作时是通过改变计数器的值来决定下一条需要执行的自己吗指令。

因为 Java 多线程是通过轮流分配处理器的时间。所以在任何时刻都会有一个线程在处理器中执行指令。这就要求处理器在拿到线程后，需要找到准确执行的位置。所以**每个线程都需要有一个独立的程序计数器**。各个线程之间的计数器互不影响，所以程序计数器是线程私有的。



## 二、Java 虚拟机栈

虚拟机栈也是线程私有的，生命周期与线程相同。虚拟建站描述的是 Java 方法执行的内存模型：**每个方法在执行的同时，都会创建一个栈帧，用于存储局部变量表、操作数栈、动态链接和方法出口等信息**，每一个方法从调用直至结束都对应着一个栈帧在虚拟机中的入栈和出栈。

局部变量表存放了编译期可知的各种**基本数据类型**(boolean、byte、char、shot、int、float、long、double)、**对象引用**(refrerence 类型，他不等同于对象本身，可能是指向一个代表对象的句柄或者其他与此相关的位置)和** returnAddress 类型**(指向了一条字节码指令的地址)

**当进入一个方法是，需要在帧中分配多大的局部变量空间是完全确定的，在方法运行期间不会改变局部变量表的大小。**



## 三、本地方法栈

本地方法栈与虚拟机栈极其相似，只不过**虚拟机栈为虚拟机执行 Java 方法**，也就是字节码服务。**本地方法栈执行的是 Native 方法**

在虚拟机规范中，并没有对本地方法栈的语言，使用方式，数据结构有强制规定，因此具体的方法栈可以自由的实现它。升值有虚拟机(Sun HotSpot)将本地方法栈和虚拟机方法栈合二为一。



## 四、Java 堆

堆应该是 Java 虚拟机所管理的内存中最大的一块，Java 堆是被所有线程共享的一块内存曲剧，在虚拟机启动时创建。这块内存的**唯一目的就是存放对象实例**，几乎所有的对象都是在这儿分配内存。

Java 堆是垃圾收集器的主要区域，因此也被称为 GC 堆。Java 堆中还会进行更细的划分，这个以后再说。



## 五、方法区

与堆一样，各个线程共享的内存区域，**用来存储虚拟机加载的类信息、常量、静态常量，即时编译后的代码等数据。





