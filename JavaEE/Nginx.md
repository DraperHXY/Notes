# 故障迁移



设置健康检测，用户会通过健康检测，检查当前的服务器是否可用，如果发现服务器宕机，则在当前周期不会再将请求发给故障机，直到下个周期，如果故障机修复，可以在下一个周期启动服务。

```conf
server localhost:8091 max_fails=1 fail_timeout=60s;
server localhost:8092 max_fails=1 fail_timeout=60s;
server localhost:8093 max_fails=1 fail_timeout=60s;
```

