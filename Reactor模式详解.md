# Reactor模式详解

###web请求处理架构

通常来说，有两种处理web请求的架构：基于线程架构和事件驱动架构

1. 基于线程架构

   使用多线程的方式处理请求，通常一个连接对应一个请求。

   优势：请求之间相互独立，一个慢请求不会影响到其他请求。

   缺陷：并发连接数有且可能会有大量的线程处于空闲等待状态，无法充分利用cpu。

2. 事件驱动架构

   事件驱动方法可以将线程从连接中分离出来，线程只处理特定的事件。

   事件驱动架构由事件生产者和事件消费者组成。生产者负责创建事件，消费者负责具体的事件处理。

### Reactor模式



[Understanding Reactor Pattern: Thread-Based and Event-Driven]: https://dzone.com/articles/understanding-reactor-pattern-thread-based-and-eve

