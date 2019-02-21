```java
public static void main(String[] args) {
    Coffee coffee = new MyMocha();
    if (coffee instanceof Coffee){
        System.out.println(true);
    }
    if (coffee instanceof  Mocha){
        System.out.println(true);
    }
    if (coffee instanceof  MyMocha){
        System.out.println(true);
    }
    if (coffee instanceof MyMochaInterface){
        System.out.println(true);
    }
}
```

事实上面这段代码都会输出 true



instanceof 的字节码

* 操作： 确定对象是否为给定的类型

* 指令格式：``instanceof|indexbyte1|indexbyte2``

* 指令前后的栈顶状态：

  ……，objectref =>

  ……，result

* 描述：

  indexbyte1 和 indexbyte2 用于构造的常量池索引进行解析，然后根据 Java 规范判断解析的类是不是 objectref 的一个实例，最后在栈顶写入结果

  

