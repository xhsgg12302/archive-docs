## Netty 是什么
> Netty 是一个 基于 NIO的client-server(客户端服务器)框架，使用它可以快速简单地开发网络应用程序。<br>
> 它极大地简化并优化了 TCP 和 UDP套接字服务器等网络编程,并且性能以及安全性等很多方面甚至都要更好。<br>
> 支持多种协议 如 FTP，SMTP，HTTP 以及各种二进制和基于文本的传统协议。

## 优点
* placeholder
    - 统一的 API，支持多种传输类型，阻塞和非阻塞的。
    - 简单而强大的线程模型。
    - 自带编解码器解决 TCP 粘包/拆包问题。
    - 自带各种协议栈。
    - 真正的无连接数据包套接字支持。
    - 比直接使用 Java 核心 API 有更高的吞吐量、更低的延迟、更低的资源消耗和更少的内存复制。
    - 安全性不错，有完整的 SSL/TLS 以及 StartTLS 支持。
    - 社区活跃
    - 成熟稳定，经历了大型项目的使用和考验，而且很多开源项目都使用到了 Netty， 比如我们经常接触的 Dubbo、RocketMQ 等等。

## 应用场景
* placeholder
    - 作为 RPC 框架的网络通信工具
    - 实现一个自己的 HTTP 服务器
    - 实现一个即时通讯系统
    - 实现消息推送系统

## 知识点
* placeholder
    - netty 核心组件有哪些？分别有什么作用
    - EventloopGroup了解么？和Eventloop有什么关系？
    - BootStrap和ServerBootStrap了解么？
    - NioEventLoopGroup 默认的构造函数会起多少线程？
    - Netty 线程模型了解吗？
    - netty 服务端和客户端的启动过过程了解吗？
    - netty 长链接和心跳机制？
    - netty 零拷贝？
    - netty中空轮询BUG

## Reference
* https://gee.cs.oswego.edu/dl/cpjslides/nio.pdf