# Docker学习

## docker的优势

更高效的利用系统资源

更快速的启动时间

一致的运行环境

持续的交付和部署

更轻松的迁移

更轻松的维护和扩展

docker与传统虚拟机比较

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

镜像中有层的概念

##### dockerfile

在dockerfile中:

FROM表示从某个层开始构建；

CMD表示从该镜像启动容器时需要运行的默认命令



### 容器

容器类似一个轻量级的沙箱，docker利用容器来运行和隔离应用；容器可以堪称是一个简易版的linux系统环境；

Ø 镜像自身是只读的。 

Ø 容器从镜像启动的时候，会在镜像的最上层创建一个可写层。

### 仓库

Docker 仓库类似于代码仓库，是 Docker 集中存放镜像文件的场所。



docker安装

#### 验证docker是否可用

sudo docker run hello-world

## 容器基本操作

docker container --help

停止容器

docker container stop container-id

删除一个或者多个容器

docker container rm container-id

也可以使用docker rm container-id删除一个容器

启动一个centos容器，并且在容器中

docker run -d centos:7.5.1804 bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"

## 镜像基本操作

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

删除容器

docker rm container-id

清理镜像

docker image prune -f

创建centos容器

获取centos:7.5.1804的镜像

docker pull centos:7.5.1804

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

进入容器

docker exec -it bcc /bin/bash

导入和导出容器

下面的命令将容器中的xxx.file导出到容器外，存为xxx.tar的归档文件

docker export -o '$PATH/xxx.tar' xxx.file

导入容器

docker import --help

可以将一个文件或者url导入到容器中，会成为一个镜像

![image-20210603164401004](/Users/yihong.li/Library/Application Support/typora-user-images/image-20210603164401004.png)

查看centos的版本

cat /etc/redhat-release

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

