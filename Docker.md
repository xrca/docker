# Docker

## 一、初始Docker

### 1、基本概念（[Docker总览](https://docs.docker.com/get-started/overview/)）

- docker是一个开源的应用容器引擎
- 诞生于2013年初，基于Go语言实现，dotCloud公司出品（后改名为Docker Inc）
- Docker可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到人和流行的Linux机器上。
- 容器是完全使用沙箱机制，相互个例
- 容器性能开销极低
- Docker从17.03版本之后分为CE(Community Edition)和EE(Enterprise Edition)

总结：Docker是一种容器技术，解决软件跨环境迁移问题。

Docker中的三大基本概念：

- **镜像（Image）**
- **容器（Container）**
- **仓库（Repository）**

### 2、Docker可以干什么

- 快速，并一致的交付你的应用

  docker通过让开发者在本地容器提供的标准化环境中工作，容器对于CI/CD（continuous intergration/continuous delivery）提供了很大的帮助。试想一下：开发者在本地开发完后，使用docker容器发布他们的应用，通过docker拉取应用到测试环境，并运行。当发现bug后，开发人员修复后发布新的docker镜像版本，并重新部署到测试环境，当测试完成后，将镜像推送到正式环境。

- 响应式部署和拓展

  docker十分的轻量级，可以运行在个人电脑、物理机、虚拟机、数据中心以及云平台。这使得你可以随时随地的发布更新你的应用。

- 轻量级（在同等硬件条件下运行更多应用）

  能够减少硬件设施的需求。

### 3、Docker Engine

docker engine是一个C/S应用，主要包含以下几个组成部分

- 一个以damon进程运行的server（dockerd命令）
- REST API用于和server进行交互，操作docker
- command line interface（CLI）命令行形式与docker进行交互（docker 命令）

<img src="./image/engine-components-flow.png" style="zoom:100%;" />



### 4、Docker架构

Docker使用了client-server架构，用Docker client连接Docker daemon，来构建、运行并发布你的应用。Docker client和Docker daemon可以运行在同一台电脑上，你也可以连接远程的Docker daemon。Docker client与Docker daemon通过建立在UNIX sockets或网络接口上的REST API进行沟通。

![Docker架构](image/architecture.svg)

**The Docker daemon**

​		Docker daemon（dockerd）监听Docker API的请求，并管理Docker中的对象，如镜像、容器、网络、数据卷等。daemon同时可以与其他daemon进行交互，共同管理Docker服务。

**The Docker client**

​		Docker clinet是大多数用户操作docker的主要方式。当你使用如 docker run 这样的命令时，client会向dockerd发送命令，client端是通过Docker API与daemon进行交互的，一个客户端可以连接多个daemon。

**Docker registries**

​		Docker registries储存docker镜像，Docker Hub是一个公开的镜像仓库，docker默认从Hub中寻找镜像。你也可以构建你自己的私有registries。

​		当使用如 docker pull 或 docker run 命令时，会从你配置的registries中拉取指定的镜像。当你使用 docker push命令时，你的自己构建的镜像会被push到你配置的registry中。

**Docker Object**

​		使用Docker涉及到一下对象，images、containers、networks、volumes、plugins等

- **images**

  镜像是一个只读模板，通常用它来创建一个docker容器。一个镜像往往是基于另一个镜像构建出来的。例如，一个基于ubuntu，并安装了Apache的web服务器，以及你自己的应用和运行时配置，这就构成了一个镜像。

  使用Dockerfile可以构建你自己的镜像，Dockerfile中的每一个指令，都会创建一个layer，当你想要重构镜像时，仅仅会重新构建改变的layers，这也是docker镜像如此快速、轻量的原因。

- **containers**

  容器是一个可以运行的镜像实例，你可以使用Docker API或CLI创建、启动、停止、移动或删除一个容器，一个容器可以连接多个网络，可以增加储存，甚至可以基于它来构建一个新的镜像。

  容器和其他容器或宿主机是相对隔离的，你可以自由的控制隔离程度。

  在移除容器时，容器中未持久化的数据将一同被移除（需要将数据存储到宿主机中）。

