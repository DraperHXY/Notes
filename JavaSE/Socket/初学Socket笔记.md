

这一步会把线程堵塞

```java
Socket socket = socketServer.accept();
```



所以如果说 **Server** 需要接收来自多个 **Client** 的注册，那么需要将上面的阻塞代码，放在一个线程中死循环运行



牵扯到 socket 就离不开 input/output Stream 操作，这是比较常用的对流的装饰

```java
InputStream inputStream = socket.getInputStream();
OutputStream outputStream = socket.getOutputStream();
BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(System.out));
```

使用 Socket 之类的数据流进行处理避免使用 readLine(); 避免因为等待一个 \r \n 而一直阻塞



可以使用类似于下面这种代码，设置缓冲区，进行拷贝

```java
byte[] temBuffer = new byte[1024 * 5];  // 缓冲区
int len = in.read(temBuffer)
byte[] ins = new byte[len];
System.arraycopy(temBuffer, 0, ins, 0, len);
String str = new String(ins);
```



流操作别忘了关闭