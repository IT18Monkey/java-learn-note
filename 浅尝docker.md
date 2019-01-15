#### 什么是docker 

​	我们先来看下维基百科给出的说法

> Docker is a computer program that performs operating-system-level virtualization, also known as "containerization. It was first released in 2013 and is developed by Docker, Inc.
>
> Docker is used to run software packages called "containers". Containers are isolated from each other and bundle their own application, tools, libraries and configuration files; they can communicate with each other through well-defined channels. All containers are run by a single operating system kernel and are thus more lightweight than virtual machines. Containers are created from "images" that specify their precise contents. Images are often created by combining and modifying standard images downloaded from public repositories.

​	简单来说，docker就是一个开源的软件工具。他能将运行一个应用所需要的所有东西，包括程序代码、系统工具、系统库和系统设置都打成一个包。这个包被称之为容器，它能运行在任何安装了docker的环境里。一次打包，到处运行，这点上有点类似jvm。你也可以理解为它是一个轻量级的虚拟机，因为它和虚拟机有着非常多的相似之处。

#### docker与虚拟机的区别

![docker](G:\文档\java-learn-note\pic\container-vm.png)

![docker](G:\文档\java-learn-note\pic\docker-containerized.png)

上面两幅图展示了用虚拟机和docker运行程序的大体结构。让我们从下往上来对比。

先看虚拟机这边：

最底层是某种类型的基础设施，可能是一台个人电脑，或者是服务器。往上，是一个名为hypervisor的虚拟机管理程序，在这之上运行客户操作系统，也就是虚拟系统，每个虚拟系统都是相互隔离的。然后,所有的应用程序都运行在虚拟系统上。这里提下，虚拟机也是可以运行在操作系统上的。它分为两种，一种直接运行在系统硬件上，创建硬件全仿真实例，被称为裸机型。一种运行在传统操作系统上，同样创建的是硬件全仿真实例，被称为“托管（宿主）”型。

再看docker这边：

最底层和虚拟机一样，是某种类型的基础设施，可能是一台个人电脑，或者是服务器。往上是一个宿主操作系统，可能是windows或者是linux。也就是说docker不能直接运行在系统硬件上。目前来说支持linux和windows。在这个操作宿主操作系统上，我们安装一个docker，而我们的应用程序全部都运行在docker上，由docker来统一管理。每个应用程序都是相互隔离的。

docker和虚拟机最大的区别就是他们的隔离级别不同。虚拟机是硬件级别的隔离，而docker只到进程级别。这一点也比较影响我们选择使用docker或是虚拟机。当然他们也不是互斥的，完全可以根据实际需要相互结合。

#### 为什么要用docker

作为一种新兴的虚拟化方式，Docker 跟传统的虚拟化方式相比具有众多的优势。

- 更高效的利用系统资源
- 更快速的启动时间
- 一致的运行环境
- 持续交付和部署
- 更轻松的迁移
- 更轻松的维护和扩展

#### docker基本概念

1. 镜像

   我们都知道，操作系统分为内核和用户空间。对于 Linux 而言，内核启动后，会挂载 `root` 文件系统为其提供用户空间支持。而 Docker 镜像（Image），就相当于是一个 `root` 文件系统。比如官方镜像 `ubuntu:18.04` 就包含了完整的一套 Ubuntu 18.04 最小系统的 `root` 文件系统。

   Docker 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。

2. 容器

   镜像（ Image ）和容器（ Container ）的关系，就像是面向对象程序设计中的
   类 和 实例 一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被
   创建、启动、停止、删除、暂停等。

3. 仓库

   镜像构建完成后，可以很容易的在当前宿主机上运行，但是，如果需要在其它服务
   器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，Docker
   Registry 就是这样的服务。
   一个 Docker Registry 中可以包含多个仓库（ Repository ）；每个仓库可以包
   含多个标签（ Tag ）；每个标签对应一个镜像。
   通常，一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的
   各个版本。我们可以通过 <仓库名>:<标签> 的格式来指定具体是这个软件哪个版
   本的镜像。如果不给出标签，将以 latest 作为默认标签。

   Docker Registry 分为公开和私有两种，用户可以根据自己的需要搭配使用。

#### docker常用命令

1. 镜像仓库操作

   * docker login/logout

     登陆/登出到一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub。

     格式：`docker login [选项] [SERVER]` ,`docker logout [选项] [SERVER]。`

   * docker pull 
     从Docker Registry获取镜像。格式：`docker pull [选项][Docker Registry 地址[:端口号]/]仓库名[:标签]。`

   * docker push

     将本地的镜像上传到镜像仓库,要先登陆到镜像仓库。格式：`docker push [选项] 仓库名[:标签]`。

   * docker search

     从Docker Hub查找镜像。格式`docker search [选项] 关键字`。

2. 镜像操作
   * docker images

     列出本地镜像。格式：`docker images [选项] [仓库名[:标签]]。`

   * docker run 

     创建一个新的容器并运行一个命令。格式：`docker run [选项] 镜像名[命令] [参数]。`

   * docker rmi 

     删除本地一个或多少镜像。格式：`docker rmi [选项] 镜像名[镜像名...]`。

   * docker build

     使用 Dockerfile 创建镜像。格式：`docker build [选项] 路径 | URL | -`。

   * docker history

     查看指定镜像的创建历史。格式：`docker history [选项] 镜像名`。

3. 容器操作
   * docker ps

     列出容器。格式：`docker ps [选项]`。

   * docker inspect

     获取容器/镜像的元数据。`docker inspect [选项] 名称|ID`。

   * docker start/stop/restart

     docker start :启动一个或多个已经被停止的容器。格式：`docker start [选项] 容器名[ [容器名...]]`。

     docker stop :停止一个运行中的容器。`docker stop [选项] 容器名[ [容器名...]]`。

     docker restart :重启容器。`docker restart [选项] 容器名[ [容器名...]]`。

   * docker kill

     杀掉一个运行中的容器。`docker kill [选项] 容器名 [容器名...]`。

   * docker rm

     删除一个或多个容器。`docker rm [选项] 容器名[容器名...]`

> 更多命令可以查看官方文档：https://docs.docker.com/engine/reference/commandline/cli/

#### 深入理解docker命令

> understanding how a technology works under the hood is the best way to achieve learning speed and to build confidence that you are using the tool in the correct way.

1. 镜像的分层存储

   因为镜像包含操作系统完整的  root  文件系统，其体积往往是庞大的，因此在Docker 设计时，就充分利用Union FS 的技术，将其设计为分层存储的架构。所以严格来说，镜像并非是像一个 ISO 那样的打包文件，镜像只是一个虚拟的概念，其实际体现并非由一个文件组成，而是由一组文件系统组成，或者说，由多层文件系
   统联合组成。

2. 使用 Linux namespace 隔离容器的运行环境

3. 使用 cgroups 限制容器使用的资源

4. 容器与镜像的区别

   当利用 `docker run` 来创建容器时，Docker 在后台运行的标准操作包括：

    - 检查本地是否存在指定的镜像，不存在就从公有仓库下载
    - 利用镜像创建并启动一个容器
    - 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
    - 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
    - 从地址池配置一个 ip 地址给容器
    - 执行用户指定的应用程序
    - 执行完毕后容器被终止

[Comparing Virtual Machines vs Docker Containers]: https://nickjanetakis.com/blog/comparing-virtual-machines-vs-docker-containers
[Visualizing Docker Containers and Images]: http://merrigrove.blogspot.com/2015/10/visualizing-docker-containers-and-images.html

