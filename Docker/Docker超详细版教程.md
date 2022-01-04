

Docker(整理自狂神的笔记)

**详情视频去bilibili搜狂神，找Docker课程**

------《三体》中的一句话：

------==弱小和无知不是生存的障碍，傲慢才是。==

# Docker超详细版教程通俗易懂



>1、2、3、4 入门
>
>5、6、7 精髓
>
>9、10、11 企业实战



## 1、Docker概述

###通俗理解：

先简单理解 docker 的使用过程，它分为镜像构建与容器启动。

镜像构建：即创建一个镜像，它包含安装运行所需的环境、程序代码等。这个创建过程就是使用 dockerfile 来完成的。

容器启动：容器最终运行起来是通过拉取构建好的镜像，通过一系列运行指令（如端口映射、外部数据挂载、环境变量等）来启动服务的。针对单个容器，这可以通过 docker run 来运行。

而如果涉及多个容器的运行（如服务编排）就可以通过 docker-compose 来实现，它可以轻松的将多个容器作为 service 来运行（当然也可仅运行其中的某个），并且提供了 scale (服务扩容) 的功能。

**简单总结：**

1.dockerfile: 构建镜像；

2.docker run: 启动容器；

3.docker-compose: 启动服务；

从头说起。
假如你不用 docker ，搭建 wordpress 怎么弄？先找台 server ，假设其 OS 为 Ubuntu ，然后按照文档一步步敲命令，写配置，对吧？
用 docker 呢？ 随便找台 server ，不管什么操作系统，只要支持 docker 就行， docker run ubuntu, docker 会从官方源里拉取最新的 Ubuntu 镜像，可以认为你开了个 Ubuntu 虚拟机，然后一步步安装，跟上面一样。

但是这样安装有个显著的缺点，一旦 container 被删，你做的工作就都没了。当然可以用 docker commit 来保存成镜像，这样就可以复用了。

但是镜像一般比较大，而且只分享镜像的话，别人也不知道你这镜像到底包含什么，这些问题都不利于分享和复用。
一个直观的解决方案就是，写个脚本把安装过程全部记录下来，这样再次安装的时候，执行脚本就行了。 Dockerfile 就是这样的脚本，它记录了一个镜像的制作过程。
有了 Dockerfile, 只要执行 docker build . 就能制作镜像，而且 Dockerfile 就是文本文件，修改也很方便。

现在有了 wordpress 的镜像，只需要 docker run 就把 wordpress 启动起来了。

如果仅仅是 wordpress, 这也就够了。但是很多时候，需要多个镜像合作才能启动一个服务，比如前端要有 nginx ， 数据库 mysql, 邮件服务等等，当然你可以把所有这些都弄到一个镜像里去，但这样做就无法复用了。
更常见的是, nginx, mysql, smtp 都分别是个镜像，然后这些镜像合作，共同服务一个项目。
docker-compose 就是解决这个问题的。你的项目需要哪些镜像，每个镜像怎么配置，要挂载哪些 volume, 等等信息都包含在 docker-compose.yml 里。
要启动服务，只需要 docker-compose up 就行，停止也只需要 docker-compse stop/down

简而言之， Dockerfile 记录单个镜像的构建过程， docker-compse.yml 记录一个项目(project, 一般是多个镜像)的构建过程。

你说有些教程用了 dockerfile+docker-compose, 是因为 docker-compose.yml 本身没有镜像构建的信息，如果镜像是从 docker registry 拉取下来的，那么 Dockerfile 就不需要；如果镜像是需要 build 的，那就需要提供 Dockerfile.



docker-compose是编排容器的。例如，你有一个php镜像，一个mysql镜像，一个nginx镜像。如果没有docker-compose，那么每次启动的时候，你需要敲各个容器的启动参数，环境变量，容器命名，指定不同容器的链接参数等等一系列的操作，相当繁琐。而用了docker-composer之后，你就可以把这些命令一次性写在docker-composer.yml文件中，以后每次启动这一整个环境（含3个容器）的时候，你只要敲一个docker-composer up命令就ok了。

dockerfile的作用是从无到有的构建镜像。它包含安装运行所需的环境、程序代码等。这个创建过程就是使用 dockerfile 来完成的。Dockerfile - 为 docker build 命令准备的，用于建立一个独立的 image ，在 docker-compose 里也可以用来实时 build
docker-compose.yml - 为 docker-compose 准备的脚本，可以同时管理多个 container ，包括他们之间的关系、用官方 image 还是自己 build 、各种网络端口定义、储存空间定义等
————————————————
原文链接：https://blog.csdn.net/ddffr/article/details/77049118



### docker为什么会出现？

一款产品：开发--上线 两套环境！应用环境，应用配置！

开发 ----运维。问题：我在我的电脑上可以运行！版本更新，导致服务不可用！对于运维来说，考验就十分大？

环境配置是十分的麻烦，每一个机器都要部署环境（集群Redis、ES、Hadoop...)费时费力。

发布一个项目（jar+(Redis MySQL jdk ES)）,项目能不能都带上环境安装打包！

之前在服务器配置一个应用的环境Redis MySQL jdk ES Hadoop，配置超麻烦，不能够跨平台。

Windows,最后发布到Linux!

传统：开发jar,运维来做！

现在：开发打包部署上线，一套流程做完！



java -- apk -- 发布（应用商店） --张三使用apk ---安装即可用！

java -- jar(环境) ----打包项目带上环境（镜像） ---（Docker仓库：商店） ---下载我们发布的镜像 --- 直接运行即可!



Docker 给以上的问题，提供了解决方案！

![image-20200708192428765](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200708192428765.png)

Docker的思想就来自于集装箱！

JRE -- 多个应用（端口冲突）--- 原来都是交叉的！

隔离：Docker核心思想！打包装箱！每个箱子是互相隔离的。 通过隔离机制，可以将服务器利用到极致！

###Docker历史

2010年，几个搞IT的年轻人，就在美国成立了一家公司`dotCloud`

做一些pass的云计算服务！LXC 有关的容器技术！

他们将自己的技术（容器化技术）命名就是Docker！

Docker刚刚诞生的时候，没有引起行业的注意！dotCloud,就活不下去了！

`开源`

开放源代码！

2013年，Docker开源！

Docker越来越多的人发现了docker的优点！火了，Docker每个月都会更新一个版本！

2014年4月9日，Docker1.0发布！

Docker为什么这么火？十分的轻巧！

在容器技术出来之前，我们都是使用虚拟机技术！

虚拟机：在windows中装一个VMware,通过这个软件我们可以虚拟出来一台或多台电脑！笨重！

虚拟机也是属于虚拟化技术，Docker容器技术，也是一种虚拟化技术！

```shell
vm：linux centos原生镜像(一个电脑!) 隔离，需要开启多个虚拟机  n个G 几分钟
docker: 隔离  镜像(最核心的环境 4m + jdk + mysql) 十分的小巧， 运行镜像就可以了！ 小巧！几个M KB  秒级启动！
```

官网地址：https://www.docker.com/

![image-20200708200404285](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200708200404285.png)

文档地址：https://docs.docker.com/  Docker的文档是超级详细的！

仓库地址：https://hub.docker.com/  里面是可以查询各种镜像

### Docker能干嘛

>之前的虚拟机技术！

![image-20200708202121745](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200708202121745.png)

虚拟机技术缺点：

1、资源占用十分多

2、冗余步骤多

3、启动很慢！



> 容器化技术！
>
> ==容器化技术不是模拟的一个完整的操作系统==

![image-20200708230404734](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200708230404734.png)

比较Docker和虚拟机技术的不同：

- 传统虚拟机，虚拟出一套硬件，运行一个完整的操作系统，然后在这个系统上安装运行软件
- 容器内的应用直接运行在宿主机的内容，容器是没有自己的内核的，也没有虚拟我们的硬件，所以就轻便了
- 每个容器间是互相隔离，每个容器内都有一个属于自己的文件系统，互不影响。



> （开发  运维）

**应用更快速的交付和部署**

传统：一堆帮助文档，安装程序

Docker：打包镜像发布测试，一键运行

**更便捷的升级和扩缩容**

使用了Docker之后，我们部署应用就和搭积木一样！

项目打包为一个镜像，扩展 服务器A !  服务器B ! 

**更简单的系统运维**

在容器化之后，我们的开发，测试环境都是高度一致的。

**更高效的计算资源利用：**

Docker是内核级别的虚拟化，可以在一个物理机上运行很多的容器实例！

##2、Docker 安装

###Docker的基本组成

![timg](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/timg.jpg)

**镜像（image）：**

docker镜像就好比是一个模板，可以通过这个模板来创建容器服务，tomcat镜像==> run ==> tomcat01容器（提供服务器），通过这个镜像可以创建多个容器（最终服务运行或者项目运行就是在容器中的）。

