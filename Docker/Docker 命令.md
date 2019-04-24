## 状态检查

> 在接触到 docker 的时候，就会一直伴随着对 docker 状态查看，类似于 本地有什么镜像、有运行的几个容器、状态怎么样、消耗多少内存

**先说下一个最基本的命令**

```bash
docker help
docker [COMMAND] --help		# 这两个命令结合起来用
```

```bash
docker images			# 检查本地有多少镜像
```

```bash
docker ps					# 检查正在运行的容器有哪些
```

```bash
docker stats			# 显示每个容器的内存,IO 等资源消耗
```



## 构建镜像

> 构建镜像有很多的方式，或者不许要镜像，但往往我们的项目比较复杂，环境需要定制，可能是 MySQL、JDK、Nginx 等
>
> **所以通常使用 Dockerfile 来构建镜像** 

[Dockerfile最佳实践](<https://docs.docker.com/develop/develop-images/dockerfile_best-practices/>)



### 一些基本命令

COPY 原样拷贝

ADD   拷贝解压



### 将项目制作成一个 image

Dockerfile

```dockerfile
FROM openjdk:8-jre
RUN mkdir /app
COPY itoken-sso-1.0.0-SNAPSHOT.jar /app/
CMD java -jar /app/itoken-sso-1.0.0-SNAPSHOT.jar
EXPOSE 8769
```

> **注意这里的路径**，很容易搞错

```bash
docker build -t itoken-sso .
```



## 快速部署

> 可能要设置暴露的接口，挂载文件的路径，比较麻烦
>
> * **所以通过 docker-compose 来部署**

``docker-compose.yml``

```yaml
mysql:
    restart: always
    image: mysql:5.7.25
    container_name: mysql
    ports:
      - 3306:3306
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: root
    command:
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
      --max_allowed_packet=128M
      --sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO"
    volumes:
      - /var:/var/lib/mysql
      - /usr/local/docker/mysql/conf:/etc/mysql
      - /usr/local/docker/mysql/logs:/var/log/mysql
      - /usr/local/docker/mysql/data:/var/lib/mysql
```

```yaml
docker-compose up       # 部署，显示日志
```

```bash
docker-compose up -d    # 部署，放置后台进行
```





