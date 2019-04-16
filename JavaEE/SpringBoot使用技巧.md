## 打包

* 若需要打成固定格式的 jar 可以使用 resources 来导资源



* 打 jar 包

如果只想打成一个普通的 jar 包，在 pom.xml 中如下操作就可以了，用 ``maven-jar-plugin`` 不太好使用。

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <mainClass>com.draper.exportexcel.ExportExcelApplication</mainClass>
    </configuration>
    <executions>
        <execution>
            <goals>
                <goal>repackage</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

**若没有入口函数，去掉 mainclass 参数即可**



## 运行

* ``java -jar XXXXXX.jar & `` - 可以后台运行
* ``java org.springframework.boot.loader.JarLauncher`` - 把 jar 包解压后，可以这样运行，几乎所有的 SpringBoot 项目都是这个样子的