**容器（container）：**

Docker利用容器技术，独立运行一个或者一组应用，通过镜像来创建的。

启动，停止，删除，基本命令！

目前就可以把这个容器理解为就是一个简易的linux系统

**仓库（repository）：**

仓库就是存放镜像的地方！

仓库分为公有仓库和私有仓库！

Docker Hub （默认是国外的比较慢）

阿里云...都用容器服务器（配置镜像加速！）



### 安装Docker

> 环境准备

1、需要会一点点的Linux的基础

2、Centos7

3、使用Xshell等远程工具来连接远程服务器进行操作！

> 环境查看

```shell
# 系统内核需是 3.10以上的
[root@localhost ~]# uname -r
3.10.0-1127.el7.x86_64
```

 ```shell
# 系统版本
[root@localhost ~]# cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
 ```

> 安装

帮助文档：https://docs.docker.com/

Download and install --> Docker for Linux --> centos ：

```shell
# 1.卸载旧的版本
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
# 2.需要的安装包
yum install -y yum-utils

# 3.设置镜像仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
# 这个是默认国外的地址，不要使用，很慢!

# 推荐更改比如下面的阿里云地址：↓
yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 先更新软件包索引
[root@localhost ~]# yum makecache fast

# 4.安装docker相关的  docker-ce 社区版  ee 企业版
yum install docker-ce docker-ce-cli containerd.io

# 5.启动docker
systemctl start docker
# 停止docker： systemctl stop docker
# 重启docker： systemctl  docker
# 查看docker状态： systemctl status docker
# 开机自动启动docker： systemctl enable docker

# 6.使用docke version 查看是否安装成功
```

![image-20200709003806873](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709003806873.png)

```shell
# 7.hello-world
docker run hello-world

# 8.查看一下下载的这个hello-world 镜像
[root@localhost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        6 months ago        13.3kB
```

了解：卸载docker

```shell
# 1.卸载依赖
yum remove docker-ce docker-ce-cli containerd.io
# 2.删除资源
rm -rf /var/lib/docker

# /var/lib/docker   docker的默认工作路径！
```



###阿里云镜像加速

1、登录阿里云找到容器服务

![image-20200709005153137](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709005153137.png)

2、找到镜像加速地址

![image-20200709005349309](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709005349309.png)

3、配置使用

```shell
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://nak9eedd.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload

sudo systemctl docker
```





**回顾hello-world流程**

![image-20200709011303326](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709011303326.png)

![image-20200709011056520](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709011056520.png)

###底层原理

**Docker是怎么工作的？**

Docker 是一个Client - Server结构的系统，Docker的守护进程运行在主机上，通过Socket从客户端访问！

DockerServer接收到Docker-Client的指令，就会执行这个命令！

![image-20200709012320291](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709012320291.png)

**Docker为什么VM快？**

1、Docker有着比虚拟机更少的抽象层。

2、Docker利用的是宿主机的内核，vm需要是Guest OS。

![image-20200709012705845](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709012705845.png)

所以说，新建一个容器的时候，docker不需要像虚拟机一样重新加载一个操作系统内核，避免引导，虚拟机是加载Guest OS，分钟级别的，而docker是利用宿主机的操作系统，省略了这个复杂的过程，秒级！



##3、Docker的常用命令

### 帮助命令

```shell
docker version      # 显示docker的版本信息
docker info         # 显示docker的系统信息，包括镜像容器的数量
docker 命令   --help # 帮助命令
```

帮助文档的地址：https://docs.docker.com/engine/reference/commandline/



### 镜像命令

**docker images 查看所有本地的主机上的镜像**

```shell 
[root@localhost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        6 months ago        13.3kB

# 解释
REPOSITORY  镜像的仓库源
TAG         镜像的标签
IMAGE ID    镜像的id
CREATED     镜像的创建时间
SIZE　　　　　镜像的大小

# 可选项
  -a, --all       # 列出所有的镜像
  -q, --quiet     # 只显示镜像的id
```

**docker search搜索镜像**

```shell
[root@localhost ~]# docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   9710                [OK]                
mariadb                           MariaDB is a community-developed fork of MyS…   3537                [OK]   
.
.
.
.

# 可选项，通过收藏来过滤
  --filter=STARS=3000   # 搜索出的镜像就是STARS大于3000的
[root@localhost ~]# docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   9710                [OK]                
mariadb                           MariaDB is a community-developed fork of MyS…   3537                [OK]   
```

**docker pull**  下载镜像

```shell
# 下载镜像 docker pull 镜像名[:tag]
[root@localhost ~]# docker pull mysql  
Using default tag: latest  # 如果不写tag,默认是latest  最新版本
latest: Pulling from library/mysql
8559a31e96f4: Pull complete    # 分层下载，docker image的核心 联合文件系统
d51ce1c2e575: Pull complete 
c2344adc4858: Pull complete 
fcf3ceff18fc: Pull complete 
16da0c38dc5b: Pull complete 
b905d1797e97: Pull complete 
4b50d1c6b05c: Pull complete 
c75914a65ca2: Pull complete 
1ae8042bdd09: Pull complete 
453ac13c00a3: Pull complete 
9e680cd72f08: Pull complete 
a6b5dc864b6c: Pull complete 
Digest: sha256:8b7b328a7ff6de46ef96bcf83af048cb00a1c86282bfca0cb119c84568b4caf6  #  签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest   #  真实地址
```

**指定版本下载**

```shell
docker pull mysql:5.7
```

**docker rmi 删除镜像！**

```shell
[root@localhost ~]# docker rmi -f 镜像id      # 删除指定的镜像
[root@localhost ~]# docker rmi -f 镜像id 镜像id 镜像id   #删除多个镜像
[root@localhost ~]# docker rmi -f $(docker images -aq)  #删除全部的镜像
```

 

### 容器命令

> **说明：我们有了镜像才可以创建容器，linux,下载一个centos镜像来测试学习**

```shell
docker pull centos
```

> **新建容器并启动**

```shell
docker run [可选参数] image

# 参数说明
 --name="Name"    容器名字  tomcat01,用来区分容器
 -d               后台方式运行
 -it              使用交互方式运行，进入容器查看内容
 -p               指定容器的端口映射  -p  8080:8080
 		-p ip:主机端口：容器端口
 		-p 主机端口：容器端口  (常用)
 		-p 容器端口
 		-p             随机指定端口
# 测试，启动并进入容器
[root@localhost ~]# docker run -it centos /bin/bash
[root@9e2314809e14 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# 从容器中返回主机
[root@9e2314809e14 /]# exit
exit

```

> **列出所有的运行容器**

```shell
# docker ps 命令
	# 列出当前正在运行的容器
-a	# 列出当前正在运行的容器+带出历史运行过的容器
-n=? # 显示最近创建的容器
-q  # 只显示容器的编号

[root@localhost ~]#  docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@localhost ~]#  docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
9e2314809e14        centos              "/bin/bash"         5 minutes ago       Exited (0) 3 minutes ago                       busy_keldysh
9295e2ae8135        hello-world         "/hello"            2 hours ago         Exited (0) 2 hours ago                         heuristic_chandrasekhar

```

> **退出容器**

```shell
exit   # 直接容器停止并退出
ctrl + P + Q   # 容器不停止 退出
```

> **删除容器**

```shell
docker rm 容器id    # 删除指定的容器 不能删除正在运行的容器，如果要强制删除 rm -f
docker rm -f $(docker ps -aq)   # 删除全部的容器
docker ps -a -q | xargs docker rm  #删除所有容器
```

启动和停止容器的操作

```shell
docker start 容器id       #启动容器
docker  容器id     #重启容器
docker stop 容器id		#停止当前正在运行容器
docker kill 容器id		#强制停止当前正在运行容器
```



### 常用其他命令

**后台启动容器**

```shell
# 命令 docker run -d 镜像名
[root@localhost ~]# docker run -d centos

# 问题 docker ps,发现 centos 停止了

# 常见的坑：docker 容器使用后台运行，就必须要有一个前台进程，docker发现没有应用的话，就会在启动后自动停止
```

**查看日志**

```shell
docker logs -f -t --tail 容器

#显示日志
 -tf			 #显示日志
 --tail  number  #要显示的日志条数
 [root@localhost ~]# docker logs -tf --tail 10 容器id
```

**查看容器中进程信息 **ps

```shell
# 命令 docker top 容器id
[root@localhost ~]# docker top 容器id
```

**查看镜像的源数据**