### 5、Docker 对比 Virtual Machine

容器是原生的运行在Linux上的，并且和其他容器共享宿主机的内核，它直接一个是独立的进程，不需要其他额外的储存。而虚拟机是一个完整的操作系统，通过hypervisor来获取主机资源。通常虚拟机消耗的资源远超过你的程序消耗的。

<img src="/image/Container.png" style="zoom: 35%;" /><img src="/image/VM.png" style="zoom:35%;" />

### 6、Docker安装

dockerd的官网给出了三种安装方式：

- 通过yum安装（**推荐**）
- 通过下载rpm包手动安装，适合无法连接互联网的环境
- 通过官方的脚本一键安装

此处以第一种yum方式在centos7环境里安装docker社区版：

- 移除旧版本（没有安装过可以忽略此步骤）

  ```shell
  sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
  ```

- 安装yum-utils

  ```shell
  sudo yum install -y yum-utils
  ```

- 添加镜像源（官方镜像）

  ```shell
  sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
  ```

  网络状况不好的，也可以添加阿里云镜像仓库

  ```shell
  sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
  ```

- 安装最新版的docker

  ```shell
  sudo yum install docker-ce docker-ce-cli containerd.io
  ```

- ps：也可以选择安装指定版本的docker。查看docker的所有可用版本，选择合适的版本进行安装

  ```shell
  yum list docker-ce --showduplicates | sort -r
  
  sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
  ```
  
- 启动docker

  ```shell
  sudo systemctl start docker
  ```

- 查看docker服务状态

  ```shell
  sudo systemctl status docker
  ```


### 7、配置Docker镜像加速

