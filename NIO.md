# 深入解析java-NIO

#### 常见的IO模型

1. 阻塞io

   最常用的io模型，默认情况下，所有文件操作都是阻塞的。以套接字接口为例：在进程空间中调用recvfrom,其系统调用直到数据包到达且被复制到应用进程的缓冲区中或者发生错误时才返回，在此期间会一直等待。

2. 非阻塞io

   recvfrom 从应用层到内核时，如果该缓冲区没有数据的话，就直接返回一个EWOULDLOCK错误，一般都对非阻塞io模型进行轮询检查这个状态，看内核是不是有数据到来。

3. io多路复用

   Linux提供select/poll可以帮助我么侦测多个fd是否处于就绪状态。select/poll是顺序扫描fd是否就绪，而且支持的fd数据有限。因此它的使用收到一些制约。Linux还提供了一个epoll系统调用，epoll使用基于事件驱动方式代替顺序扫描，因此性能更高。当有fd就绪时，立即回调函数rollback。

4. 信号驱动io模型。

   首先开启套接字接口信号驱动io功能，并通过系统调用sigaction执行一个信号处理函数（此系统调用立即返回，进程继续工作，它是非阻塞的）。当数据准备就绪时，就为该进程生成一个SIGIO信号，通过信号回调通知应用程序调用recvfrom来读取数据，并通知主循环函数处理数据。

5. 异步io：告知内核启动某个操作，并让内核在整个操作完成后（包括将数据从内核复制到用户自己的缓冲区）通知我们。这种模型和信号驱动io的主要区别时：信号驱动io由内核通知我们何时可以开始一个io操作，异步io由内核通知我们io操作何时已经完成。

#### java中的NIO

1. Buffer

   当我们进行数据传输时，往往需要使用数据缓冲区。java中Buffer类提供了这个功能，这是一个抽象类，实际使用中我们用它的实现类。它的实现类主要有以下几种：`IntBuffer,FloatBuffer,CharBuffer,DoubleBuffer,ShortBuffer,LongBuffer,ByteBuffer`

   分别对应7种基本类型的包装类。对于NIO编程而言，我们主要使用的是ByteBuffer，下面的例子中也将使用它来演示。

   学习Buffer类的使用首先需要了解三个重要的属性。position，limit，capacity。

2. Channel

3. Selector

4. NIO.2

#### Socket通信

#### netty

[NIO入门](https://www.ibm.com/developerworks/cn/education/java/j-nio/j-nio.html)

[NIO.2](https://www.ibm.com/developerworks/java/library/j-nio2-1/index.html)

[高性能IO模型浅析](https://blog.csdn.net/baixiaoshi/article/details/48708347)

《Netty权威指南》