```shell
# 命令
docker inspect 容器id

# 测试
[root@localhost ~]# docker inspect 9e2314809e14
[
    {
        "Id": "9e2314809e14562eb7c738e8f9270aa4ef9ea9f318536105cfd9ee497b041fc1",
        "Created": "2020-07-08T18:11:08.800649837Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "exited",
            "Running": false,
            "Paused": false,
            "ing": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 0,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-07-08T18:11:09.381597919Z",
            "FinishedAt": "2020-07-08T18:12:38.655689049Z"
        },
        "Image": "sha256:831691599b88ad6cc2a4abbd0e89661a121aff14cfa289ad840fd3946f274f1f",
        "ResolvConfPath": "/var/lib/docker/containers/9e2314809e14562eb7c738e8f9270aa4ef9ea9f318536105cfd9ee497b041fc1/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/9e2314809e14562eb7c738e8f9270aa4ef9ea9f318536105cfd9ee497b041fc1/hostname",
        "HostsPath": "/var/lib/docker/containers/9e2314809e14562eb7c738e8f9270aa4ef9ea9f318536105cfd9ee497b041fc1/hosts",
        "LogPath": "/var/lib/docker/containers/9e2314809e14562eb7c738e8f9270aa4ef9ea9f318536105cfd9ee497b041fc1/9e2314809e14562eb7c738e8f9270aa4ef9ea9f318536105cfd9ee497b041fc1-json.log",
        "Name": "/busy_keldysh",
        "Count": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "Policy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/02d6e113273aca1f07e887c348dbfb5709656f48d0aaa22a12cf1c6755b0f6aa-init/diff:/var/lib/docker/overlay2/a920b584b38e40052ac40dab4af5823e9fb6bd20997f64de8d5b4e375bd4eca2/diff",
                "MergedDir": "/var/lib/docker/overlay2/02d6e113273aca1f07e887c348dbfb5709656f48d0aaa22a12cf1c6755b0f6aa/merged",
                "UpperDir": "/var/lib/docker/overlay2/02d6e113273aca1f07e887c348dbfb5709656f48d0aaa22a12cf1c6755b0f6aa/diff",
                "WorkDir": "/var/lib/docker/overlay2/02d6e113273aca1f07e887c348dbfb5709656f48d0aaa22a12cf1c6755b0f6aa/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "9e2314809e14",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200611",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "a787b0fb192d1e5081155f81608f3cd970cec4ab859f55631f87b7901e665503",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/a787b0fb192d",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "d7c3dfad14d6bc9582490be05840c0c47c9051780abfb0b623fdb8c6f0372d87",
                    "EndpointID": "",
                    "Gateway": "",
                    "IPAddress": "",
                    "IPPrefixLen": 0,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

**进入当前正在运行的容器**

```shell
# 我们通常容器都是使用后台方式运行的，需要进入容器，修改一些配置

# 命令
exec 执行
-it 交互模式
docker exec -it 容器id bashShell

# 测试
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
9e2314809e14        centos              "/bin/bash"         6 hours ago         Up 6 seconds                            busy_keldysh
[root@localhost ~]# docker exec -it 9e2314809e14 /bin/bash
[root@9e2314809e14 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@9e2314809e14 /]# ps -ef
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 00:22 pts/0    00:00:00 /bin/bash
root         14      0  0 00:22 pts/1    00:00:00 /bin/bash
root         28     14  0 00:22 pts/1    00:00:00 ps -ef

# 方式2
docker attach 容器id
[root@localhost ~]# docker attach 容器id
正在执行当前的代码...

# docker exec		# 进入容器后开启一个新的终端，可以在里面操作（常用）
#docker attach		# 进入容器正在执行的终端，不会启动新的进程！
```

**从容器内拷贝文件到主机**

```shell
docker cp 容器id:容器内路径 目的的主机路径

# 查看当前主机目录下
[root@localhost home]# ls
bbb.java
[root@localhost home]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
9e2314809e14        centos              "/bin/bash"         6 hours ago         Up 8 minutes                            busy_keldysh
# 进入docker容器内部
[root@localhost home]# docker attach 9e2314809e14
[root@9e2314809e14 /]# cd /home
[root@9e2314809e14 home]# ls
aaa.java
# 在容器内部新建了一个test.java文件
[root@9e2314809e14 home]# touch test.java
[root@9e2314809e14 home]# exit
exit
[root@localhost home]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
9e2314809e14        centos              "/bin/bash"         6 hours ago         Exited (0) 6 seconds ago                       busy_keldysh
9295e2ae8135        hello-world         "/hello"            8 hours ago         Exited (0) 8 hours ago                         heuristic_chandrasekhar
# 将这个文件从容器内拷贝出来到主机上
[root@localhost home]# docker cp 9e2314809e14:/home/test.java /home
[root@localhost home]# ls
bbb.java  test.java

# 拷贝是一个手动过程，之后我们使用 -v 卷的技术，可以实现
```



### 小结

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630103225429-558967168.png)

```shell
attach		Attach to a running container									# 当前shell下attach连接指定运行镜像
build		Build a image from a Dockerfile									# 通过Dockerfile定制镜像
commit		Create a new image from a container changes						# 提交当前容器为新的镜像
cp			Copy files/folders from the containers filesystem to the host path		# 从容器中拷贝指定文件或者目录到宿主机中
create		Create a new container											# 创建一个新的容器，同run，但不启动容器
diff		Inspect changes on a container's filesystem 					# 查看docker 容器变化
events		Get real time events from the server							# 从docker服务获取容器实时事件
exec		Run a command in an existing container							# 在已存在的容器上运行命令
export		Stream the contents of a container as a tar archive				# 导出容器的内容流作为一个tar 归档文件[对应import]
history		Show the history of an image									# 展示一个镜像形成历史
images		List images 													# 列出系统当前镜像
import		Create a new filessystem image from the contents of a tarball	# 从tar包中的内容创建一个新的文件系统映像[对应export]
info		Display system-wide information									# 显示系统相关信息
inspect		Return low-level information on a container						# 查看容器详细信息
kill		Kill a running container										# kill指定docker容器
load		Load an image from a tar archive								# 从一个tar包中加载一个镜像[对应save]
login		Register or Login to the docker registry server					# 注册或者登录一个docker源服务器
logout		Log out from a Docker registry server							# 从当前Docker registry退出
logs		Fetch the logs of a container									# 输出当前容器日志信息
port		Lookup the public-facing port which is NAT-ed to PRIVATE_PORT	# 查看映射端口对应的容器内部源端口
pause		Pause all processes within a container							# 暂停容器
ps			List containers													# 列出容器列表
pull		Pull an image or a repository from the docker registry server	# 从docker镜像源服务器拉取指定镜像或库镜像
push		Push an image or a repository to the docker registry server		# 推送指定镜像或库镜像至docker源服务器
		 a running container										# 重启运行的容器
rm			Remove one or more containers									# 移除一个或者多个容器
rmi			Remove one or more images		# 移除一个或者多个镜像[无容器使用该镜像才可删除，否则需要删除相关容器才可以继续 或-f 强制删除]
run			Run a command in a new container								# 创建一个新的容器并运行一个命令
save		Save an image to a tar archive									# 保存一个镜像为一个tar包[对应load]
search		Search for an image on the Docker Hub							# 在docker hub中搜索镜像
start		Start a stopped containers										# 启动容器
stop		Stop a running containers										# 停止容器
tag			Tag an image into a repository									# 给源中镜像打标签
top			Lookup the running processes of a container						# 查看容器中运行的过程信息
unpause		Unpause a paused container										# 取消暂停容器
version		Show the docker version information								# 查看docker 版本号
wait		Block until a container stops,then print its exit code			# 截取容器停止时的退出状态值
```

docker的命令是十分多的，上面的都是最常用的容器和镜像的命令，之后还会有很多命令！



### 练习

> Docker 安装 Nginx

```shell
# 1、搜索镜像 docker search nginx

