# 项目2 Docker镜像、容器与仓库管理
## 任务1 镜像的使用与操作
### 任务引入
### 任务分析
### 知识讲解
### 2.1.1 镜像的基本概念
![[1729314938417-978bd87b-3f8d-42f1-b332-4f498a808246.png]]

#### 1.什么是镜像
Docker镜像是一个轻量、可执行的独立软件包，包含运行特定软件所需的所有代码、库、依赖项和配置。它是Docker容器的基础。

在IT领域，镜像（Image）通常指的是一系列文件或一个磁盘驱动器的精确副本。例如，Ghost软件使用镜像文件来存储一个分区或整个硬盘的所有信息。在云计算环境中，镜像被用作虚拟机模板，预装了基本的操作系统和其他软件。创建虚拟机时，首先需要准备一个镜像，然后可以启动一个或多个该镜像的实例。

与虚拟机镜像类似，Docker镜像作为创建容器的只读模板，包含文件系统，且比虚拟机镜像更轻量。Docker镜像按照Docker的要求定制应用程序，类似于软件安装包。一个Docker镜像可以包含一个应用程序及其运行所需的基本操作系统环境。例如，一个Ubuntu镜像可能包含Ubuntu操作系统，而一个Web应用程序的镜像则可能包含完整的操作系统（如Ubuntu）、Apache HTTP服务器及用户开发的Web应用。

操作系统分为内核空间和用户空间。在Linux系统中，内核启动后会挂载root文件系统，为用户空间提供支持，Docker镜像则相当于一个root文件系统。Docker镜像是一种特殊的文件系统，除了提供容器运行时所需的程序、库和资源外，还包含运行所需的配置参数。镜像不包含任何动态数据，其内容在创建容器后不会改变。

镜像是创建容器的基础，Docker通过版本管理和联合文件系统提供了简单的机制来创建和更新镜像。当容器运行时，如果所需镜像在本地计算机中不存在，Docker会自动从Docker镜像仓库下载镜像，默认情况下是从Docker Hub公共镜像库下载。

### 2.1.2 搜索与下载镜像的方法
#### 1.搜索镜像
在命令行环境下使用docker search <image_name> 即可搜索Docker Hub中的对应镜像，而无需使用浏览器搜索。如果要启动一个nginx镜像来提供web服务，则可以先搜索和nginx相关的镜像，如下图所示。

![[1729400889404-84cbcaae-cfe3-4292-8394-130e56e63b7e.png]]

（1）其中NAME列显示镜像仓库（源）名称

（2）DESCRIPTION显示了镜像发布者在仓库中对镜像的描述

（3）STARS表示在镜像仓库中有多少人点亮收藏了该镜像

（4）OFFICIAL列指明是否为Docker官方发布

建议没有特性需求的情况下，使用官方发布的镜像，提升镜像来源安全性。

下图是Docker Hub官方搜索的结果，可与上图第一条对应参考

![[1729401748713-b5413c2b-804f-4c6d-ad53-9b6a31d86fb3.png]]

#### 2.下载镜像
在本地运行容器时，若使用一个不存在的镜像，则Docker会自动下载这个镜像。如果需要预先下载这个镜像，则可以使用docker pull命令来拉取它，即将它从镜像仓库（默认为Docker Hub上公开的仓库）下载到本地，完成之后可以直接使用这个镜像来运行容器。例如，拉取一个 nginx:latest 版本的镜像，命令执行结果如下。

```bash
下载镜像：使用命令 docker pull  从Docker Hub下载镜像。
docker pull 镜像仓库拉取镜像:版本号（不写默认最新版=latest）

[root@docker1 ~]# docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
a480a496ba95: Pull complete
f3ace1b8ce45: Pull complete
11d6fdd0e8a7: Pull complete
f1091da6fd5c: Pull complete
40eea07b53d8: Pull complete
6476794e50f4: Pull complete
70850b3ec6b2: Pull complete
Digest: sha256:28402db69fec7c17e179ea87882667f1e054391138f77ffaf0c3eb388efc3ffb
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```

拉取指定版本的镜像

默认情况下使用 docker pull 不指定镜像的版本时，会默认拉取仓库中最新的镜像，如果需要拉取指定般的的镜像就需要手动指定镜像的版本，列如拉取ubuntu:22.04版本的镜像，如下命令所示：

```bash
[root@docker1 ~]# docker pull ubuntu:22.04
22.04: Pulling from library/ubuntu
6414378b6477: Pull complete
Digest: sha256:0e5e4a57c2499249aafc3b40fcd541e9a456aab7296681a3994d631587203f97
Status: Downloaded newer image for ubuntu:22.04
docker.io/library/ubuntu:22.04

```

