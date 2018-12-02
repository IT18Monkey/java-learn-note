# Reactor模式详解

### web请求处理架构

通常来说，有两种处理web请求的架构：基于线程架构和事件驱动架构

1. 基于线程架构

   使用多线程的方式处理请求，通常一个连接对应一个请求。

   优势：请求之间相互独立，一个慢请求不会影响到其他请求。

   缺陷：并发连接数有且可能会有大量的线程处于空闲等待状态，无法充分利用cpu。

2. 事件驱动架构

   事件驱动方法可以将线程从连接中分离出来，线程只处理特定的事件。

   事件驱动架构由事件生产者和事件消费者组成。生产者负责创建事件，消费者负责具体的事件处理。

### Reactor模式

​	reactor模式是事件驱动架构的一种具体实现。简单地说，它对资源事件使用单线程事件循环阻塞，并将它们分派给相应的处理程序和回调。reactor模式允许用一个简单的线程处理多个阻塞任务。该模式将模块化应用程序级代码与可重用反应器实现解耦。

​	reactor模式中有两个关键模块

1. Reactor

   Reactor运行在一个单独的线程中，它的主要工作是响应io事件，然后将这些事件分配给对应Handler来处理。

2. Handlers

   Handler负责针对不同的io事件完成具体的工作。

### Reactor线程模型

1. 单线程模型

   ![](G:\文档\java-learn-note\pic\reactor单线程模型.png)

2. 多线程模型

   ![](G:\文档\java-learn-note\pic\reactor多线程模型.png)

3. 主从多线程模型

   ![](G:\文档\java-learn-note\pic\reactor主从多线程模型.png)

### Netty中的Reactor

​	有很多著名的项目中都使用了Reactor模式，如Netty，Redis，Nginx等。下面我们看看它在Netty中的实现方式。









[Understanding Reactor Pattern: Thread-Based and Event-Driven]: https://dzone.com/articles/understanding-reactor-pattern-thread-based-and-eve

[Netty 系列之 Netty 线程模型]: https://www.infoq.cn/article/netty-threading-model