# 2、下载镜像 docker pull nginx
[root@localhost /]# docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
8559a31e96f4: Already exists 
8d69e59170f7: Pull complete 
3f9f1ec1d262: Pull complete 
d1f5ff4f210d: Pull complete 
1e22bfa8652e: Pull complete 
Digest: sha256:21f32f6c08406306d822a0e6e8b7dc81f53f336570e852e25fbe1e3e3d0d0133
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
# 3、运行测试 docker images
[root@localhost /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              831691599b88        2 weeks ago         215MB
nginx               latest              2622e6cca7eb        3 weeks ago         132MB
mysql               5.7                 9cfcce23593a        4 weeks ago         448MB

# -d 后台运行
# --name 给容器命名
# -p 宿主机端口：容器内部端口
[root@localhost /]# docker run -d --name nginx001 -p 3344:80 nginx
0bc8a900f47849f7b0d6489a014c4c0f5376f375044893e850352a8ff5323fe5
[root@localhost /]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
0bc8a900f478        nginx               "/docker-entrypoint.…"   8 seconds ago       Up 7 seconds        0.0.0.0:3344->80/tcp   nginx001
[root@localhost /]# curl localhost:3344

# 进入容器
[root@localhost /]# docker exec -it nginx001 /bin/bash
root@0bc8a900f478:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@0bc8a900f478:/# cd /etc/nginx
root@0bc8a900f478:/etc/nginx# ls
conf.d  fastcgi_params  koi-utf  koi-win  mime.types  modules  nginx.conf  scgi_params  uwsgi_params  win-utf
root@0bc8a900f478:/etc/nginx#

```

思考问题：我们每次改动Nginx配置文件，都需要进入容器内部？十分的麻烦，我要是可以在容器内部提供一个映射路径，达到在容器修改文件名，容器内部就可以自动修改？ -v 数据卷！



![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630103240296-1908427973.png)



> 练习 ：docker 来装一个tomcat

```shell
# 官方的使用
docker run -it --rm tomcat:9.0

#我们之前的启动都是后台，停止了容器之后，容器还是可以查到 docker run -it --rm,一般用来测试，用完即删

#下载在启动
docker pull tomcat

#启动运行
docker run -d -p 3355:8080 --name tomcat001 tomcat

#测试访问没有问题

#进入容器

#发现问题：1、linux命令少了，2、没有webapps,阿里云镜像的原因，默认是最小的镜像，所有不必要的都剔除掉
#保证最小可运行的环境！
```

思考问题：我们以后要部署项目，如果每次都需要进入容器内部？十分的麻烦，我要是可以在容器外部提供一个映射路径，webapps,我们在外部放置项目，就自动同步到内部就好了！



> 练习：部署es + kibana

```shell
# es 暴露的端口很多
# es 十分的耗内存
# es 的数据一般需要放置到安全目录！挂载
# --net somenetwork ? 网络配置 
#启动 elasticsearch
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type-single node" elasticsearch:7.6.2

# 启动了 linux就卡的要死 docker status 查看cpu状态

# es 是十分耗内存的  咱们随便搞的1核2G根本不行

# 查看 docker status

# 测试一下es是否成功了
[root@localhost /]# curl localhost:9200
# 成功了赶紧关闭，增加内存的限制,修改配置文件，-e环境配置的修改
docker run -d --name elasticsearch02 -p 9200:9200 -p 9300:9300 -e "discovery.type-single node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2

#  
```



使用kibana连接es?思考网络如何才能连接过去！

![image-20200709103118908](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709103118908.png)

### 可视化

- portainer(先用这个，不是最佳选择)

- Rancher(CI/CD再用)

**什么是portainer?**

Docker图形化界面管理工具！提供一个后台面板供我们操作！

```shell
docker run -d -p 8088:9000 \
--=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

访问测试：http://ip:8088/

通过它来访问了：

![image-20200709104131051](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709104131051.png)

选择本地的：

![image-20200709104235928](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709104235928.png)

进入之后显示如下面板：

![image-20200709104431550](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709104431550.png)

可视化平时不会使用，自己测试玩玩即可！



## 4、Docker镜像

###镜像是什么

镜像是一种轻量级，可执行的独立软件包，用来打包软件运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件。

所有的应用，直接打包docker镜像，就可以直接跑起来！

**如何得到镜像**

- 从远程仓库下载
- 朋友拷贝给你
- 自己制作一个镜像Dockerfile

### Docker镜像加载原理

> UnionFS (联合文件系统)

我们下载的时候看到的一层层就是这个！

UnionFS(联合文件系统)：Union文件系统是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同日志挂载到同一个虚拟文件系统下（unite serveral directories into a single virtual filesystem)。Union文件系统是Docker镜像的基础，镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录



> Docker镜像加载原理

docker的镜像实际上由一层一层的文件系统组成，这样层级的文件系统UnionFS。

bootfs(boot file system)主要包含bootloader和kernel,bootloader主要是引导加载kernel,Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后，整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs（root file system),在bootfs之上。包含的就是典型Linux系统中的/dev,/proc,/bin,/etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu,Centos等等。 

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630103324065-1986625349.png)

平时我们安装进虚拟机的CentOS都是好几个G,为什么Docker这里才200M?



对于一个精简的OS,rootfs可以很小，只需要包含最基本的命令，工具和程序库就可以了，因为底层直接用Host的kernel,自己只需要提供rootfs就可以了。由此可见对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以公用bootfs。



### 分层理解 

> 分层的镜像

我们可以去下载一个镜像，注意观察下载的日志输出，可以看到是一层一层的在下载！

![image-20200709111109288](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709111109288.png)

思考：为什么Docker镜像要采用这种分层的结构呢？

最大的好处，我觉得莫过于是资源共享了！比如有多个镜像都从相同的Base镜像构建而来，那么宿主机只需在磁盘上保留一份base镜像，同时内存中也只需要加载一份base镜像，这样就可以为所有的容器服务了，而且镜像的每一层都可以被共享。

查看镜像分层的方式可以通过docker image inspect 命令

```shell
[root@localhost home]# docker image inspect redis:latest
[
    //...... 
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:13cb14c2acd34e45446a50af25cb05095a17624678dbafbcc9e26086547c1d74",
                "sha256:e6b49c7dcaac7a2ec2acc379da5f5b1bcc6a5d3badd72814fe945296216557bd",
                "sha256:cdaf0fb0082b74223a224c39c2d2ea886c32f53b7e1d5b872d5354aae0df56b8",
                "sha256:72d3a7e6fe022824ee2f852ca132030e22c644fbaf8287f4ea8044268abe40b7",
                "sha256:67c707dbd847d8310d3b988c3e3d9d9eb53387ede0de472e36a15abbcb6c719c",
                "sha256:7b9c5be81844318508f57a5b0574822dabaaed3dc25ee35d960feec3a9e941c4"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

**理解：**

所有Docker镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层。

举一个简单的例子，假如基于Ubuntu Linux 16.04创建一个新的镜像，这就是新镜像的第一层；如果在该镜像中添加Python包，就会在基础镜像层之上创建第二个镜像层；如果继续添加一个安全补丁，就会创建第三个镜像层。

该镜像当前已经包含3个镜像层，如下图所示（这这是一个用于演示的例子）。

![image-20200709112219287](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709112219287.png)

在添加额外的镜像层的同时，镜像始终保持是当前所有镜像的组合，理解这一点非常重要。下图中举了一个简单的例子，每个镜像层包含3个文件，而镜像包含了来自两个镜像层的6个文件。

![image-20200709112442357](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709112442357.png)

上图中的镜像层跟之前图中的略有区别，主要目的是便于展示文件。

下图中展示了一个稍微复杂的3层镜像，在外部看来整个镜像只有6个文件，这是因为最上层中的文件7是文件5的一个更新版本。

![image-20200709112801293](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709112801293.png)

这种情况下，上层镜像层中的文件覆盖了底层镜像层中的文件。这样就使得文件的更新版本作为一个新镜像层添加到镜像中。

Docker通过存储引擎（新版本采用快照机制）的方式来实现镜像层堆栈，并保证多镜像层对外展示为统一的文件系统。

Linux上可用的存储引擎有AUFS、Overlay2、Device Mapper、Btrfs以及ZFS。顾名思义，每种存储引擎都是基于Linux中对应的文件系统或者块设备技术，并且每种存储引擎都有其独特的性能特点。

Docker在windows上仅支持windwsfilter一种存储引擎，该引擎基于NTFS文件系统之上实现了分层和Cow[1]。

下图展示了与系统显示相同的3层镜像。所有镜像层堆叠并合并，对外提供统一的视图。

![image-20200709113418399](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709113418399.png)

> 特点

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部！

这一层就是我们通常说的容器层，容器之下的都叫镜像层！

![image-20200709131914102](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709131914102.png)



如何提交一个自己的镜像

### commit镜像

```shell
docker commit 提交容器成为一个新的副本

