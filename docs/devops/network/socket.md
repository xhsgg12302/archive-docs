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
            <br>listen函数将 socket 文件描述符标记为被动 socket,也就是服务端的socket，执行完之后，文件描述符处于监听状态。使用accept获取客户端连接。backlog 定义 socket允许待处理的客户端连接的队列长度。全连接和半连接会使用到这个值(和内核版本有关)

* ## Reference

    + https://github.com/torvalds/linux/blob/master/net/socket.c
    + https://github.com/torvalds/linux/blob/master/include/linux/net.h#L117
    + https://gist.github.com/browny/5211329
    + https://man7.org/linux/man-pages/man2/socket.2.html