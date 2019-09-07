# Java中的位运算以及技巧

## 一、&（按位与运算）

* 0 & 0 = 0
* 0 & 1 = 0
* 1 & 0 = 0
* 1 & 1 = 1

### 技巧

#### 1. 清零

```java
System.out.println("清零");
System.out.println(Integer.toBinaryString(55));
System.out.println(55 & 0);
System.out.println();
```

#### 2. 取一个数中指定位

```java
System.out.println("去一个数中指定位");
System.out.println(Integer.toBinaryString(55));
System.out.println(((55) & (1 << 3)) >> 3); // 右数第四位为 0
System.out.println();
```



## 二、|（按位或运算）

* 0 | 0 = 0
* 0 | 1 = 1
* 1 | 0 = 1
* 1 | 1 = 1

### 技巧

#### 1. 将某些位置设为 1

```java
System.out.println("将第四个位置设为 1");
System.out.println(Integer.toBinaryString(55));
System.out.println(Integer.toBinaryString((55) | (1 << 3)));
System.out.println();
```



## 三、^（异或运算符）

* 0 ^ 0 = 0
* 0 ^ 1 = 1
* 1 ^ 0 = 1
* 1 ^ 1 = 0

### 技巧

#### 1. 翻转一个数

```java
System.out.println("翻转指定位数");
System.out.println(Integer.toBinaryString(55));
System.out.println(Integer.toBinaryString(55 ^ 0xF));// 使低四位翻转
System.out.println();
```

#### 2. 与 0 异或保留原值

```java
System.out.println("与 0 异或保留原值");
System.out.println(Integer.toBinaryString(55));
System.out.println(Integer.toBinaryString(55 ^ 0));
System.out.println();
```



## 四、~（取反一个数）

* ~1 = 0
* ~0 = 1

### 技巧

#### 1.取反

```java
System.out.println("取反一个数");
System.out.println(Integer.toBinaryString(55));
System.out.println(Integer.toBinaryString(~55));
System.out.println();
```

#### 2. 使一个数最低位变为 0

```java
System.out.println("使一个数最低位变为 0");
System.out.println(Integer.toBinaryString(55));
System.out.println(Integer.toBinaryString(55 & ~1));
System.out.println();
```



## 五、<<（左移位）

使二进制各个位全部左移指定位数，右边用 0 补齐

### 技巧

#### 1. 左移一位相当于 * 2，左移两位相当于 * 2 * 2

```java
System.out.println("左移乘2");
System.out.println(Integer.toBinaryString(55));
System.out.println(Integer.toBinaryString(55 << 1));
System.out.println(Integer.toBinaryString(55 << 2));
System.out.println();
```



## 六、>>（右移位）

使二进制各个位全部右移指定位数，左边用 0 补齐，**但是符号位不动**

```java
System.out.println("右移除2");
System.out.println(Integer.toBinaryString(55));
System.out.println(Integer.toBinaryString(55 >> 1));
System.out.println(Integer.toBinaryString(55 >> 2));
System.out.println(55 >> 2);
System.out.println();
```



## 七、>>>（无符号右移位）

右移可能会导致一个负数变成正数，所以有了无符号和有符号的区分

```java
System.out.println("有符号右移");
System.out.println(Integer.toBinaryString(-1 >> 1));
System.out.println((-1 >> 1));

System.out.println("无符号右移");
System.out.println(Integer.toBinaryString(-1 >>> 1));
System.out.println((-1 >>> 1));
```



没有无符号左移，<< 和 <<< 是一样的，符号位被覆盖来决定是正是负