# 命令和git原理类似
docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名：[TAG]
```

实战测试

```shell
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE                 COMMAND             CREATED             STATUS              PORTS                    NAMES
cab33aebeb25        tomcat                "catalina.sh run"   13 seconds ago      Up 11 seconds       0.0.0.0:8080->8080/tcp   youthful_bhaskara
2dd6c842fbca        portainer/portainer   "/portainer"        3 hours ago         Up 3 hours          0.0.0.0:8088->9000/tcp   lucid_pascal
# 启动一个默认的tomcat
[root@localhost ~]# docker exec -it cab33aebeb25 /bin/bash
root@cab33aebeb25:/usr/local/tomcat# cd webapps
# 发现这个默认的tomcat是没有webapps应用， 镜像的原因，官方的镜像默认webapps下面是没有文件的！
root@cab33aebeb25:/usr/local/tomcat/webapps# ls
root@cab33aebeb25:/usr/local/tomcat/webapps# cd ..
root@cab33aebeb25:/usr/local/tomcat# ls
BUILDING.txt     LICENSE  README.md      RUNNING.txt  conf  logs            temp     webapps.dist
CONTRIBUTING.md  NOTICE   RELEASE-NOTES  bin          lib   native-jni-lib  webapps  work
# 我自己拷贝进去了基本的文件
root@cab33aebeb25:/usr/local/tomcat# cp -r webapps.dist/* webapps
root@cab33aebeb25:/usr/local/tomcat# ls
BUILDING.txt     LICENSE  README.md      RUNNING.txt  conf  logs            temp     webapps.dist
CONTRIBUTING.md  NOTICE   RELEASE-NOTES  bin          lib   native-jni-lib  webapps  work
root@cab33aebeb25:/usr/local/tomcat# cd webapps
# 文件这次有了
root@cab33aebeb25:/usr/local/tomcat/webapps# ls
ROOT  docs  examples  host-manager  manager
root@cab33aebeb25:/usr/local/tomcat/webapps# cd ROOT
root@cab33aebeb25:/usr/local/tomcat/webapps/ROOT# ls
RELEASE-NOTES.txt  asf-logo-wide.svg  bg-middle.png  bg-upper.png  index.jsp         tomcat.css  tomcat.png
WEB-INF            bg-button.png      bg-nav.png     favicon.ico   tomcat-power.gif  tomcat.gif  tomcat.svg
# 退出
root@cab33aebeb25:/usr/local/tomcat/webapps/ROOT# exit
exit
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE                 COMMAND             CREATED             STATUS              PORTS                    NAMES
cab33aebeb25        tomcat                "catalina.sh run"   7 minutes ago       Up 7 minutes        0.0.0.0:8080->8080/tcp   youthful_bhaskara
2dd6c842fbca        portainer/portainer   "/portainer"        3 hours ago         Up 3 hours          0.0.0.0:8088->9000/tcp   lucid_pascal
# 将操作过的容器通过commit提交为一个镜像！以后就可以使用自己修改过的镜像即可
[root@localhost ~]# docker commit -a="lufei" -m="add webapps app" cab33aebeb25 tomcat01:1.0
sha256:2e61e83100e995edbaa204782f21fdbebb8e417f4f40795cf28cbf299dd50adb
# 查看发现 现在有我们刚提交的镜像了
[root@localhost ~]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
tomcat01              1.0                 2e61e83100e9        5 seconds ago       652MB
tomcat                latest              6055d4d564e1        2 days ago          647MB
centos                latest              831691599b88        3 weeks ago         215MB
redis                 latest              235592615444        4 weeks ago         104MB
mysql                 latest              be0dbf01a0f3        4 weeks ago         541MB
portainer/portainer   latest              cd645f5a4769        5 weeks ago         79.1MB
hello-world           latest              bf756fb1ae65        6 months ago        13.3kB
[root@localhost ~]# 
```

```shell
如果想要保存当前容器的状态，就可以通过commit来提交，获得一个镜像。
就好比VM的快照！
```

到此算是入门Docker了！



## 5、容器数据卷

### 什么是容器数据卷

**docker的里面回顾**

将应用和环境打包成一个镜像！

数据？如果数据都在容器中，那么容器一旦删除，数据就会丢失！ ==需求：数据可以持久化==

比如--MySQL,容器删了，删库跑路！==需求：MySQL数据可以存储在本地！==

容器之间可以有一个数据共享的技术！Docker容器中产生的数据，同步到本地！

这就是卷技术！目录的挂载，将我们容器内的目录，挂载到Linux上面！

![image-20200709135242247](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709135242247.png)

**总结：容器的持久化和同步操作！容器间也是可以数据共享的！**

### 使用数据卷

> 方式1：直接使用命令来挂载 -v

```shell
docker run -it -v 主机目录：容器内目录  # 类似端口映射

#测试
[root@localhost home]# docker run -it -v /home/ceshi:/home centos /bin/bash

# 启动起来时候可以通过docker inspect 容器id 查看卷是怎么挂载的，如下↓
```

![image-20200709140202228](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709140202228.png)

> 最简单的示例：在容器内home新建一个文件ccc.java

![image-20200709140518499](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709140518499.png)

> 然后在宿主机/home/ceshi里查看，发现同步了   （同步过程  双向绑定）

![image-20200709140531191](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709140531191.png)

**思考：**

每次改动Nginx配置文件，都需要进入容器内部？十分的麻烦，要是可以在容器外部提供一个映射路径，达到在容器修改文件名，容器内部就可以自动修改！ -v 数据卷！

```shell
-v /usr/share/nginx:/usr/nginx
```

**好处：**以后修改只需要在本地修改即可，容器内会自动同步！

**实战小示例：安装MySQL**

思考：MySQL的数据持久化问题

```shell
# 获取镜像

# 运行容器，需要做数据挂载！ # 安装启动MySQL，需要配置密码的，要注意！

# https://hub.docker.com/_/mysql找到官方样例命令
# docker run --name some-mysql -v /my/custom:/etc/mysql/conf.d my-secret-pw -d mysql:tag

[root@localhost home]# docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 mysql:5.7
4b141424b5a2ba32abc044afcddbda965c19f94a2b695126be93842370cd09d6
[root@localhost home]#

# 启动成功后，在本地使用Sqlyog来测试一下
# sqlyog-连接到服务器3310 --- 3310 和容器内的3306映射，这个时候就可以连接上了。
# 在本地测试创建一个数据库，查看一下映射的路径是否ok!

# 即使docker rm -f mysql02,挂载到本地的数据卷也仍旧不会丢失，这就实现了容器数据持久化功能！
```



### 具名和匿名挂载

```shell
# 匿名挂载
-v 容器内路径！
docker run -d - P --name nginx01 -v /etc/nginx nginx

# 查看所有volume的情况
[root@localhost home]# docker volume ls
local           34hl2h5lkh34kl2hl5khk2l34
# 这里发现，这种就是匿名挂载，在-v只写了容器内路径，没有写容器外路径！

# 具名挂载
[root@localhost home]# docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
# 之后再docker volume ls 就发现有指定了juming-nginx这个名字的了
```

所有的docker容器内的卷，没有指定目录的情况下都是在 ==/var/lib/docker/volumes/xxxx/==

通过具名挂载可以方便的找到一个卷，大多数情况在使用的==具名挂载==

```shell
# 如何确定是具名挂载还是匿名挂载，还是指定路径挂载！
-v 容器内路径	# 匿名挂载
-v 卷名：容器内路径	 # 具名挂载
-v /宿主机路径：：容器内路径  # 指定路径挂载！
```

拓展：

```shell
# 通过 -v 容器内路径： ro rw 改变读写权限
ro		readonly	# 只读
rw		readwrite	# 可读可写

# 一旦这个设置了容器权限，容器对我们挂载出来的内容就有限定了！
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx

# ro 只要看到ro就说明这个路径只能通过宿主机来操作，容器内部是无法操作的！
```



### 初识Dockerfile

Dockerfile就是用来构建docker镜像的构建文件！命令脚本！先体验一下！



### 数据卷容器

多个mysql同步数据！

==--volumes-from==

![image-20200709153347183](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709153347183.png)

```shell
# 比如先运行一个自定义的容器centos:1.0 命名docker01
[root@localhost home]# docker run -it --name docker01 centos:1.0

# 然后通过--volumes-from docker01 运行第二个容器docker02
[root@localhost home]# docker run -it --name docker02 --volumes-from docker01 centos:1.0

# 还可以通过--volumes-from docker01 运行第三个容器docker03
[root@localhost home]# docker run -it --name docker03 --volumes-from docker01 centos:1.0

