# Docker



## 背景

为了减少重复的环境配置工作的一个容器

有了高可用的沙箱机制就离高可用更进了一步



## Docker 核心概念

使用Docker的步骤：

1）、安装Docker

2）、去Docker仓库找到这个软件对应的镜像；

3）、使用Docker运行这个镜像，这个镜像就会生成一个Docker容器；

4）、对容器的启动停止就是对软件的启动停止；



### Docker 引擎

Docker 引擎是一个包含以下主要最贱的客户端服务器应用程序

* 一种服务器，它是一种称为守护进程并且长时间运行的程序

* REST API 用于指定程序可以用来与守护进程通信的接口，并指示它做什么

  > 使用 RESTful  API 来调用程序的 API

* 一个有命令行（CLI ）工具的客户端

![](https://docs.docker.com/engine/images/engine-components-flow.png)

- docker 镜像(images) 镜像用来创建 Docker 容器的模板

- docker 容器(Container) 容器是独立运行的一个或一组应用

  > 在一台机器上运行多个应用，可以实现负载均衡等，Docker 也可以成为

- docker 客户端(Client) 客户端通过命令行或者其他工具使用 Docker

  > * build 请求守护进程，守护进程构建一个镜像
  > * pull 拉取
  > * run 运行

- docker 仓库(Registry) Docker 仓库用来保存镜像，可以理解为代码控制中的代码仓库

- docker 主机(Host) 安装 Docker 程序的机器，用于执行 Docker 守护进程和容器

  > 可以是一个物理机或者虚拟机，***Docker 中还可以装 Docker***

* docker 网络(Network)
* docker 数据卷(Data Volumes)



### Docker 架构

Docker 使用客户端-服务器(C/S)架构模式，使用远程 API 来创建管理 Docker 容器

Docker  容器通过 Docker 镜像来创建

容器与镜像的关系类似于 OOP 中的对象与类



### Docker 镜像

操作系统分为内核和用户空间，对于 Linux 而言，内核启动后，会挂在 ``root`` 文件系统为其提供用户空间支持。而 Docker 镜像(Image) 相当于一个 root 文件系统。比如官方镜像 ``ubuntu:16.04`` 包含一套完成 Ubuntu 16.04 最小系统的 root 文件系统



Docker 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序，库，资源，配置等文件外，还包含了一些为运行时准备的一些配置参数(如匿名卷，环境变量，用户等)。镜像不包含任何动态数据，其内容在构建之后也不会改变。



#### 分层存储

因为镜像包含操作系统 root 文件系统，其体积也是庞大的，因此在 Docker 设计时，就充分利用 ``Union FS`` 技术，将其设计为分层存储的架构。所以严格来说，镜像并非像一个 ISO 那样打包的文件，镜像只是一个虚拟概念，其实际提现并非由一个文件组成，而是由一组文件系统组成，或者说，由多层文件系统联合组成。



镜像构建时，会以此封层构建，前一层时后一层的基础，每一层构建完就不会发生改变，后一层上的改变只发生在自己这一层。比如删除前一层文件的操作，实际不是删除前一层的文件，而是仅仅在当前层标记为该文件已删除。在最终容器运行的时候，虽然不会看见这个文件，但是实际上该文件会一致跟随镜像。因此在构建镜像的时候，需要额外小心，每一层尽量只包含该层需要添加的东西，任何额外的东西应该在该层构建结束前清理掉。



分层存储的特征还使得镜像的复用，定制变得更为容易。甚至可以用之前构建好的镜像作为基础层，然后进一步添加新的层，以定制所需的内容，构建新的镜像。

(**可以理解成 Java 的继承**)



### Docker 容器

镜像（Image） 和容器 （Container） 的关系，就相当于 OOP 中类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。



容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的命名空间，因此容器可以拥有自己的 root 文件系统、自己的网络配置、自己的进程空间、甚至自己的用户 ID 空间。容器内的进程是运行在一个隔离的环境里，使用起来，就好像在一个独立于宿主的系统下操作一样。这种特性也是容器封装的应用比直接在宿主运行更加安全。也因为这种隔离的特性，许多人会搞混容器和虚拟机



镜像使用的是分层存储，容器也是如此，每一个容器运行时，是以镜像为基础层，在其上创建一个当前容器的存储层，我们可以称这个容器为运行时读写而准备的容器存储层。



容器存储的生存周期和容器一样，容器消亡时，容器存储也随之消亡。因此，任何保存于存储层的信息都会随容器删除而丢失。



**按照 Docker 最佳实践的要求，容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化，所有的文件写入操作，都应该使用 ``数据圈(Volume)``，或者绑定宿主目录，在这些位置的读写会跳过容器存储层，直接对宿主（或网络存储）发生读写，其性能和稳定性更高**

> 虚拟机的文件存储在放虚拟机的地方，由虚拟机管理，会发生一个重复写的操作，性能明显弱于宿主机，使用 Docker 使用避免重复 IO，使性能接近宿主机。

数据卷的生命周期独立于容器，容器消亡，数据卷不会向往，因此，使用数据卷后，容器删除或者重新运行之后数据却不会丢失。 



### Docker 仓库

镜像构建完成后，可以很容易的在当前宿主机上运行，但是，如果需要在其他服务器上使用这个镜像，我们就需要一个集中的存储，分发镜像服务， ``Docker Registry`` 就是这样的服务



一个 Docker Registry 中可以包含多个仓库 （``Repository``）；每个仓库可以包含多个标签（``Tag``），每个标签对应一个镜像。



通常一个仓库包含同一个软件不同版本镜像，而标签就常用于对应该软件的各个版本。我们可以通过 ``<仓库名>:<标签>`` 的格式来指定具体软件的版本镜像，如果不给出标签，将以 ``latest`` 作为默认标签。

#### 公有 Docker Registry

最常用的 Registry 公开服务是官方的  DockerHub，这也是默认的 Registry，还有 CoreOS 的 Quay.io

#### 私有 Docker Registry

开源的 Docker Registry 镜像只提供了 Docker Registry API 的服务端实现，足以支持 ``docker 命令``但不包含图形界面，以及镜像维护，用户管理，访问控制。在官方的商业化版本 Docker Trusted Registrty 中提供高级功能。



## Docker 操作

### docker ps

查看正在运行的程序

```bash
rapers-MBP:~ draper$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
11981671e633        ubuntu:16.04        "bash"              3 seconds ago       Up 2 seconds                            adoring_keller
```



### docker ps -a

如果没有使用 ``--rm``查看所有容器则会如下所示

```bash
rapers-MBP:~ draper$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                           PORTS               NAMES
11981671e633        ubuntu:16.04        "bash"              4 minutes ago       Up 4 minutes                                         adoring_keller
a26d7fab4d83        tomcat              "catalina.sh run"   About an hour ago   Exited (130) About an hour ago                       admiring_swirles
5b660a81eed8        hello-world         "/hello"            3 hours ago         Exited (0) 3 hours ago                               sad_heisenberg
```



### docker rm

```bash
rapers-MBP:~ draper$ docker rm 5b660a81eed8
5b660a81eed8
```



### docker rm --force

```bash
Drapers-MBP:~ draper$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                           PORTS               NAMES
11981671e633        ubuntu:16.04        "bash"              14 minutes ago      Up 14 minutes                                        adoring_keller
a26d7fab4d83        tomcat              "catalina.sh run"   About an hour ago   Exited (130) About an hour ago                       admiring_swirles
Drapers-MBP:~ draper$ docker rm --force 11981671e633
11981671e633
Drapers-MBP:~ draper$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                           PORTS               NAMES
a26d7fab4d83        tomcat              "catalina.sh run"   About an hour ago   Exited (130) About an hour ago                       admiring_swirles
```



### docker pull

```bash
docker pull ubuntu:16.04
```

> 一般可以是 ``docker pull ip:port/reponsitory:tag``



### docker run

```bash
docker run -it --rm \
ubuntu:16.04 \
bash
```



* ``it`` 这是两个参数 ``-l``:交互式操作，``-t``: 进入``终端``

* ``--rm`` 退出容器后将其删除

  > 一般情况下，退出容器不会立即删除，而是手动 ``docker rm`` 删除

* ``ubuntu:16.04`` 我们需要启动的镜像

* ``bash`` 我们需要的交互式终端，指定用 ``bash``

> run 代表启动新容器，容易触发隔离机制
>
> 用交互的方式启动容器和用交互的方式进入容器并不相同，所以下面列出用交互的方式进入容器
>
> ```bash
> docker exec -it <CONTENT ID> bash
> ```
>
> 



> ```bash
> docker run -p 80:8080 tomcat
> ```
>
> 用宿主机 80 端口映射到 8080 端口

### docker system df

查看镜像，容器，数据卷所占用的空间

> docker hub 上面的体积一般是经过压缩的，所以本地的体积会比 docker hub 上大

> 使用 ``docker image ls`` 的体积经过了继承，复用，所以实际磁盘消耗大小要比 docker image ls`` 的体积小很多



### docker image ls -f dangling=true 

用于显示虚悬镜像，随着官方镜像维护，旧镜像的名称被取消，原来的镜像标签则会变为 ``<none>``

> docker pull 或者 build 会出现这种问题



### docker image ls -a

用于显示中间层镜像



### docker image ls

显示顶级镜像



### docker image rm

* 删除镜像，可以用 ``docker rmi`` 代替
* 删除镜像前应该先删除容器



### docker image prune

删除虚悬镜像

> 如果删除不了 会用 ``docker ps``， ``docker ps -a`` 来检查容器是否关闭



## 使用 Dockerfile 来定制化容器

使用 Dockerfile 要对分层存储结构有了解，连续两个命令不是同一个运行环境，所以 ``RUN cd webapps`` 这种命令会失效，需要用 ``WORKDIR`` 命令代替。

### WORKDIR <dir>

将该 dir 作为工作的指定目录



### docker build -t myshop .

将当前目录打包

> ```dockerfile
> FROM tomcat
> WORKDIR /usr/local/tomcat/webapps/ROOT
> RUN rm -fr *
> COPY <FILE.zip> .
> RUN unzip <FILE.zip>
> RUN rm -fr <FILE.zip>
> WORKDIR /usr/local/tomcat
> ```





```bash
docker run -p 8080:8080 --name tomcat -d -v /usr/local/docker/tomcat/ROOT:/usr/local/tomcat/webapps/ROOT
```

* ``-p`` 指定端口映射
* ``--name`` 指定别名
* ``-d`` 后台运行
* ``-v`` 指定数据卷



```bash
docker run -p 3306:3306 --name mysql \
-v /usr/local/docker/mysql/conf:/etc/mysql \
-v /usr/local/docker/mysql/logs:/var/log/mysql \
-v /usr/local/docker/mysql/data:/var/lib/mysql \
-e MY_ROOT_PASSWORD=123456 \
-d mysql
```



docker-compose.yml

```yaml
version: '3'
services:
  tomcat:
    restart: always
    image: tomcat
    container_name: tomcat
    ports: 
      - 8080:8080
  mysql:
    restart: always
```





* docker-compose up
* docker-compose up -d
* docker-compose logs tomcat
* docker-compose logs -f tomcat

此外 docker-compose 还有许多其他命令，例如 ``down`` ,``exec``,``image``,``kill``


发现了这个原始资料[Docker — 从入门到实践](https://yeasy.gitbooks.io/docker_practice/)



提供一些常用的模板文件

```yaml
version: "3.1"
services:
  web:
    restart: always
    image: tomcat
    container_name: web
    ports:
      - 8080:8080
    volumes:
      - /usr/local/docker/ROOT:/usr/local/tomcat/webapps/ROOT
  mysql:
    restart: always
    image: mysql:5.7.25
    container_name: mysql
    ports:
      - 3306:3306
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123456
    command:
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
      --max_allowed_packet=128M
      --sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO"
    volumes:
      - /var:/var/lib/mysql
volumes:
  mysql-data:
```



```yaml
version: "3.1"
services:
  web:
    restart: always
    image: tomcat
    container_name: web
    ports:
      - 8080:8080
    volumes:
      - /usr/local/docker/ROOT:/usr/local/tomcat/webapps/ROOT
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























