主要内容

* Ubuntu 开放 docker daemon 端口
* IDEA 远程连接



#### Ubuntu 开放 docker daemon 端口

主要找 docker.service 这个文件，可能不知道在系统哪个地方

```bash
find / -name docker.service
```

找到了在 /lib/systemd/system/docker.service

在里面添加 -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock

总体如下所示

```bash
ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock
```



我使用的是阿里云，所以还需要配置安全组，开放 2375 端口

