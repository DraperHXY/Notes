> 吃一蛰长一智，感觉在 maven 的运用上面花了浪费很多时间...在这里记录一下



### [Spring Boot Maven Plugin](https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/maven-plugin/)

```xml
<!-- Spring boot maven plugin -->
<!-- goals -->
<!-- 1. spring-boot:run runs your Spring Boot application -->
<!-- 2. spring-boot:repackage repackages your jar/war to be executable -->
<!-- 3. spring-boot:start and spring-boot:stop to manage the lifecycle of your Spring Boot application (i.e. for integration tests) -->
<!-- 4. spring-boot:build-info generates build information that can be used by the Actuator -->
<!-- https://docs.spring.io/spring-boot/docs/2.1.3.RELEASE/maven-plugin/ -->
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <mainClass>com.draper.itoken.sso.SsoApplication</mainClass>
    </configuration>
</plugin>
```

#### 4 Goals

* 运行 Spring Boot 程序
* 将应用打成 jar 或者 war
* 进行集成测试
* 通过 actuator 生成信息(感觉功能好强大，当然一般通过 api 来调用生成)



在 Spring Boot 中，这一插件的作用是打成了 **可执行 Jar**

在给被依赖包，例如 core 包，util 包，等没有 main class 的包，只需要把这个去掉就可以了

> 我就 fuck 了





###[maven-surefire-plugin](http://maven.apache.org/surefire/maven-surefire-plugin/)

``` xml
<!-- SureFire plugin -->
<!-- The Surefire Plugin has only one goal: -->
<!-- surefire:test runs the unit tests of an application -->
<!-- http://maven.apache.org/surefire/maven-surefire-plugin/ -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <configuration>
        <skip>true</skip>
    </configuration>
</plugin>
```

只有一个功能，**测试**，跟测试有关的功能，例如忽略测试失败，跳过测试等..

