* ## Intro(SOCKET)

    > [?] socket 是网络架构中程序与操作系统网络协议栈进行交互的接口API。从所处的地位来讲，套接字上联应用进程，下联网络协议栈，是应用程序通过网络协议进行通信的接口。
    <br> [linux数据结构定义](https://github.com/torvalds/linux/blob/master/include/linux/net.h#L117) 、[C demo](https://gist.github.com/browny/5211329)
    <br><br>作为服务端，先创建一个socket，返回fd，然后与地址(ip+port)进行绑定，监听，然后接收
    <br>作为客户端，先创建一个socket，返回fd，然后与地址(serv ad)进行连接，发送

    + ### 系统调用

        - #### socket()

            原型：`int socket(int domain, int type, int protocol);` 例如：`socketfd = socket(AF_INET, SOCK_STREAM, 0);` [【man2】](https://man7.org/linux/man-pages/man2/socket.2.html)
            <br>用来创建一个socket，并且返回引用的文件描述符fd。

        - #### bind()

            原型：`int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);` 例如：`bind(socketfd, (struct sockaddr*)&serv_addr, sizeof(serv_addr));`[【man2】](https://man7.org/linux/man-pages/man2/bind.2.html) 
            <br>分配一个地址给socket。
            
        - #### listen()

            原型：`int listen(int sockfd, int backlog);` 例如：`listen(listenfd, 10);`[【man2】](https://man7.org/linux/man-pages/man2/listen.2.html) 
            <br>listen函数将 socket 文件描述符标记为被动 socket,也就是服务端的socket，执行完之后，文件描述符处于监听状态。使用accept获取客户端连接。backlog 定义 socket允许待处理的客户端连接的队列长度。[全连接和半连接](https://blog.isayme.org/posts/issues-47/)会使用到这个值(和内核版本有关)

        - #### connect()

            原型：`int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);` 例如：`if( connect(sockfd, (struct sockaddr *)&serv_addr, sizeof(serv_addr)) < 0)`[【man2】](https://man7.org/linux/man-pages/man2/connect.2.html) 
            <br> 客户端应用使用connect命令在本地socket和远程socket上建立连接。TCP只能调用一次进行三次握手，多次会报错。UPD的话可以调用多次改变关联的socket，好像只是注册一下对端地址 [【IBM connect】](https://www.ibm.com/docs/en/zos/2.4.0?topic=functions-connect) [【UDP之connect】](https://liubigbin.github.io/2016/07/11/UDP%E4%B9%8Bconnect/)

    + ### JAVA_SOCKET

        ![](/.images/devops/network/socket/socket-psi-01.png ':size=50%')

        > [?] `1).` 不论是`Socket`还是`ServerSocket`都会执行**createImpl()**.方法内部使用`SocketImpl`的实现(一般是`SocksSocketImpl`)的父类的 **create(stream)** 方法填充里面的fd属性。填充过程如下：
        <br><span style='padding-left:2.7em'/>先是创建 FileDescriptor fd, 然后调用到子类实现`PlainSocketImpl`的`native`方法 **socketCreate(boolean isServer)** 。
        <br><span style='padding-left:5em'/>native 对应的 [C代码](https://github.com/openjdk/jdk/blob/jdk8-b120/jdk/src/solaris/native/java/net/PlainSocketImpl.c#L181)，主要是获取当前对象的描述符对象。
        <br><span style='padding-left:5em'/>然后使用 **JVM_Socket** 方法调用到 [C++代码(hotspot JVM实现)](https://github.com/openjdk/jdk/blob/jdk8-b120/hotspot/src/share/vm/prims/jvm.cpp#L3678) 使用系统调用创建一个socket，返回文件描述符，给第一步创建的 fd 对象里面的 int 属性 fd 赋值，完成创建。
        <br><br><span style='padding-left:5em'/>⭕ 整个过程和[ FileInputStream 构造器初始化 **initIDs** ](/docs/doc/base/IO/README.md#构造器初始化解读)大同小异。

        > [?] `2).` 默认`Socket`内部使用的 impl 为`SocksSocketImpl`,也就是`PlainSocketImpl`子类。根据 [RFC 1928](https://www.rfc-editor.org/rfc/rfc1928) SOCKS(V4 & V5) socket实现。也就是具有socks代理功能的 Socket。
        <br><span style='padding-left:2.7em'/>主要体现在 [**java.net.SocksSocketImpl#connect**](https://github.com/openjdk/jdk/blob/jdk8-b120/jdk/src/share/classes/java/net/SocksSocketImpl.java#L327) 方法上。先是通过`DefaultProxySelector`获取当前系统代理，没有代理直接连接，
        <br><span style='padding-left:2.7em'/>发现代理 [参考](/docs/doc/base/misc/properties.md#systemproperties) 实例后，判断socks版本，v4时候进入 **connectV4** 方法, 不是的话，继续V5 的鉴权。完成后在刚才的TCP连接上继续发送数据。比如：http报文。
        <br><br><span style='padding-left:2.7em;color:red'>也可以通过`System.setProperty("socksProxyHost","");`设置系统属性直接连接，不管有当前系统有没有代理环境。</span>
        <br><span style='padding-left:2.7em;color:red'>另外一个需要注意的属性是`java.net.useSystemProxies`，如果设置为true，且代理工具设置过系统代理的话，上述设置系统属性的方法会失效。</span>

* ## Reference

    + https://github.com/torvalds/linux/blob/master/net/socket.c
    + https://github.com/torvalds/linux/blob/master/include/linux/net.h#L117
    + https://gist.github.com/browny/5211329
    + https://man7.org/linux/man-pages/man2/socket.2.html
    + 
    + https://github.com/openjdk/jdk/blob/jdk8-b120/jdk/src/solaris/native/java/net/PlainSocketImpl.c
    + https://github.com/openjdk/jdk/blob/jdk8-b120/hotspot/src/share/vm/prims/jvm.cpp
    + 
    + socks
    + http://chu-studio.com//posts/2015/SOCKS5协议阅读笔记
    + https://stackoverflow.com/questions/7186601/is-socks5-bind-persistent-or-one-time-only
    + https://www.rfc-editor.org/rfc/rfc1928
    + https://en.wikipedia.org/wiki/SOCKS#SOCKS_proxy_server_implementations