# 最终的结果是可以达到数据共享
# 并且当删除docker01容器的时候，并不影响后面docker02，docker03的数据内容，因为是拷贝的概念
```

![image-20200709154418568](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709154418568.png)

结论：

容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止。

但是一旦持久化到了本地，这个时候本地的数据是不会删除的！



## 6、Dockerfile

### Dockerfile介绍

Dockerfile是用来构建docker镜像的文件！命令参数脚本！

构建步骤：

1、编写一个Dockerfile文件

2、docker build 构建成为一个镜像

3、docker run 运行镜像

4、docker push 发布镜像（DockerHub，阿里云镜像仓库！）

先看一下官方怎么做的？

![image-20200709155114169](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709155114169.png)



![image-20200709155145118](Docker.assets/image-20200709155145118.png)

### Dockerfile构建过程

基础知识：

1. 每个保留关键字（指令）都必须是大写字母
2. 从上到下顺序执行
3. 表示注释(#)
4. 每个指令都会创建提交一个新的镜像，并提交

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630103737429-2119801149.png)

**Dockerfile是面向开发的，以后发布项目，做镜像，就需要编写dockerfile文件**

**Docker镜像逐渐成为企业交付的标准**

Dockerfile：构建文件，定义了一切的步骤，源代码

Dockerimages：通告Dockerfile构建生成的镜像，最终发布运行的产品

Docker容器：容器就是镜像运行起来提供服务

### Dockerfile的指令

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630103751230-376026982.png)

```shell
FROM              # 基础镜像，一切从这开始构建
MAINTAINER 		  # 镜像是谁写的，姓名+邮箱
RUN				  # 镜像构建的时候需要运行的命令
ADD				  # 添加内容
WORKDIR			  # 镜像的工作命令
VOLUME     		  # 挂载的目录
EXPOSE			  # 指定暴露端口		  
CMD				  # 指定容器启动的时候运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT        # 指定这个容器启动的时候要运行的命令可以追加命令
ONBUILD			  # 当构建一个被继承的 dockerfile ，这个时候就会运行ONBUILD的指令，触发指令
COPY              # 类似ADD，将我们的文件拷贝到镜像中
ENV				  # 构建的时候设置环境变量
```

### 实战测试

**Docker Hub中99%镜像都是从这个基础镜像过来的 FROM scratch，然后配置需要的软件和配置来进行构建**





![image-20200709155145118](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709155145118.png)

**小示例：**

官方基础包都是特别精简的，很多命令都不可用，现在我们要自己制作一个添加了vim和 net-tools的镜像包

```shell
# 1.编写Dcokerfile文件
vim mydockerfile-centos

FROM centos
MAINTAINER yan<7456@qq.com>

ENV MYPATH /usr/loacl
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "----end----"
CMD /bin/bash

# 2.通过这个文件构建镜像
# 命令：docker build -f Dockerfile文件路径 -t 镜像名:[tag] .
# 注意末尾的那个 . 一定不能丢失
docker build -f mydockerfile-centos -t mycentos:0.1 .

# 3.测试运行
docker run -it mycentos:0.1
pwd
ifconfig
vim
发现都可以使用了
```

**我们可以通过 docker history+镜像id 列出本地镜像的变更历史**

我们平时拿到一个镜像，可以研究一下他是怎么做到的

> CMD和ENTRYPOINT的区别

```shell
CMD			# 指定这个容器启动的时候要运行的命令，只有最后一个生效，可被替代
ENTRYPOINT  # 指定这个容器启动的时候要运行的命令，可以追加命令
```

测试CMD

```shell
# 编写dockerfile文件
vi dockerfile-cmd-test
FROM centos
CMD ["ls","-a"]

# 构建镜像
docker build -f dockerfile-cmd-test -t cmdtest .

# RUN运行，发现我们的ls -a命令生效
docker run dd8e4401d72f

# 想追加一个命令 -l，成为ls -al
docker run dd8e4401d72f -l

docker: Error response from daemon: OCI runtime create failed: container_linux.go:349: starting container process caused "exec: \"-l\":
 executable file not found in $PATH": unknown.
ERRO[0000] error waiting for container: context canceled 
# cmd的情况下 -l 替换了CMD["ls","-l"]。 -l  不是命令所有报错
```

测试ENTRYPOINT

```shell
# 编写dockerfile文件
$ vim dockerfile-cmd-entrypoint
FROM centos
ENTRYPOINT ["ls","-a"]

$ docker build -f dockerfile-cmd-entrypoint -t entrypoint-test .

$ docker run 3c4c9621ed91   
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found ...

# 我们的命令，是直接拼接在我们得ENTRYPOINT命令后面的
$ docker run entrypoint-test:0.1 -l
total 56
drwxr-xr-x   1 root root 4096 May 16 06:32 .
drwxr-xr-x   1 root root 4096 May 16 06:32 ..
-rwxr-xr-x   1 root root    0 May 16 06:32 .dockerenv
lrwxrwxrwx   1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x   5 root root  340 May 16 06:32 dev
drwxr-xr-x   1 root root 4096 May 16 06:32 etc
drwxr-xr-x   2 root root 4096 May 11  2019 home
lrwxrwxrwx   1 root root    7 May 11  2019 lib -> usr/lib
lrwxrwxrwx   1 root root    9 May 11  2019 lib64 -> usr/lib64 ....
```



###实战：tomcat镜像

```shell
# 1. 准备镜像文件 tomcat 压缩包，jdk的压缩包
# 2. 编写dockerfile文件，官方命名Dockerfile，build会自动寻找这个文件，不需要再-f指定

FROM centos 
MAINTAINER yan<7456@qq.com>      # 名字

COPY readme.txt /usr/local/readme.txt #复制文件

ADD jdk-8u231-linux-x64.tar.gz /usr/local/ #复制解压
ADD apache-tomcat-9.0.35.tar.gz /usr/local/ #复制解压

RUN yum -y install vim

ENV MYPATH /usr/local #设置环境变量
WORKDIR $MYPATH #设置工作目录

ENV JAVA_HOME /usr/local/jdk1.8.0_11 #设置环境变量
ENV CLASSPATH $JAVA_HOME/bin/dt.jar:$JAVA_HOME/lib/tools.jar #设置环境变量 分隔符是：
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.35 #设置环境变量
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.35 #设置环境变量
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080 #设置暴露的端口
CMD /usr/local/apache-tomcat-9.0.35/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.35/bin/logs/catalina.out # 设置默认命令

# 3. 构建镜像
docker build -t diytomcat .

# 4. 运行
$ docker run -d -p 9090:8080 --name kuangshentomcat -v /home/kuangshen/build/tomcat/test:/usr/local/apache-tomcat-9.0.35/webapps/test -v /home/kuangshen/build/tomcat/tomcatlogs/:/usr/local/apache-tomcat-9.0.35/logs diytomcat
 
# 5. 访问测试

# 6. 发布项目(由于做了卷挂载，我们直接在本地编写项目就可以发布了！)
```



###发布自己的镜像

> DockerHub

**1、** **[https://hub.docker.com](https://hub.docker.com/) 注册自己的账号**

**2、确定这个账号可以登录**

**3、在我们服务器上提交自己的镜像**

**4、登录完毕之后就可以提交镜像了，docker push**

```shell
$ docker login --help
Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username

# 登录
dokcer login -u 用户名

# 上传镜像
docker push 镜像名

# push镜像的问题
 会发现push不上去，因为如果没有前缀的话默认是push到 官方的library
# 解决方法
1. 第一种 build的时候添加你的dockerhub用户名，然后在push就可以放到自己的仓库了
$ docker build -t yan/mytomcat:0.1 .

2. 第二种 使用docker tag 然后再次push
$ docker tag 镜像id yan/mytomcat:1.0 #然后再次push
```

> 阿里云镜像服务上

1. 登录阿里云
2. 找到容器镜像服务
3. 创建命名空间

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630103837865-20068667.png)

4. 创建容器镜像

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630103849540-1535113094.png)

5. 浏览阿里云

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630103904139-796318914.png)

**阿里云容器镜像的就参考官方地址**



### 小结

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630103916780-1516758943.png)





## 7、Docker网络整理

###理解Docker0

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630103929228-299123366.png)

三个网络

> 问题：docker 是如何处理容器网络访问的

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630103939469-1526891873.png)

```shell
# 测试  运行一个tomcat
docker run -d -P --name tomcat01 tomcat

# 查看容器的内部的网络地址   ip addr                               
       
docker exec -it 容器id ip addr

# 查看容器内部网络地址 发现容器启动的时候会得到一个 eth0@if551 ip地址，docker分配！
550: eth0@if551: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
       
# 思考？ linux能不能ping通容器内部！ 可以 容器内部可以ping通外界吗？ 可以！
$ ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.069 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.074 ms
```

> 原理

1、我们每启动一个docker容器，docker就会给docker容器分配一个ip ，我们只要安装了docker，就会有一个网卡docker0 桥接模式，使用的技术是 evth-pair技术

**再次测试ip addr**

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630103953816-1204182328.png)

**再启动一个容器测试，发现又多了一对网卡**

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630104005807-1911869839.png)

```shell
# 我们发现这个容器带来的网卡，都是一对一对出现的
# veth-pair 就是一对的虚拟设备接口，都是成对出现的，一段连着协议，一段彼此相连
# 正因为有这个特性，veth-pair 充当一个桥梁，连接各种虚拟网络设备的
# Openstack，Docker容器之间的连接，OVS的连接，都是使用veth-pair技术
```

我们测试 tomcat01 和 tomcat02 可以ping通

```shell
docker exec -it tomcat02 ping 172.18.0.2