### 2.1.3 查看本地镜像列表的命令
#### 1.查看本地镜像列表
使用 docker images 命令来列出本地主机上所有的镜像，执行结果如下：

```bash
[root@docker1 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
nginx         latest    3b25b682ea82   2 weeks ago     192MB
ubuntu        22.04     97271d29cb79   5 weeks ago     77.9MB
hello-world   latest    d2c94e258dcb   17 months ago   13.3kB

```

输出的列表展示了镜像的基本信息。

（1）REPOSITORY 列显示镜像所在的仓库

（2）TAG 列表示镜像的标签

（3）IMAGE ID  列显示镜像的唯一标识符

（4）CREATED  列记录镜像的创建时间

（5）SIZE  列显示镜像的大小

IMAGE ID 是镜像的唯一标识符，以UUID格式表示，长度为64个十六进制字符。如果需要查看完整的 IMAGE ID，可以使用 --no-trunc 选项，如下所示：

```bash
[root@docker1 ~]# docker images nginx --no-trunc
REPOSITORY   TAG       IMAGE ID                                                                  CREATED       SIZE
nginx        latest    sha256:3b25b682ea82b2db3cc4fd48db818be788ee3f902ac7378090cf2624ec2442df   2 weeks ago   192MB
```

这将显示完整的 SHA256 哈希值。引用镜像时，无需添加 "sha256" 字段，只需直接引用冒号后的 ID 号即可。当本地镜像数量较少时，可以直接使用前几位来表示相应的镜像。拿当前镜像举例，如： "3b25b6"

### 2.1.4 删除本地镜像的操作

```bash
[root@docker1 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
nginx         latest    3b25b682ea82   2 weeks ago     192MB
hello-world   latest    d2c94e258dcb   17 months ago   13.3kB
[root@docker1 ~]# docker rmi d2c94e258dcb
Untagged: hello-world:latest
Untagged: hello-world@sha256:d211f485f2dd1dee407a80973c8f129f00d54604d2c90732e8e320e5038a0348
Deleted: sha256:d2c94e258dcb3c5ac2798d32e1249e42ef01cba4841c2234249495f87264ac5a
Deleted: sha256:ac28800ec8bb38d5c35b49d45a6ac4777544941199075dff8c4eb63e093aa81e
```

该命令用于删除本地指定的Docker镜像。通过镜像的ID（d2c94e258dcb）进行删除。删除成功后，会显示相应的提示，包括已解除标签的镜像和已删除的镜像ID。

### 2.1.5 导入导出镜像的流程
1.导出镜像

```bash
[root@docker1 ~]# docker save 3b25b682ea82 -o  sava_nginx_v1.tar
[root@docker1 ~]# ls
sava_nginx_v1.tar.gz
[root@docker1 ~]# docker save nginx:latest -o  sava_nginx_v2.tar
[root@docker1 ~]# ls
sava_nginx_v1.tar  sava_nginx_v2.tar
```

将指定的Docker镜像导出到一个tar文件中，以便于后续的导入或备份。这里的 3b25b682ea82 是镜像的ID。 创建的tar文件（sava_nginx_v1.tar）可以在本地文件系统中查看

2.导入镜像

使用 docker load 从指定的tar文件中导入Docker镜像。-i选项指定要导入的文件。

```bash
#先将 Nginx 镜像删除
[root@docker1 ~]# docker rmi 3b25b682ea82
Untagged: nginx:latest
Untagged: nginx@sha256:28402db69fec7c17e179ea87882667f1e054391138f77ffaf0c3eb388efc3ffb
Deleted: sha256:3b25b682ea82b2db3cc4fd48db818be788ee3f902ac7378090cf2624ec2442df
Deleted: sha256:3e8a4396bcdb62aeb916ec1e4cf64500038080839f049c498c256742dd842334
Deleted: sha256:8dd6a711fbdd252eba01f96630aa132c4b4e96961f09010fbbdb11869865f994
Deleted: sha256:9368c52198f80c9fb87fc3eaf7770afb7abb3bfd4120a8defd8a8f1a68ff375d
Deleted: sha256:46834c975bf2d807053675d76098806736ee94604c650aac5fe8b5172ab008c8
Deleted: sha256:6e433330e8b1553bee0637fac3b1e66c994bb2c0cab7b2372d2584171d1c93d8
Deleted: sha256:fbc611fa4a4aff4cf0bfd963c49e2c416ff8047c9f84c2dc9328d3b833f1118d
Deleted: sha256:98b5f35ea9d3eca6ed1881b5fe5d1e02024e1450822879e4c13bb48c9386d0ad

#导入镜像
[root@docker1 ~]# docker load -i sava_nginx_v1.tar > nginx:latest
[root@docker1 ~]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
nginx        latest    3b25b682ea82   2 weeks ago   192MB
```

