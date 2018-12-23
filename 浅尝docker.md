docker与虚拟机的区别

docker详解

仓库
公有和私有
镜像
层级概念，aufs ,dockerfile
容器

当利用 `docker run` 来创建容器时，Docker 在后台运行的标准操作包括：

- 检查本地是否存在指定的镜像，不存在就从公有仓库下载
- 利用镜像创建并启动一个容器
- 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
- 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
- 从地址池配置一个 ip 地址给容器
- 执行用户指定的应用程序
- 执行完毕后容器被终止

再看dockerfile执行过程



[Comparing Virtual Machines vs Docker Containers]: https://nickjanetakis.com/blog/comparing-virtual-machines-vs-docker-containers
[Visualizing Docker Containers and Images]: http://merrigrove.blogspot.com/2015/10/visualizing-docker-containers-and-images.html

