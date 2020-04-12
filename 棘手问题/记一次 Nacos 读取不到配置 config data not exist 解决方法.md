# 记一次 Nacos 读取不到配置 config data not exist 解决方法

因为办公习惯用的是笔记本，方便在公司和家里随时转换，所以在公司本地启动了 Nacos 就再也没管过，但没想到在家解决一个其他问题的时候，居然发现从配置中心无法读取配置，但是应用确实注册了上去，在 [Nacos 官网 OpenAPI 指南](https://nacos.io/zh-cn/docs/open-api.html)中，使用 Postman 会给出一行信息 config data not exist

然后去 ${nacos_home}/logs/config_server.log 中查看日志 ERROR unhealthIp:192.168.11.103:8848, unhealthCount:26692 不停的答应这样的错误。仔细发现这个 ip 不是我当前的 ip，我的 nacos server 和 app client 都是在本地，所以重新启动了 nacos 就可以了(浪费了好长时间...)，也没阅读源码..不明白为什么会保存之前的 ip