### 任务实施：Docker镜像操作实战
### 知识拓展
## 任务2 容器的使用与操作
### 任务引入
### 任务分析
### 知识讲解
### 2.2.1 容器基本概念
容器的英文名称为Container，在Docker中指从镜像创建的应用程序运行实例。镜像和容器的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体，基于同一镜像可以创建若干不同的容器。Docker的设计借鉴了集装箱的概念，每个容器都有一个软件镜像，相当于集装箱中的货物。可以将容器看作将一个应用程序及其依赖环境打包而成的集装箱。容器可以被创建、启动、停止、删除、暂停等。与集装箱一样，Docker在执行这些操作时并不关心容器里有什么软件。容器的实质是进程，但与直接在主机上执行的进程不同，容器进程在属于自己的独立的命名空间内运行。因此容器可以拥有自己的根文件系统、自己的网络配置、自己的进程空间，甚至自己的用户ID空间。容器内的进程运行在一个隔离的环境里，使用起来就好像是在一个独立于主机的系统下操作一样。通常容器之间是彼此隔离、互不可见的。这种特性使得容器封装的应用程序比直接在主机上运行的应用程序更加安全，但这种特性可能会导致一些初学者混淆容器和虚拟机，这个问题应引起重视。

#### 1.查看容器的信息
在容器创建完成后可以查看其对应的基本信息，使用 docker ps -a 可以输出本地主机上的全部容器列表，命令执行结果如下：

```bash
[root@docker1 ~]# docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED              STATUS                      PORTS     NAMES
fc54b851c52e   nginx         "/docker-entrypoint.…"   20 seconds ago       Exited (0) 3 seconds ago              blissful_clarke
d78422feae17   hello-world   "/hello"                 39 seconds ago       Exited (0) 38 seconds ago             clever_elion
0c6126ca0c70   nginx         "/docker-entrypoint.…"   About a minute ago   Up 59 seconds               80/tcp    festive_meninsky
d19a3e016c91   nginx         "/docker-entrypoint.…"   8 minutes ago        Created                               eloquent_aryabhata
```

上面列表中反映了容器的基本信息。CONTAINER ID 列表示容器 ID，IMAGE 列表示容器所用镜像的名称，COMMAND列表示启动容器时执行的命令，CREATED列表示容器的创建时间，STATUS 列表示容器运行的状态（Up表示运行中，Exited表示已停止，Created表示创建完成但还未启动），PORTS列表示容器对外发布的端口号，NAMES列表示容器名称。

### 2.2.2 容器的创建与启动命令
#### 1.创建时启动容器
在Docker中，启动一个容器最常用的方式是通过 docker run 命令。这个命令不仅用于创建新的容器，还会自动启动它。基本语法如下：

```bash
docker run -it --name (name是为容器指定的新名字)(it一般搭配使用，意为启动命令行界面)
docker start 镜像id或镜像name  开启

#前台模式启动
[root@docker1 ~]# docker run -it nginx
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2024/10/20 10:22:25 [notice] 1#1: using the "epoll" event method
2024/10/20 10:22:25 [notice] 1#1: nginx/1.27.2
2024/10/20 10:22:25 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14)
2024/10/20 10:22:25 [notice] 1#1: OS: Linux 3.10.0-862.el7.x86_64
2024/10/20 10:22:25 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 65536:65536
2024/10/20 10:22:25 [notice] 1#1: start worker processes
2024/10/20 10:22:25 [notice] 1#1: start worker process 28
2024/10/20 10:22:25 [notice] 1#1: start worker process 29
2024/10/20 10:22:25 [notice] 1#1: start worker process 30
2024/10/20 10:22:25 [notice] 1#1: start worker process 31

#后台模式启动
[root@docker1 ~]# docker run -id nginx
3dc17afe62e2852096ee640b16a0fb6da9140e8addc23e164155724d3cb87240
[root@docker1 ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS        PORTS     NAMES
3dc17afe62e2   nginx     "/docker-entrypoint.…"   2 seconds ago   Up 1 second   80/tcp    silly_gagarin

```

