## 1. docker history 查看镜像构建完整信息


```shell
docker history image_name --no-trunc=true
```


## 2.docker 列出所有容器 IP

```shell
 docker inspect -f='{{.Name}} {{.NetworkSettings.IPAddress}} {{.HostConfig.PortBindings}}' $(docker ps -aq)
```