国内访问Docker Hub的速度可能很慢，这里可以配置阿里云、网易、中科大等镜像地址。

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["自己的阿里云镜像地址"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 8、Hello World!

运行第一个docker命令

```shell
docker run hello-world
```

![](/image/docker-run-hello-world.png)

docker run后台流程

<img src="/image/docker-run.png" style="zoom:80%;" />



------

## 二、Docker命令

### 1、[Docker官方文档](https://docs.docker.com/)

几乎所有的docker 命令问题的答案，都可以在官方文档中找到。

### 2、Docker服务相关命令

- 启动服务

  ```shell
  systemctl start docker
  ```

- 查看服务状态

  ```shell
  systemctl status docker
  ```

- 停止服务

  ```shell
  systemctl stop docker
  ```

- 重启服务

  ```shell
  systemctl restart docker
  ```

- 开机启动服务

  ```shell
  systemctl enable docker
  ```

### 2、Docker通用命令

- **查看docker版本号**

  ```shell
  docker --version
  docker -v
  ```

- **查看docker信息**

  ```shell
  docker info
  ```

- **docker帮助**

  ```shell
  docker --help
  ```

- **查看具体的docker命令用法**

  ```shell
  docker command --help
  ```

### 3、Docker镜像命令

- **查看本地镜像**

  ```shell
  docker images [OPTIONS] [REPOSITORY[:TAG]]
  
  docker image ls
  ```

  ![](/image/docker-images.png)

  含义：

  ​			REPOSITORY：表示镜像的仓库源

  ​			TAG：镜像的标签

  ​			IMAGE ID：镜像ID

  ​			CREATE：镜像创建时间

  ​			SIZE：镜像大小

  

  OPTIONS：

  - -a, --all	展示所有镜像（默认隐藏中间镜像）

  - --digests  显示digests

  - -q, --quiet 只展示IMAGE ID

  - --no-trunc 显示完整的镜像信息

    

- **搜索镜像**

  ```shell
  docker search [OPTIONS] TERM
  ```

  OPTION：

  - --no-trunc   展示镜像完整信息
  - -s   列出收藏数不小于指定值得镜像
  - --automated   只列出automated build类型的镜像

  

- **下载/拉取镜像**

  ```shell
  docker pull [OPTIONs] NAME[:TAG|@DIGEST]
  ```

  OPTIONS：

  - -a, --all-tags   下载所有标签的镜像

  - --disable-content-trust 跳过镜像验证

  - -q, --quiet   拉取镜像时显示简短信息

    

- **删除镜像**

  ```shell
  docker rmi [OPTIONS] IMAGE [IMAGE...]
  
  docker image rm IMAGE
  ```

  IAMEG可以是IMAGE ID或者NAME:TAG

  OPTIONS：

  - -f, --force   强力删除

  删除所有镜像：

  ```shell
  docker rmi $(docker images -qa)
  docker rmi `docker images -qa`
  ```

- **搜索不同版本的镜像**

  登录[Docker Hub](https://hub.docker.com/)，搜索指定镜像指定版本。

  ![](/image/docker-hub.png)

### 4、Docker容器命令

- **查看容器**

  ```shell
  docker ps [OPTIONS]
  ```

  OPTIONS：

  - -a, --all   列出所有容器（默认只列出正在运行的容器）
  - -l   显示最近创建的容器
  - -n   显示最近n个创建的容器
  - -q   静默模式，只显示容器编号
  - --no-trunc   不截断输出

- **创建容器**

  ```shell
  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
  ```

  OPTIONS：

  - --name   给容器命名（不可重复）

  - -d, --detach   在后台运行容器，并打印出容器id

  - -i    以交互模式运行容器，通常与-t同时使用

  - -t   为容器重新分配一个伪终端，通常与-i同时使用

  - -P   随机端口映射

  - -p   指定端口映射，有如下格式

    ​		ip:hostPort:containerPort

    ​		ip::containerPort

    ​		**hostPort:containerPort**

    ​		containerPort

  日常使用：创建守一个名为container_name的守护式容器，将hostPort与containerPort进行映射

  ```shell
  docker run -id --name=container_name -p hostPort:containerPort IMAGE:TAG
  ```

  

- **启动容器**

  ```shell
  docker start [OPTIONS] CONTAINER [CONTAINER...]
  ```

  CONTAINER：容器Id或容器名

  

  OPTIONS：

  - -a, --attach   xxx
  - -i, --interactive  xxxx

  

- **进入容器**

  退出容器时，使用exit会使容器停止

  ```shell
  docker attach CONTAINER
  ```

  交互式方式进行容器并分配伪终端，同时运行/bin/bash命令。退出容器时，无论使用什么命令都不会使容器停止。

  ```shell
  docker -it exec CONTAINER /bin/bash
  ```

- **退出容器**

  ```shell
  exit
  ```

  ```shell
  ctrl + P + Q
  ```

- **停止容器**

  ```shell
  docker stop CONTAINER
  ```

  

- **删除容器**

  ```shell
  docker rm [OPTIONS] CONTAINER [CONTAINER...]
  ```

  OPTIONS：

  - -f, --force   强力删除，容器正在运行会SIGKILL后删除
  - v, --volumes   删除容器时，同时删除与其关联的数据卷

<img src="/image/docker-rm-v.png" style="zoom: 80%;" />

- **查看容器信息**

  ```shell
  docker inspect CONTAINER
  ```

- **向容器发送指令**

  ```shell
  docker exec CONTAINER bash -c "YOUR COMMAND"
  ```

- **复制文件**

  ```shell
  docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH
  
  docker cp [OPTIONS] SRC_PATH CONTAINER:DEST_PATH
  ```

  





------

## 三、Docker容器数据卷

### 1、数据卷概念和作用

#### 1）、概念

- 数据卷是宿主机中的一个目录或文件
- 当容器目录和数据卷目录绑定后，对方的修改会立即同步（容器目录和宿主机中的数据卷是双向同步的）
- 一个数据卷可以被多个容器挂载，同时一个容器也可以挂载多个数据卷

#### 2）、作用

- 容器删除后，容器中的数据也会随之被删除，数据卷可以持久化容器中的数据，让容器成为一个无状态服务
- 让容器和外部机器进行文件交换（外部机器通过操作宿主机的数据卷来间接和容器进行文件交换）
- 容器与容器之间进行数据交换

### 2、配置数据卷

- **查看本地数据卷**

  ```shell
  docker volume ls
  ```

- **查看数据卷详情**

  ```shell
  docker volume inspect VOLUME
  ```

- **创建数据卷**

  创建容器时直接挂载数据卷

  ```shell
  docker run ... -v hostDir:containerDir ...
  ```

  先创建容器卷，在挂载到容器上

  ```shell
  docker volume create VOLUME
  
  docker run -id -v VOLUME:containerDir ...
  ```

- **删除数据卷**

  删除数据卷的前提是该数据卷未与任何容器进行绑定

  ```shell
  docker volume rm VOLUME
  ```

  

### 3、配置数据卷容器

创建数据卷容器：

```shell
docker run -id --name=c3 -v /home/volume centos:7
```

创建c1、c2容器，以c3为数据卷容器

```shell
docker run -id --name=c2 --volumes-from c3 centos:7

docker run -id --name=c1 --volumes-from c3 centos:7
```

<img src="/image/docker-run-volumes-from.png" style="zoom:80%;" />



------

## 四、Dockerfile

------

### 0、操作系统组成部分

- 进程调度子系统
- 进程通信子系统
- 内存管理子系统
- 设备管理子系统
- **文件管理子系统**
- 网络通信子系统
- 作业控制子系统

Linux文件系统由bootfs和rootfs两部分组成：

- bootfs：包含BootLoader（引导加载程序）和kernel（内核）
- rootfs：root文件系统，包含的就是典型Linux系统中的/dev、/proc、/bin、/etc等标准目录和文件
- 不同的linux发行版，bootfs基本一样，而rootfs不同，如ubuntu、centos等

### 1、Docker镜像原理

![](/image/docker-image.png)



### 2、制作镜像（通过容器）

- **将容器转化为镜像**

  ```shell
  docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
  ```

  OPTIONS：

  - -a, --author   作者信息
  - -m, --message   提交信息

  

  注：通过目录挂载的数据不会被保存到镜像中。



- **将镜像打成压缩包**

  ```shell
  docker save [OPTIONS] IMAGE [IMAGE...]
  ```

  OPTIONS：

  - -o, --output

  

- **从压缩文件中加载镜像**

  ```shell
  docker load [OPTIONS]
  ```

  OPTIONS：

  - -i, --input   读取指定压缩文件
  - -q, --quiet  静默模式 



### 3、制作镜像（通过Dockerfile）

- **构建镜像**

  ```shell
  docker build [OPTIONS] PATH | URL | -
  ```

  OPTIONS：

  - -f, --file   指定dockerfile文件
  - -t, --tag   命名标签，格式："name:tag"

  示例：以centos7为基础，创建一个新的镜像，包含vim功能，工作路径为/usr，作者信息为 saury <fishsaury@163.com>

  

  创建命令：

  ```shell
  docker build -f ./dockerfile -t NAME:TAG .
  ```

  dockerfile：

  ```shell
  FROM centos:7
  MAINTAINER saury <fishsaury@163.com>
  RUN yum install -y vim
  WORKDIR /usr
  CMD /bin/bash
  ```

  

## 五、Docker Compose

------





------

## 六、Docker常用应用部署

### 1、Nginx

### 2、Tomcat

### 3、MySql

### 4、Oracle

### 5、Redis

### 6、Mongo

### 7、Zookeeper

### 8、ElasticSearch

### 9、Kibana





------

## 七、Docker私有仓库

------



## 八、Docker与Spring Boot整合

### 1、将手动打包好的jar用dockerfile构建成镜像



### 2、与IDEA的Docker插件结合，实现一键发布更新



------



## 附录

**Docker命令示意图**

![Docker命令示意图](/image/Docker-Command-Diagram.png)



**学习资料：**

https://yeasy.gitbook.io/docker_practice/

docker attach 与 docker exec的区别：

https://stackoverflow.com/questions/30960686/difference-between-docker-attach-and-docker-exec

https://cloud.tencent.com/developer/article/1657953



docker常见小问题：

https://stackoverflow.com/questions/37929190/docker-exec-printf-gives-no-such-file-or-directory-error