# 结论：容器和容器之间是可以相互ping通的
```

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630104018459-1664572974.png)

**结论： tomcat01 和 tomcat02 是共用的一个 docker0**

**所有的容器不指定网络的情况下，都是通过docker0通信的，docker会给我们的容器分配一个默认的可用IP**

> **小结：**

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630104030319-3315550.png)



- **Docker使用的是Linux的桥接，宿主机中是一个Dokcer容器的网桥 docker0**
- **Dcoker中的所有的网络接口都是虚拟的，虚拟的转发效率高**
- **只要容器删除，对应网桥一对就都没了**

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630104041872-729612688.png)

###容器互联  --link

> 思考一个场景：编写了一个微服务，database url=ip:，项目不重启，数据库ip换掉了，我们希望可以处理这个问题，可以名字来进行访问容器？

 ```shell
$ docker exec -it tomcat02 ping tomca01   # ping不通
ping: tomca01: Name or service not known
# 如何解决？
通过 --link 解决了网络连通问题


# 运行一个tomcat03 --link tomcat02 
$ docker run -d -P --name tomcat03 --link tomcat02 tomcat
5f9331566980a9e92bc54681caaac14e9fc993f14ad13d98534026c08c0a9aef

# 用tomcat03 ping tomcat02 可以ping通
$ docker exec -it tomcat03 ping tomcat02
PING tomcat02 (172.17.0.3) 56(84) bytes of data.
64 bytes from tomcat02 (172.17.0.3): icmp_seq=1 ttl=64 time=0.115 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=2 ttl=64 time=0.080 ms

# 用tomcat02 ping tomcat03 ping不通

 ```

**探究：inspect**

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630104058050-89416980.png)

查看hosts 配置

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630104130947-443322867.png)

- **--link 就是在我们hosts配置中增加了一个 172.18.0.3 tomcat02 312857784cd4**
- **现在已经不建议使用 --link 了**

**docker0的问题：不支持容器名连接网络**



### 自定义网络

**查看所有的docker 网络**

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630104149616-896188016.png)

网络模式：

```shell
1、bridge：桥接（默认）
2、none：不配置网络
3、host：和宿主机共享网络
4、container：容器网络互通（用的少，局限大
```

测试：

```shell
# 我们直接启动的命令默认有--net bridge，而这个就是我们的docker0
docker run -d -P --name tomcat01 tomcat
等价于---> docker run -d -P --name tomcat01 --net bridge tomcat

# docker0 特点：默认bridge，域名不能访问，  --link可以打通连接,但不推荐使用

# 我们可以自定义一个网络
```

自定义网络：

```shell
# --driver  bridge
# --subnet 192.168.0.0/16
# --gateway 192.168.0.1
docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet

docker network ls

# 我们的网络就创建好了
docker network inspect mynet

# 测试
docker run -d -P --name tomcat-net-01 --network mynet tomcat
docker run -d -P --name tomcat-net-02 --network mynet tomcat


# 自定义的网络可以直接ping 通， 不需要使用 --link
```



![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630104202790-1336292174.png)

**自定义的网络 docker都已经帮我们维护好了对应的关系，推荐我们平时这样使用网络**



**好处：**

> redis：不同的集群使用不同 的网络，保证集群是安全和健康的
>
> mysql：不同的集群使用不同的网络，保证集群是安全和健康的



###网络联通

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630104214294-2005633979.png)

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630104222635-877952590.png)



![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630104232173-1112614731.png)

```shell
# 测试打通 tomcat01 - mynet
docker network connect mynet tomcat01

# 连通之后就是将 tomcat01 放到了 mynet 网络下

# 一个容器两个ip地址
```

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630104244645-1209514907.png)



![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630104253004-705220267.png)

**结论：假设要跨网络操作别人，就需要使用docker network connect 连通**





### 实战：部署Redis集群

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/2030366-20200630104304222-1806439885.png)

```shell
# 创建网卡
docker network create redis --subnet 172.38.0.0/16
# 通过脚本创建六个redis配置
for port in $(seq 1 6);\
do \
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat << EOF >> /mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done

# 通过脚本运行六个redis
for port in $(seq 1 6);\
docker run -p 637${port}:6379 -p 1667${port}:16379 --name redis-${port} \
-v /mydata/redis/node-${port}/data:/data \
-v /mydata/redis/node-${port}/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.1${port} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf


docker exec -it redis-1 /bin/sh #redis默认没有bash

# 创建集群
redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379  --cluster-replicas 1
```



###SpringBoot微服务打包Docker镜像

1、构建springboot项目

2、打包应用

3、编写dockerfile

4、构建镜像

5、发布运行



以后我们使用Docker之后，给别人交付的就是一个镜像即可！



## 8、IDEA整合Docker

## 9、Docker Compose

###简介

Compose项目是Docker官方的开源项目，负责实现对Docker容器集群的快速编排。从功能上看，跟OpenStack中的Heat十分类似。

其代码目前在https://github.com/docker/compose上开源。

### 安装Docker Compose

https://docs.docker.com/compose/install/

![image-20200709205826541](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200709205826541.png)

```shell
# 官方的有时候下载慢
curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

```shell
# 其他源
[root@localhost ~]# curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   423  100   423    0     0     51      0  0:00:08  0:00:08 --:--:--    94
100 16.2M  100 16.2M    0     0  1624k      0  0:00:10  0:00:10 --:--:-- 9340k
[root@localhost ~]# cd /usr/local/bin
# 发现docker-compose此时不是可执行的
[root@localhost bin]# ll
总用量 16636
-rw-r--r--. 1 root root 17031320 7月   9 21:17 docker-compose
# 运行如下命令使之可执行
[root@localhost bin]# chmod +x /usr/local/bin/docker-compose
# 修改后
[root@localhost bin]# ll
总用量 16636
-rwxr-xr-x. 1 root root 17031320 7月   9 21:17 docker-compose
# 查看版本，安装成功
[root@localhost bin]# docker-compose version
docker-compose version 1.25.0, build 0a186604
docker-py version: 4.1.0
CPython version: 3.7.4
OpenSSL version: OpenSSL 1.1.0l  10 Sep 2019
[root@localhost bin]# 
```

编写docker-compose.yml的时候，如果要从外部粘贴进来，请注意：

先  ：set paste 进入粘贴模式，

然后再  **i**  开始输入啊 粘贴啊



![image-20200710091838269](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200710091838269.png)



![image-20200710091847401](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/image-20200710091847401.png)

不然粘贴进来的格式混乱。

==yml文件不要使用制表符（tab），最好用两个空格代表一次缩进==

###部署Tomcat

小例子-----

Dockerfile-compose.yml

```shell
version: '3.1'
services:
  tomcat:
    restart: always
	image: tomcat
	container_name: tomcat
	ports:
	  - "8080:8080"
	volumes:
	  - ./webapps:/usr/local/tomcat/webapps
	environment:
	  TZ: Asia/Shanghai
```

### 部署MySQL

小例子----

```shell
version: '3.1'
services:
  db:
    # 目前 latest版本为MySQL8.x
	image: mysql
	restart: always
	environment:
	  MYSQL_ROOT_PASSWORD: 123456
	command:
	  --default-authentication-plugin=mysql_native_password
	  --character-set-server=utf8mb4
	  --collation-server=utf8mb4_general_ci
	  --explicit_defaults_for_timestamp= true
	  --lower_case_table_names=1
	ports:
	  - "3306:3306"
	volumes:
	  - ./data:/var/lib/mysql
 # MYSQL的Web客户端
 adminer:
   image: adminer
   restart: always
   ports:
	  - "8080:8080"
```

下面是Dockerfile文件搭配docker-compose.yml一起（==还是搞不懂有没有必要一起用==）

docker-compose.yml和Dockerfile-tomcat放在同一目录

```shell
version: '3.1'
services:
  tomcat:
    build:
      context: .
      dockerfile: Dockerfile-tomcat # 加载的外面的Dockerfile-tomcat文件
    ports:
      - "8080:8080"
  db:
    image: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    privileged: true
    command:
      --default-authentication-plugin=mysql_native_password
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
    ports:
      - "3306:3306"
    volumes:
      - ./data:/var/lib/mysql # 卷 挂载
```

通过docker-compose.xml构建

tomcat、mysql、maven、redis、nexus3、postgresql、gitlab、jenkins等

只是个简单的示例，需要什么不需要什么自行删改。

