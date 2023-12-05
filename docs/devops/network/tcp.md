## tcp flag

### 图例
* ?> IP 报头（最小20Byte,最大 15[1111]  * 32 / 8, 可填充 40Byte ）： 首部长度的单位为 4Byte.

    ![](/.images/devops/network/tcp/tcp-flag-01.png)

* ?> TCP 报头(20Byte)

    ![](/.images/devops/network/tcp/tcp-flag-02.jpeg)

### 标志位解释
1. ***SYN：同步序列编号（Synchronize Sequence Numbers）***

    !> 是TCP/IP建立连接时使用的握手信号。在客户机和服务器之间建立正常的TCP网络连接时，客户机首先发出一个SYN消息，服务器使用SYN+ACK应答表示接收到了这个消息，最后客户机再以ACK消息响应。这样在客户机和服务器之间才能建立起可靠的TCP连接，数据才可以在客户机和服务器之间传递。

2. ***ACK (Acknowledge character）***

    !> ACK (Acknowledge character）即是确认字符，在数据通信中，接收站发给发送站的一种传输类控制字符。表示发来的数据已确认接收无误。在TCP/IP协议中，如果接收方成功的接收到数据，那么会回复一个ACK数据。通常ACK信号有自己固定的格式,长度大小,由接收方回复给发送方。

3. ***RST***

    !> 产生RST场景或者导致“Connection reset by peer”场景？
    * 当尝试和未开放的服务器端口建立tcp连接时，服务器tcp将会直接向客户端发送reset报文；
    * 双方之前已经正常建立了通信通道，也可能进行过了交互，当某一方在交互的过程中发生了异常，如崩溃等，异常的一方会向对端发送reset报文，通知对方将连接关闭；
    * 当收到TCP报文，但是发现该报文不是已建立的TCP连接列表可处理的，则其直接向对端发送reset报文；
    * ack报文丢失，并且超出一定的重传次数或时间后，会主动向对端发送reset报文释放该TCP连接；

#### 相关实验

![](/.images/devops/network/tcp/tcp-flag-04.png '03 :size=40%') ![](/.images/devops/network/tcp/tcp-flag-03.jpg '04 :size=50%')

* ***猜想***
    1. ack值：

        ?> 存在SYN时，ack为对方的seq+1，</br>
        存在PSH时，ack为对方的seq+len,
    2. seq值：

        ?> 存在ACK时，seq为对方的ack.

## socket_keepalive理解
* ### 前言

    ?> 1. net.ipv4.tcp_keepalive_intvl = 75 （发送探测包的周期，前提是当前连接一直没有数据交互，才会以该频率进行发送探测包，如果中途有数据交互，则会重新计时tcp_keepalive_time，到达规定时间没有数据交互，才会重新以该频率发送探测包）
    </br> 2. net.ipv4.tcp_keepalive_probes = 9  （探测失败的重试次数，发送探测包达次数限制对方依旧没有回应，则关闭自己这端的连接）
    </br> 3. net.ipv4.tcp_keepalive_time = 7200 （空闲多长时间，则发送探测包）

* ### 响应结果

    ?> 1. 正常ack，继续保持连接；
    </br> 2. 对方响应rst信号，双方重新连接。
    </br> 3. 对方无响应。

* ### 修改tcp，keepalive参数
    + #### **windows**

        没有这两个参数的话，就新建

        ![](/.images/devops/network/tcp/tcp-keepalive-01.png '01 :size=75%')

    + #### **linux**
        ```shell
        # echo 600 > /proc/sys/net/ipv4/tcp_keepalive_time
        # echo 60 > /proc/sys/net/ipv4/tcp_keepalive_intvl
        # echo 20 > /proc/sys/net/ipv4/tcp_keepalive_probes
        ```
        ![](/.images/devops/network/tcp/tcp-keepalive-02.png)

* ###  探测情况：
    1. #### 无任何干预

        ![](/.images/devops/network/tcp/tcp-keepalive-03.png '03 :size=85%')

    2. #### client 强制关闭

        ![](/.images/devops/network/tcp/tcp-keepalive-04.png '04 :size=85%')

    3. #### client->socket.close()

        ![](/.images/devops/network/tcp/tcp-keepalive-05.png '05 :size=85%')




## tcp重传 滑动窗口 流量控制 拥塞控制

## 