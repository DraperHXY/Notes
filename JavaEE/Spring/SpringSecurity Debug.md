SpringSecurity DEBUG



## 一、 注意看方法栈的调用



![img](../../img/spring-securityx.png)



![image-20190429125826669](../../img/image-20190429125826669.png)



``ProviderManager`` 这个类作为所有的 AuthenticationProvider 的管理

![image-20190429130332021](../../img/image-20190429130332021.png)

可以看到有两个 provider，其中一个是 提供的匿名 provider，另外一个是我自定义的 DaoAuthenticationProvider