```shell
version: '3.1'
services:
  tomcat:
    build:
      context: .
      dockerfile: Dockerfile-tomcat # 加载的外面的Dockerfile-tomcat文件
    container_name: tomcat
    ports:
      - "8080:8080"
    volumes:
      - "/usr/local/docker/apache-tomcat-9.0.35/webapps:/usr/local/apache-tomcat-9.0.35/webapps"
      - "/usr/local/docker/apache-tomcat-9.0.35/logs:/usr/local/apache-tomcat-9.0.35/logs"
  db:
    image: mysql:8.0
    container_name: mysql8
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      MYSQL_DATABASE: nacos
    privileged: true
    command:
      --default-authentication-plugin=mysql_native_password
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
    ports:
      - "3306:3306"
    volumes:
      - /usr/local/docker/mysql/data:/var/lib/mysql # 卷 挂载
      - /usr/local/docker/mysql/conf:/etc/mysql/conf.d
  nexus3:
    image: sonatype/nexus3
    container_name: nexus3
    ports:
      - "8081:8081"
    volumes:
      - ./nexus3/data/:/nexus-data
      - ./nexus3/log/:/nexus-data/log/
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "10"
    restart: always
  redis:
    image: redis:latest
    container_name: redis
    privileged: true
    ports:
      - "6379:6379"
    volumes:
      - "./redis/redis_data:/data"
      - "./redis/redis_conf/redis.conf:/etc/redis/redis.conf"
  nacos:
    image: nacos/nacos-server
    container_name: nacos
    restart: always
    ports:
      - "8848:8848"
    environment:
      MODE: standalone
      MYSQL_SERVICE_HOST: mysql8.0
      MYSQL_SERVICE_DBNAME: nacos
      MYSQL_SERVICE_PORT: 3306
      JVM_XMS: 256m
      JVM_MMS: 256m
  postgresql:
    image: sameersbn/postgresql
    container_name: postgresql
    privileged: true
    environment:
      DB_NAME: gitlabhq_production
      DB_USER: gitlab
      DB_PASS: password
      DB_EXTENSION: pg_trgm
    volumes:
      - "/usr/local/docker/postgresql/data:/var/lib/postgresql sameersbn/postgresql"
  gitlab:
    image: gitlab/gitlab-ce
    container_name: gitlab
    links:
      - "postgresql:postgresql"
      - "redis:redisio"
    hostname: 192.168.0.3
    environment:
      GITLAB_PORT: 8899
      GITLAB_SSH_PORT: 10022
      GITLAB_SECRETS_DB_KEY_BASE: long‐and‐random‐alpha‐numeric‐string
      GITLAB_SECRETS_SECRET_KEY_BASE: long‐and‐random‐alpha‐numeric‐string
      GITLAB_SECRETS_OTP_KEY_BASE: long‐and‐random‐alpha‐numeric‐string
      GITLAB_HOST: 192.168.0.3
      SMTP_AUTHENTICATION: login
      # TZ: Asia/Shanghai
      # 下面这些参数通过关闭各种功能，来有效降低gitlab内存占用，使之更轻量级
      GITLAB_OMNIBUS_CONFIG: |
        external_url = '192.168.0.3'
        gitlab_rails['time_zone'] = 'Asia/Shanghai'

        # 关闭电子邮件相关功能
        # gitlab_rails['smtp_enable'] = false
        # gitlab_rails['gitlab_email_enabled'] = false
        # gitlab_rails['incoming_email_enabled'] = false

        # Terraform
        gitlab_rails['terraform_state_enabled'] = false

        # Usage Statistics
        gitlab_rails['usage_ping_enabled'] = false
        gitlab_rails['sentry_enabled'] = false
        grafana['reporting_enabled'] = false

        # 关闭容器仓库功能
        gitlab_rails['gitlab_default_projects_features_container_registry'] = false
        gitlab_rails['registry_enabled'] = false
        registry['enable'] = false
        registry_nginx['enable'] = false

        # 包仓库
        gitlab_rails['packages_enabled'] = false
        gitlab_rails['dependency_proxy_enabled'] = false

        # GitLab KAS
        gitlab_kas['enable'] = false
        gitlab_rails['gitlab_kas_enabled'] = false

        # Mattermost
        mattermost['enable'] = false
        mattermost_nginx['enable'] = false

        # Kerberos
        gitlab_rails['kerberos_enabled'] = false
        sentinel['enable'] = false

        # GitLab Pages
        gitlab_pages['enable'] = false
        pages_nginx['enable'] = false

        # 禁用 PUMA 集群模式
        puma['worker_processes'] = 0
        puma['min_threads'] = 1
        puma['max_threads'] = 2

        # 降低后台守护进程并发数
        sidekiq['max_concurrency'] = 5

        gitlab_ci['gitlab_ci_all_broken_builds'] = false
        gitlab_ci['gitlab_ci_add_pusher'] = false

        # 关闭监控
        prometheus_monitoring['enable'] = false
        alertmanager['enable'] = false
        node_exporter['enable'] = false
        redis_exporter['enable'] = false
        postgres_exporter['enable'] = false
        pgbouncer_exporter['enable'] = false
        gitlab_exporter['enable'] = false
        grafana['enable'] = false
        sidekiq['metrics_enabled'] = false
    ports:
      - "10022:22"
      - "8899:80"
    volumes:
      - "/usr/local/docker/gitlab/data:/home/git/data"
  jenkins:
    image: jenkinsci/blueocean
    container_name: jenkins
    user: root
    ports:
      - "8889:8080"
    privileged: true
    volumes:
      - "/usr/local/docker/jenkins/var/jenkins_home:/var/jenkins_home"
      - "/usr/bin/docker:/usr/bin/docker"
      - "/var/run/docker.sock:/var/run/docker.sock"
      - "/etc/localtime:/etc/localtime"
      - "/usr/bin/mv:/usr/bin/mv"
      - "/usr/local/jdk1.8.0_251/bin:/usr/local/java/jdk1.8.0_251/bin"
      - "/usr/local/apache-maven-3.6.3:/usr/local/apache-maven-3.6.3"
      - "/usr/local/jdk1.8.0_251:/usr/local/java/jdk1.8.0_251"
      - "/usr/bin/git:/usr/bin/git"
```

jenkins挂载相关说明：

不挂载的话，jenkins容器里面没有maven、jdk、git等，全局配置的时候识别不到。

```shell
-v /var/jenkins_home:/var/jenkins_home目录为容器jenkins工作目录，我们将硬盘上的一个目录挂载到这个位置，方便后续更新镜像后继续使用原来的工作目录。这里我们设置的就是上面我们创建的 /var/jenkins_home目录
-v /etc/localtime:/etc/localtime让容器使用和服务器同样的时间设置。
-v /usr/bin/docker:/usr/bin/docker 
-v /var/run/docker.sock:/var/run/docker.sock   设置docker
-v /usr/bin/mv:/usr/bin/mv 
-v /usr/local/java/jdk1.8.0_271/bin:/usr/local/java/jdk1.8.0_271/bin 设置jdk
-v /usr/local/maven3.6:/usr/local/maven3.6  设置maven
```

jenkins挂载相关参考：

[(36条消息) Docker手把手部署jenkins教程,jenkins容器带jdk,maven,docker,git_白领说事的专栏-CSDN博客_docker jenkins 自带jdk](https://blog.csdn.net/u011526721/article/details/109481400)

gitlab优化内存占用相关参数参考：

[GitLab 14 轻量化运行方案 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/389717047)



启动docker服务（如果没自启动的话）

```shell
systemctl start docker.service
```

通过docker-compose.yml构建容器

```shell
docker-compose up -d --build
```

如果上面执行后gitlab没构建成功的话，先删掉已经启动的容器，然后重启docker再重新构建。

```shell
dockerp-compose down
systemctl restart docker
docker-compose up -d --build
```



-------

防火墙相关命令：

```shell
查看状态： systemctl status firewalld 
关闭： systemctl stop firewalld
开机禁用： systemctl disable firewalld
```

----------









###踩坑

1、遇到的问题是找不到后面的路径，当把注释去掉，就找到了。。。

![image-20200710180337085](Docker超详细版教程.assets/image-20200710180337085.png)



```shell
version: '3.1'
services:
  db:
    build:
      context: .
      dockerfile: Dockerfile-mysql
  tomcat:
    build:
      context: .
      dockerfile: Dockerfile-tomcat
```

2、docker-compose up -d --build启动mysql之后，mysql总是一直重启

原因：加了-d是后台运行，前台没应用mysql就会认为没事了，就自杀了，去掉-d即可











## 10、Docker Swarm

##11、CI\CD jenkins







```shell
FROM java:8

MAINTAINER yan <yp_jobs@163.com>

VOLUME /tmp 

ADD demo-0.0.1-SNAPSHOT.jar app.jar 

RUN bash -c 'touch /app.jar'
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]

```





