#### 2.创建容器
```bash
[root@docker1 ~]# docker create -it  nginx
06951deba5bd9f7f66bbb8fe627f1ff4919eca9f1917362ac94b0984bb501816
```

创建容器之后对容器进行的各种操作，如启动、停止、修改或删除等，都可以通过容器 ID 来进行引用。容器的唯一标识容器 ID 与镜像 ID 一样采用 UUID 形式表示，它是由64个十六进制字符组成的字符串。可以在 docker ps 命令中加上 --no-trunc 选项显示完整的容器 ID ，但通常采用前12个字符的缩略形式，这在同一主机上就足以区分各个容器了。容器数量少的时候，还可以使用更短的格式，只取前面几个字符即可。

容器ID能保证唯一性，但难于记忆，因此可以通过容器名称来代替容器ID引用容器。容器名称默认由 Docker 自动生成，也可在执行 docker run 命令时通过 --name 选项自行指定。还可以使用 docker rename 命令为现有的容器重新命名，以便于后续的容器操作。例如，使用以下命令更改容器名称。

```bash
docker rename festive_meninsky webserver
```

#### 3.启动容器
```bash

```

### 2.2.3 进入与退出容器的方法
```bash
docker attach  容器id  进入容器，exit退出会正常关闭容器
docker exec -it 容器id /bin/bash  进入到后台运行的容器  （并且该命令使用exit不会关闭容器）
```

### 2.2.4 容器的停止与删除操作
```bash
docker stop 容器id或容器name  停止
docker rm 容器id或容器name  删除停止的容器
docker rm --force 容器id或容器name 强制删除正在运行中的容器
```

### 2.2.5 查看容器日志的命令
```bash
docker logs 容器id或容器name
docker container logs  容器id或容器name
```

### 任务实施：Docker容器操作实战演练
### 知识拓展
## 任务3 注册中心的使用与操作
### 任务引入
### 任务分析
### 知识讲解
### 2.3.1 注册中心、仓库、Hub的概念

## 私有库的应用场景
像Dockerhub、阿里云这样的公共镜像仓库有的时候用起来不太方便：Dockerhub；阿里云需要花钱买云主机。

另外，涉及内部资源的保密性，有的机构不太可能将镜像提供给公网，因此需要创建一个基于内部项目镜像，构造本地私人仓库供给团队使用。

因此，Docker提供了Dcoker Registry工具，可以用于构建私有镜像仓库。

### 2.3.2 第三方注册中心的选择与使用
```bash

```

### 2.3.3 私有仓库的搭建与使用流程
为了实现本地镜像发布到私有仓库Registry，准备工作主要包括：

（1）首先，安装Reistry和验证安装成功；

（2）然后，创建一个本地的新镜像，用于发布到私有仓库Registry。

#### 1.registry私有仓库搭建
```bash
#拉取registry镜像
[root@docker1 ~]# docker pull registry:2.8.3
2.8.3: Pulling from library/registry
1cc3d825d8b2: Pull complete
85ab09421e5a: Pull complete
40960af72c1c: Pull complete
e7bb1dbb377e: Pull complete
a538cc9b1ae3: Pull complete
Digest: sha256:ac0192b549007e22998eb74e8d8488dcfe70f1489520c3b144a6047ac5efbe90
Status: Downloaded newer image for registry:2.8.3
docker.io/library/registry:2.8.3

#运行registry容器
[root@docker1 ~]# docker run -d -p 5000:5000 -v /data/registry:/var/lib/registry --restart=always --name registry registry:2.8.3
79b7b6a69629092d98dd844cde38ffabc37bf3d3ec97d12e689fa115ee076439
```

默认情况下，仓库创建在容器的/var/lib/registry目录下，也就是说每次我们上传到registry的私人服务器仓库的文件就存储到/var/lib/registry目录下。考虑到权限管理问题，在实际应用中通常需要自定义容器卷映射，以便与宿主机联调

#### 2.推送镜像文件到Registry
修改daemon.json文件，添加 "insecure-registries": ["0.0.0.0/0"]  配置

```bash
[root@docker1 ~]# cat /etc/docker/daemon.json
{
  "insecure-registries": ["0.0.0.0/0"],
  "registry-mirrors": ["https://docker.1panelproxy.com","https://proxy.1panel.live","https://docker.1panelproxy.com"]
}

```

推送镜像

```bash
#将镜像修改为符合私服规范的Tag
docker tag registry:2.8.3  192.168.100.100:5000/registry:2.8.3
```

### 任务实施：搭建并使用私有仓库
### 知识拓展
