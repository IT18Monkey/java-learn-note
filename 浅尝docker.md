#### 什么是docker 

​	我们先来看下官方给出的说法

> Package Software into Standardized Units for Development, Shipment and Deployment

​	docker是一个工具，一个用于软件打包的工具。那这个打包工具有什么特殊的呢，他能将运行一个应用所需要的所有东西，包括程序代码、系统工具、系统库和系统设置都打成一个包。这个包被称之为容器。

我个人更倾向于将他描述为一个轻量级的虚拟机，因为它和虚拟机有着很多相似之处。

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

作为一种新兴的虚拟化方式，Docker 跟传统的虚拟化方式相比具有众多的优势。作为一种新兴的虚拟化方式，Docker 跟传统的虚拟化方式相比具有众多的优势。

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

#### 使用docker常用命令

* docker pull 
  从Docker Registry获取镜像，格式：`docker pull [选项][Docker Registry 地址[:端口号]/]仓库名[:标签]`

* docker run 

  启动一个镜像，格式：

* docker ps

  查看容器

* docker build


#### 深入理解docker命令

当利用 `docker run` 来创建容器时，Docker 在后台运行的标准操作包括：

- 检查本地是否存在指定的镜像，不存在就从公有仓库下载
- 利用镜像创建并启动一个容器
- 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
- 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
- 从地址池配置一个 ip 地址给容器
- 执行用户指定的应用程序
- 执行完毕后容器被终止

再看dockerfile执行过程

#### 技术要点

1. UnionFS与镜像分层存储

   所谓UnionFS就是把不同物理位置的目录合并mount到同一个目录中。UnionFS的一个最主要的应用是，把一张CD/DVD和一个硬盘目录给联合
   mount在一起，然后，你就可以对这个只读的CD/DVD上的文件进行修改（当然，修改的文件存于硬盘上的目录里）。主要有以下几种实现：autfs,overlayfs。下图展示了overlayfs的基本结构

   ![overlayfs lowerdir, upperdir, merged](https://docs.docker.com/storage/storagedriver/images/overlay_constructs.jpg)

   其中lower dirA / lower dirB目录和upper dir目录为来自底层文件系统的不同目录，用户可以自行指定，内部包含了用户想要合并的文件和目录，merge dir目录为挂载点。当文件系统挂载后，在merge目录下将会同时看到来自各lower和upper目录下的内容，并且用户也无法（无需）感知这些文件分别哪些来自lower dir，哪些来自upper dir，用户看见的只是一个普通的文件系统根目录而已（lower dir可以有多个也可以只有一个）。

   虽然overlayfs将不同的各层目录进行合并，但是upper dir和各lower dir这几个不同的目录并不完全等价，存在层次关系。首先当upper dir和lower dir两个目录存在同名文件时，lower dir的文件将会被隐藏，用户只能看见来自upper dir的文件，然后各个lower dir也存在相同的层次关系，较上层屏蔽叫下层的同名文件。除此之外，如果存在同名的目录，那就继续合并（lower dir和upper dir合并到挂载点目录其实就是合并一个典型的例子）。

   各层目录中的upper dir是可读写的目录，当用户通过merge dir向时其中一个来自upper dir的文件写入数据时，那数据将直接写入upper dir下原来的文件中，删除文件也是同理；而各lower dir则是只读的，在overlayfs挂载后无论如何操作merge目录中对应来自lower dir的文件或目录，lower dir中的内容均不会发生任何的改变。既然lower dir是只读的，那当用户想要往来自lower层的文件添加或修改内容时，overlayfs首先会的拷贝一份lower dir中的文件副本到upper dir中，后续的写入和修改操作将会在upper dir下的copy-up的副本文件中进行，lower dir原文件被隐藏。

   以上就是overlayfs最基本的特性，简单的总结为以下3点：（1）上下层同名目录合并；（2）上下层同名文件覆盖；（3）lower dir文件写时拷贝。这三点对用户都是不感知的。

2. Linux Namespace

   Linux为了提供更加精细的资源分配管理机制，给出了namespace机制解决方法。

   Docker利用这一技术实现容器之间的相互隔离。

3. [Linux CGroup](https://coolshell.cn/articles/17049.html)





[Comparing Virtual Machines vs Docker Containers]: https://nickjanetakis.com/blog/comparing-virtual-machines-vs-docker-containers
[Visualizing Docker Containers and Images]: http://merrigrove.blogspot.com/2015/10/visualizing-docker-containers-and-images.html

[Docker 从入门到实践]: https://docker_practice.gitee.io/

