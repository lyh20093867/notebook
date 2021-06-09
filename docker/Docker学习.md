# Docker学习

## docker的优势

更高效的利用系统资源

更快速的启动时间

一致的运行环境

持续的交付和部署

更轻松的迁移

更轻松的维护和扩展

#### docker与传统虚拟机比较

| 特性       | Docker             | 虚拟机 |
| ---------- | ------------------ | ------ |
| 启动       | 秒级               |        |
| 硬盘使用   | 一般为MB           |        |
| 性能       | 接近原生           |        |
| 系统支持量 | 单机支持上千个容器 |        |
| 隔离性     |                    |        |



## docker的核心概念

### 镜像

docker镜像类似于虚拟机镜像xxx.iso，可以理解为一个只读的模板，镜像是创建Docker容器的基础

##### 层

镜像中有层的概念，使用docker image history image_name\i mage_id可以查看一个镜像的分层

> docker image history c0cdc95609f1

> (base) liyihong:mysql_latest yihong.li$ docker image history c0cdc95609f1
> IMAGE          CREATED       CREATED BY                                      SIZE      COMMENT
> c0cdc95609f1   4 weeks ago   /bin/sh -c #(nop)  CMD ["mysqld"]               0B
> <missing>      4 weeks ago   /bin/sh -c #(nop)  EXPOSE 3306 33060            0B
> <missing>      4 weeks ago   /bin/sh -c #(nop)  ENTRYPOINT ["docker-entry…   0B
> <missing>      4 weeks ago   /bin/sh -c ln -s usr/local/bin/docker-entryp…   34B
> <missing>      4 weeks ago   /bin/sh -c #(nop) COPY file:345a22fe55d3e678…   14.5kB
> <missing>      4 weeks ago   /bin/sh -c #(nop) COPY dir:2e040acc386ebd23b…   1.12kB
> <missing>      4 weeks ago   /bin/sh -c #(nop)  VOLUME [/var/lib/mysql]      0B
> <missing>      4 weeks ago   /bin/sh -c {   echo mysql-community-server m…   420MB
> <missing>      4 weeks ago   /bin/sh -c echo 'deb http://repo.mysql.com/a…   55B
> <missing>      4 weeks ago   /bin/sh -c #(nop)  ENV MYSQL_VERSION=8.0.25-…   0B
> <missing>      4 weeks ago   /bin/sh -c #(nop)  ENV MYSQL_MAJOR=8.0          0B
> <missing>      4 weeks ago   /bin/sh -c set -ex;  key='A4A9406876FCBD3C45…   2.61kB
> <missing>      4 weeks ago   /bin/sh -c apt-get update && apt-get install…   52.2MB
> <missing>      4 weeks ago   /bin/sh -c mkdir /docker-entrypoint-initdb.d    0B
> <missing>      4 weeks ago   /bin/sh -c set -eux;  savedAptMark="$(apt-ma…   4.17MB
> <missing>      4 weeks ago   /bin/sh -c #(nop)  ENV GOSU_VERSION=1.12        0B
> <missing>      4 weeks ago   /bin/sh -c apt-get update && apt-get install…   9.34MB
> <missing>      4 weeks ago   /bin/sh -c groupadd -r mysql && useradd -r -…   329kB
> <missing>      4 weeks ago   /bin/sh -c #(nop)  CMD ["bash"]                 0B
> <missing>      4 weeks ago   /bin/sh -c #(nop) ADD file:7362e0e50f30ff454…   69.3MB

一旦层发生变化，所有下游层也必须重新创建，必须重新安装yarn依赖项，yarn是js中的包依赖管理工具，类似java中的maven。

但是有层缓存，这样可以加快再次构建的速度。

##### 多阶段构建

- 将构建时依赖项与运行时依赖项分开
- 通过*仅*传送您的应用程序需要运行的内容来减少整体图像大小





##### dockerfile



### 容器

容器类似一个轻量级的沙箱，docker利用容器来运行和隔离应用；容器可以堪称是一个简易版的linux系统环境，但少了多余的东西，只有要运行的应用和必须的运行环境；是镜像的一个运行实例，镜像是静态的只读文件，容器带有运行时需要的可写文件层，同时容器中的应用进程处于运行状态。

Ø 镜像自身是只读的。 

Ø 容器从镜像启动的时候，会在镜像的最上层创建一个可写层。

#### 容器的文件系统

当容器运行时，它会将镜像中的各个层用于其文件系统。每个容器也有自己的“暂存空间”来创建/更新/删除文件。任何更改都不会在另一个容器中看到，*即使*它们使用相同的图像。

#### 容器的卷(volume)

容器每次启动时都是从镜像定义开始的。虽然容器可以创建、更新和删除文件，但是当容器被移除并且所有更改都与该容器隔离时，这些更改将丢失。

[卷](https://docs.docker.com/storage/volumes/)提供了将容器的特定文件系统路径连接回主机的能力。如果挂载了容器中的目录，则主机上也会看到该目录中的更改。如果我们在容器重新启动时挂载相同的目录，我们会看到相同的文件。

##### 命名卷

###### 创建命名卷

```
docker volume create volume_name
```

docker volume create todo-db

```shell
docker run -dp 3000:3000 -v todo-db:/etc/todos getting-started
```

###### 查看卷内内容

```shell
docker volume inspect todo-db
```

###### 例子

docker volume create first_volume

docker volume ls

centos:7.5.1804为镜像的名

docker run -dp 3000:3000 -v  first_volume:/etc/todos centos:7.5.1804

docker volume inspect first_volume

##### bind mount(绑定挂载)

##### 跟命名卷的对比

| Named Volumes                                | Bind Mounts               |                               |
| :------------------------------------------- | :------------------------ | ----------------------------- |
| Host Location                                | Docker chooses            | You control                   |
| Mount Example (using `-v`)                   | my-volume:/usr/local/data | /path/to/data:/usr/local/data |
| Populates new volume with container contents | Yes                       | No                            |
| Supports Volume Drivers                      | Yes                       | No                            |

 运行容器支持开发工作流 

- 将我们的源代码挂载到容器中
- 安装所有依赖项，包括“dev”依赖项
- 启动 nodemon 以监视文件系统更改

运行命令

```shell
docker run -dp 3000:3000 \
     -w /app -v "$(pwd):/app" \
     node:12-alpine \
     sh -c "yarn install && yarn run dev"
```

- `-dp 3000:3000`- 和之前一样。在分离（后台）模式下运行并创建端口映射
- `-w /app` - 设置“工作目录”或命令将运行的当前目录
- `-v "$(pwd):/app"`- 将容器中宿主机的当前目录绑定挂载到`/app`目录中
- `node:12-alpine`- 要使用的图像。请注意，这是来自 Dockerfile 的应用程序的基本映像
- `sh -c "yarn install && yarn run dev"`- 命令。我们正在使用`sh`（alpine 没有`bash`）启动一个 shell并运行`yarn install`以安装*所有*依赖项，然后运行`yarn run dev`. 如果我们查看`package.json`，我们将看到`dev`脚本正在启动`nodemon`。

查看日志

```shell
 docker logs -f <container-id>
 $ nodemon src/index.js
 [nodemon] 1.19.2
 [nodemon] to restart at any time, enter `rs`
 [nodemon] watching dir(s): *.*
 [nodemon] starting `node src/index.js`
 Using sqlite database at /etc/todos/todo.db
 Listening on port 3000
```

#### 容器网络

默认情况下，容器是独立运行的，并且对同一台机器上的其他进程或容器一无所知。那么，我们如何让一个容器与另一个容器通信呢？答案是 **网络**

如果两个容器在同一个网络上，它们可以相互通信。如果他们不是，他们就不能。

#### docker安装mysql8

##### 拉取mysql镜像

docker search mysql

docker pull mysql

##### 创建网络

 docker network create mysql-app

查看已经有的网络

docker network ls

> (base) liyihong:mysql_latest yihong.li$ docker network ls
> NETWORK ID     NAME          DRIVER    SCOPE
> 11f27eb352c4   bridge        bridge    local
> bd13c3393bd6   host          host      local
> 619b83d1ede3   mysql-app     bridge    local
> 2eed59e547db   none          null      local
> d0172c8c1d51   test_es7net   bridge    local

> (base) liyihong:mysql_latest yihong.li$ docker network inspect mysql-app
> [
>     {
>         "Name": "mysql-app",
>         "Id": "619b83d1ede343dbae0383bda7ac7db8be6e259895893395aa2f61d973309826",
>         "Created": "2021-06-09T07:26:48.5393052Z",
>         "Scope": "local",
>         "Driver": "bridge",
>         "EnableIPv6": false,
>         "IPAM": {
>             "Driver": "default",
>             "Options": {},
>             "Config": [
>                 {
>                     "Subnet": "172.19.0.0/16",
>                     "Gateway": "172.19.0.1"
>                 }
>             ]
>         },
>         "Internal": false,
>         "Attachable": false,
>         "Ingress": false,
>         "ConfigFrom": {
>             "Network": ""
>         },
>         "ConfigOnly": false,
>         "Containers": {},
>         "Options": {},
>         "Labels": {}
>     }
> ]

##### 创建mysql容器

创建数据文件夹

mkdir -p /Users/yihong.li/Documents/study/docker/mysql/mysql_latest/data用于存放数据日志等文件

mkdir -p /Users/yihong.li/Documents/study/docker/mysql/mysql_latest/files用于存放文件

mkdir -p /Users/yihong.li/Documents/study/docker/mysql/mysql_latest/conf用于存放配置文件

在/Users/yihong.li/Documents/study/docker/mysql/mysql_latest/mysql/conf目录下创建一个自定义的配置文件my.cnf



```shell
docker run --name mysql-latest --network mysql-app \
--network-alias mysql --restart=always \
-v /Users/yihong.li/Documents/study/docker/mysql/mysql_latest/conf:/etc/mysql/conf.d \
-v /Users/yihong.li/Documents/study/docker/mysql/mysql_latest/data:/var/lib/mysql \
-v /Users/yihong.li/Documents/study/docker/mysql/mysql_latest/files:/var/lib/mysql-files \
-p 3307:3307 -e MYSQL_ROOT_PASSWORD=test_lyh -e MYSQL_DATABASE=todos -d mysql:latest
```

##### 查看创建结果

docker ps

>  (base) liyihong:mysql_latest yihong.li$ docker ps
> CONTAINER ID   IMAGE             COMMAND                  CREATED         STATUS         PORTS                                                            NAMES
> 17610cc3df25   mysql:latest      "docker-entrypoint.s…"   6 seconds ago   Up 5 seconds   3306/tcp, 33060/tcp, 0.0.0.0:3307->3307/tcp, :::3307->3307/tcp   mysql-latest
> 721fd2cd8a47   centos:7.5.1804   "bash -c 'shuf -i 1-…"   4 hours ago     Up 4 hours                                                                      beautiful_mayer
> 37a6b98df34c   centos:7.5.1804   "/bin/bash"              5 days ago      Up 5 days                                                                       serene_neumann

##### 查看安装的mysql版本

docker exec -it 17610cc3df25 mysql --version

查看mysql在容器中的日志

> docker logs 0910429bdf38

连接mysql

使用[nicolaka/netshoot](https://github.com/nicolaka/netshoot)容器，它附带了*许多*可用于故障排除或调试网络问题的工具,第一次运行容器，如果没有nicolaka/netshoot这个容器，会在仓库中下载

```shell
docker run -it --network mysql-app nicolaka/netshoot
```

使用 nicolaka/netshoot 镜像启动一个新容器。确保将其连接到同一网络

在容器内部，使用`dig`命令，查找主机名的 IP 地址`mysql`

```shell
dig mysql
```



连接刚才建好的mysql

```shell
docker run -dp 3000:3000 \
   -w /app -v "$(pwd):/app" \
   --network mysql-app \
   -e MYSQL_HOST=mysql \
   -e MYSQL_USER=root \
   -e MYSQL_PASSWORD=test_lyh \
   -e MYSQL_DB=todos \
   node:12-alpine \
   sh -c "yarn install && yarn run dev"
```





### 仓库

Docker 仓库类似于代码仓库，是 Docker 集中存放镜像文件的场所。

配置国内镜像源地址

Ø 网易

​       http://hub-mirror.c.163.com

 

Ø 中国科技大学

​       https://docker.mirrors.ustc.edu.cn

 

Ø 阿里云容器服务

​       https://cr.console.aliyun.com/

​       首页点击“创建我的容器镜像” 得到一个专属的镜像加速地址，类似于

​        https://abcdef.mirror.aliyuncs.com

## docker安装

Docker 目前支持 CentOS 7 及以后的版本, 内核版本必须是3.10.

cat /etc/redhat-release

uname -r



#### 验证docker是否可用

sudo docker run hello-world

## docker基本操作

### 容器基本操作

docker container --help

停止容器

docker container stop container-id

删除一个或者多个容器

docker container rm container-id

也可以使用docker rm container-id删除一个容器

启动一个centos容器，并且在容器中

docker run -d centos:7.5.1804 bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"

查看某个容器中的文件内容

docker exec -it 721fd2cd8a47 cat /data.txt

创建容器

docker create -i -t centos:7.5.1804 /bin/bash

-i表示容许你对容器内的标准输入进行交互

-t在新容器内指定一个伪终端或终端

启动容器

docker start bcc

可以直接使用run命令，创建并启动容器

docker run -it centos:7.5.1804 /bin/bash

查看有哪些启动的容器

docker ps

docker ps -a查看所有的容器

启动后台进程，添加-d参数

docker run -itd centos:7.5.1804 /bin/bash

docker run -dp 3000:3000 021a1b85e641

停止容器

docker stop bcc

删除容器

docker rm container-id

进入容器

docker exec -it bcc /bin/bash

导入和导出容器

下面的命令将容器中的xxx.file导出到容器外，存为xxx.tar的归档文件

docker export -o '$PATH/xxx.tar' xxx.file

导入容器

docker import --help

可以将一个文件或者url导入到容器中，会成为一个镜像

![image-20210603164401004](/Users/yihong.li/Library/Application Support/typora-user-images/image-20210603164401004.png)

查看容器

docker container inspect container-id

查看容器内进程

docker top 

查看统计信息

docker stats --no-stream container-id

容器和主机之间复制文件

docker cp file container:/

docker cp container:/file path



### 镜像基本操作

https://docs.docker.com/get-started/overview/

查看帮助文档

docker --help

> Usage:  docker [OPTIONS] COMMAND
>
> A self-sufficient runtime for containers
>
> Options:
>       --config string      Location of client config files (default "/Users/yihong.li/.docker")
>   -c, --context string     Name of the context to use to connect to the daemon (overrides DOCKER_HOST env var and default context set with "docker context use")
>   -D, --debug              Enable debug mode
>   -H, --host list          Daemon socket(s) to connect to
>   -l, --log-level string   Set the logging level ("debug"|"info"|"warn"|"error"|"fatal") (default "info")
>       --tls                Use TLS; implied by --tlsverify
>       --tlscacert string   Trust certs signed only by this CA (default "/Users/yihong.li/.docker/ca.pem")
>       --tlscert string     Path to TLS certificate file (default "/Users/yihong.li/.docker/cert.pem")
>       --tlskey string      Path to TLS key file (default "/Users/yihong.li/.docker/key.pem")
>       --tlsverify          Use TLS and verify the remote
>   -v, --version            Print version information and quit
>
> Management Commands:
>   app*        Docker App (Docker Inc., v0.9.1-beta3)
>   builder     Manage builds
>   buildx*     Build with BuildKit (Docker Inc., v0.5.1-docker)
>   config      Manage Docker configs
>   container   Manage containers
>   context     Manage contexts
>   image       Manage images
>   manifest    Manage Docker image manifests and manifest lists
>   network     Manage networks
>   node        Manage Swarm nodes
>   plugin      Manage plugins
>   scan*       Docker Scan (Docker Inc., v0.5.0)
>   secret      Manage Docker secrets
>   service     Manage services
>   stack       Manage Docker stacks
>   swarm       Manage Swarm
>   system      Manage Docker
>   trust       Manage trust on Docker images
>   volume      Manage volumes
>
> Commands:
>   attach      Attach local standard input, output, and error streams to a running container
>   build       Build an image from a Dockerfile
>   commit      Create a new image from a container's changes
>   cp          Copy files/folders between a container and the local filesystem
>   create      Create a new container
>   diff        Inspect changes to files or directories on a container's filesystem
>   events      Get real time events from the server
>   exec        Run a command in a running container
>   export      Export a container's filesystem as a tar archive
>   history     Show the history of an image
>   images      List images
>   import      Import the contents from a tarball to create a filesystem image
>   info        Display system-wide information
>   inspect     Return low-level information on Docker objects
>   kill        Kill one or more running containers
>   load        Load an image from a tar archive or STDIN
>   login       Log in to a Docker registry
>   logout      Log out from a Docker registry
>   logs        Fetch the logs of a container
>   pause       Pause all processes within one or more containers
>   port        List port mappings or a specific mapping for the container
>   ps          List containers
>   pull        Pull an image or a repository from a registry
>   push        Push an image or a repository to a registry
>   rename      Rename a container
>   restart     Restart one or more containers
>   rm          Remove one or more containers
>   rmi         Remove one or more images
>   run         Run a command in a new container
>   save        Save one or more images to a tar archive (streamed to STDOUT by default)
>   search      Search the Docker Hub for images
>   start       Start one or more stopped containers
>   stats       Display a live stream of container(s) resource usage statistics
>   stop        Stop one or more running containers
>   tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
>   top         Display the running processes of a container
>   unpause     Unpause all processes within one or more containers
>   update      Update configuration of one or more containers
>   version     Show the Docker version information
>   wait        Block until one or more containers stop, then print their exit codes
>
> Run 'docker COMMAND --help' for more information on a command

对镜像进行重命名，此处需要写自己的docker hub的名字

docker tag centos-jdk lyh20093867/centos-jdk

列出本机镜像

docker images

查看配置信息

docker info

搜索镜像 

docker search centos:7.5.1804

删除镜像

使用tag删除镜像

docker rmi hello-world:latest

使用镜像id删除镜像

docker rmi image-id

清理镜像

docker image prune -f

创建centos容器

获取centos:7.5.1804的镜像

docker pull centos:7.5.1804

查看centos的版本

cat /etc/redhat-release

### 镜像高级操作

#### 创建镜像

##### 基于已有容器创建

docker commit -m 'add new file:a.txt' -a 'lyh' exists_container new_image

-m:提交的信息

-a:作者

exists_container:已经有的容器

new_container:新的镜像

##### 基于dockerfile创建

dockerfile是一个文本文件，利用给定的指令，描述基于某个父镜像创建新的镜像的过程

在dockerfile中:

每个指令都会在镜像上创建一个新的层，每一个指令的前缀都必须要大写；

FROM表示从某个镜像开始构建，用于指定镜像源

RUN告诉docker在镜像内执行命令，安装内容

COPY是把文件copy到镜像中，需要注意的是，此处的源文件必须是相对路径，写绝对路径也会转成相对路径

ENV在镜像中设置环境变量

CMD表示从该镜像启动容器时需要运行的默认命令

###### 命令

docker build -t image_name:tagname path

-t指定镜像的名字和标签

path为dockerfile所在的相对路径



#### 保存和加载镜像

保存镜像

docker save -o lyh_centos_java8.tar centos_java8:1.0

加载镜像

sudo docker load -i lyh_centos_java8.tar

#### Docker hub的登陆

此处的需要在后面跟上自己在仓库中配置的地址才行，否则可能登陆不上

docker login hub-mirror.c.163.com

查看镜像的历史情况

docker image history hello-world



## Docker下安装hadoop集群

官网：https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/DockerContainers.html#user-management

准备工作

创建centos-jdk:1.0镜像



### 注意事项

#### 需要让nodemanger和docker镜像之间的用户uid保持一致

docker容器将以应用程序所有者作为容器用户显示启动，如果应用程序所有者不是docker镜像中的有效用户，会失败；

docker镜像需要准备好应用程序的所有配置

对于hadoop、spark等，docker镜像需要饱含jre和hadoop等必要的环境变量：JAVA_HOME、HADOOP_COMMON_PATH、HADOOP_HDFS_HOME、HADOOP_MAPRED_HOME、HADOOP_YARN_HOME 和 HADOOP_CONF_DIR

CGroups配置要求

```shell
systemctl show --no-pager --property=ExecStart docker.service
```

