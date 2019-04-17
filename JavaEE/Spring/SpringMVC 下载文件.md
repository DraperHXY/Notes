

# SpringMVC 下载文件



核心代码：

```java
BufferedInputStream bis = null;
BufferedOutputStream bos = null;
try {
    bis = new BufferedInputStream(new FileInputStream(filePath));
    bos = new BufferedOutputStream(out);
    byte[] buff = new byte[2048];
    int bytesRead;
    while (-1 != (bytesRead = bis.read(buff, 0, buff.length))) {
        bos.write(buff, 0, bytesRead);
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
} catch (IOException e) {
    e.printStackTrace();
} finally {
    close(bis, bos);
}
```



**注意 Controller 不需要返回值，只需要 OutputStream 进行输出就可以完成目的**