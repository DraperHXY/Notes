# Java 类的加载机制



有三个主要原因是我们学习类的加载机制的理由

1. 部署在同一个服务器上的两个 Web 应用程序所使用的 Java 类库可以实现相互隔离
2. 部署在同一个服务器上的两个 Web 应用程序的类库可以相互共享
3. 支持热替换
4. 类加密

 

### 1. 加载 .class 文件的方式

- 从本地直接加载
- 通过网络下载 .class
- 从 zip，jar 等归档文件中加载 .class 文件
- 从专有数据库中提取 .class 文件
- 将 Java 源文件动态编译为 .class 文件

### 2. 类的生命周期

- 加载
- 连接
  - 验证
  - 准备
  - 解析 
- 初始化
- 使用
- 卸载

在这几个阶段中，加载、验证、准备和初始化这四个阶段的顺序是确定的，而解析阶段则不一定，它在某些情况下可以在初始化之后开始，这是为了支持 Java 语言的运行时绑定（也称为动态绑定或晚期绑定）。

另外，**初始阶段是按照顺序开始，但不是按照顺序进行或完成**，因为这些阶段通常都是互相交叉地混合进行的，通常在一个阶段执行的过程激活另外一个阶段。



### 3. 加载指的是什么？

- 通过一个类的全限定名来获取其定义的二进制字节流
- 将字节流锁代表的静态存储结构转化为方法区的运行时数据结构
- 在 Java 对中生成一个代表这个类的 ``java.lang.Class`` 对象，作为方法区汇总这些数据的访问入口

相比起加载的其他阶段而言，加载阶段(准确地说，是加载阶段获取类的二进制字节流的动作)是可控性最强的阶段，因为**开发人员既可以使用系统提供的类加载器来完成加载，也可以自定义的类加载器进行加载**

加载完成后，虚拟机外部的二进制字节流按照虚拟机所需的格式存储在方法区中，而且在堆中也创建一个 ``java.lang.Class`` 类的对象，这样便可以通过该对象访问方法区中的这些数据。

### 4. 连接指的是什么

连接有三个步骤

- 验证 确保被加载的类的正确性，例如 ``0xCAFEBABE`` 、 编译版本号等等。
- 准备 为类的静态变量分配内存，并将其初始化为默认值，如 int 为 0，对象为 null。
- 解析 把类中的符号引用转换为直接引用

 

### 5. 初始化指的是什么

初始化，为类中的静态变量赋予正确的初始值，JVM 负责对类进行初始化，主要对类变量进行初始化。在 Java 中对类变量进行初始值设定有两种方式：

- 声明类变量是指定初始值
- 使用静态代码块为类变量指定初始值

JVM 初始化步骤

- 假如这个类还没有被加载和连接，则程序先加载并连接该类
- 假如这个类的直接父类还没有初始化，则先初始化其直接父类
- 假如这个类中有初始化语句，则系统一次执行这些初始化语句



### 6. 初始化的时机

不过 JVM 规范中没有强制约束加载的时机，不过对于 JVM 规范严格规定了有且仅有 5 中情况会必须立即对类进行初始化

只有当对类的主动使用的时候才会导致类的初始化，类的主动使用包括以下几种：

- 创建类的实例，也就是 new 的方式，字节码指令 ``new``。
- 访问某个类或接口的静态变量，或者对该静态变量赋值，字节码指令 ``getstatic``。
- 调用类的静态且**final**方法，字节码指令 ``putstatic``。或者仅调用静态方法，字节码指令 ``invokestatic``。
- 反射(如 ``Class.forName("com.draper.Main")``)
- 初始化某个类的子类，则其父类也会被初始化
- Java 虚拟机启动时被标明为启动类的类，直接使用 ``java.exe`` 命令来运行某个主类







### 7. 结束生命周期

- 执行了 ``System.exit()`` 方法

- 程序正常的执行结束

- 程序在执行过程中遇到了异常或错误而异常终止

- 由于操作系统出现错误而导致 Java 虚拟机进程终止

  

### 8. 类加载器

- ``BootStrap Classloader `` 顶级类加载器，采用C/C++ 实现，在 Java 中无法通过 loader.getParent() 来引用。加载 ``JDK\jre\lib`` 下，或被 ``-Xbootclasspath`` 参数自定的路径中的，并且能被虚拟机识别的类库。
- ``ExtClassLoader`` 次顶层类加载器，扩展类加载器，加载 ``JDK\jre\lib\ext`` 目录，或者由 ``java.ext.dirs`` 系统变量指定的路径中所欲类库。
- ``AppClassLoader `` 应用类加载器，一般默认的类加载器就是这个
- ``UserClassLoader`` 用户自定义类加载器

### 9. 类的加载方式

1. 命令行启动应用时由 JVM 初始化加载
2. 通过 ``Class.forname()`` 方法动态加载
3. 通过 ``ClassLoader.loadClass()`` 方法动态加载



### 10. 双亲委派机制

[JVM/类的双亲委派机制](../../JVM/类的双亲委派机制.md)



### 11. 如何自定义类加载器

1. 若要打破双亲委派模型，继承 ``java.lang.ClassLoader`` 并只重新 ``loadClass `` 方法
2. 若不想打破双亲委派模型，那么就重写 ``findClass`` 方法即可



参考：
[浅析JVM（一）Class文件&类加载机制](https://www.jianshu.com/p/5f3278916b38)（写的太好了..）

