## tun/tap
?> tun/tap 设备是操作系统内核中的虚拟网络设备，是用软件模拟的网络设备，提供与硬件网络设备完全相同的功能。主要用于用户空间和内核空间传递报文。

### **tun/tap 设备与物理网卡的区别，如图所示:**

1. 对于硬件网络设备而言，一端连接的是物理网络，一端连接的是网络协议栈。
2. 对于 tun/tap 设备而言，一端连接的是应用程序（通过 字符设备文件 /net/dev/tun），一端连接的是网络协议栈。
![](/.images/devops/network/tun-tap/tun-tap-01.png)

### **工作原理**
> 从下图可以更直观的看出 tun/tap 设备和物理设备的区别：虽然它们的一端都是连着网络协议栈，但是物理网卡另一端连接的是物理网络，而 tun/tap 设备另一端连接的是一个应用层程序，这样协议栈发送给 tun/tap  的数据包就可以被这个应用程序读取到，此时这个应用程序可以对数据包进行一些自定义的修改(比如封装成 UDP)，然后又通过网络协议栈发送出去——其实这就是目前大多数“代理”的工作原理。(Tun/tap 设备提供的虚拟网卡驱动，从tcp/ip协议栈的角度而言，它与真实网卡驱动并没有区别。)

![](/.images/devops/network/tun-tap/tun-tap-02.jpg)



#### **工作模式**
?> tun/tap 有两种模式，tun 模式 与 tap 模式。tun 设备与 tap 设备工作方式完全相同，区别在于：
1. Tun 设备是三层设备，从 /dev/net/tun 字符设备上读取的是 IP 数据包，写入的也只能是 IP 数据包，因此不能进行二层操作，如发送 ARP 请求和以太网广播。
2. Tap 设备是二层设备，处理的是二层 MAC 层数据帧，从 /dev/net/tun 字符设备上读取的是 MAC 层数据帧，写入的也只能是 MAC 层数据帧。从这点来看， Tap 虚拟设备和真实的物理网卡的能力更接近，可以与物理网卡做 bridge。

> [!] **注意事项**
* 无论是 tun 还是 tap 设备，都是通过 open/dev/net/tun 这个字符设备文件，通过 ioctl 系统调用在内核创建新的 tun、tap 设备，创建的设备并不会以文件的形式出现在 /dev/ 下，可以在 sys/class/net/ 下看到对应的网络接口 tunx 或者  tapx。
* 设备 /dev/net/tun 必须以 read/write 的方式打开。该设备也被称为克隆设备，它是创建任何 tun/tap 虚拟接口的起点。
* open 系统调用执行的时候，VFS 会为这次 open 分配一个独立的内核态 file 结构，也就是说，每次打开执行时，内核为此次打开分配的 file 结构实例不同，代表不同的字符设备。

#### **应用的数据收发过程**
1. 数据发送：应用进程 A  open/dev/net/tun 字符设备，通过 ioctl 调用创建虚拟接口 tunx 或者 tapx, ioctl 调用返回表示对应  tunx 或者 tapx 设备的文件描述符 fd ，应用 A 通过这个文件描述符 fd 写入格式化的数据，数据通过虚拟网卡驱动到达协议栈，对于协议栈来说，这个数据就像从真实网卡接收的一样。
2. 数据接收：当网络协议栈发送数据到虚拟接口 tunx 或者 tapx 时，应用进程 A 通过上述创建的设备文件描述符 fd，从中读取接口发送的数据，然后进行处理。


### **设备创建**
```shell
# 创建 tun/tap 设备
ip tuntap add dev tap0 mod tap # 创建 tap
ip tuntap add dev tun0 mod tun # 创建 tun
 
# 删除tun/tap设备
ip tuntap del dev tap0 mod tap # 删除 tap
ip tuntap del dev tun0 mod tun # 删除 tun
 
# 设置 ip 地址，up 设备
ip address add dev tap0 10.0.1.5/24
ip link set dev tap0 up
```

### **设备驱动**
* Tun/tap 驱动程序中包含两个部分，一部分是字符设备驱动，还有一部分是网卡驱动。
    1. 利用网卡驱动部分接收来自 TCP/IP 协议栈的网络分包并发送或者反过来将接收到的网络分包传给协议栈处理。
    2. 字符驱动部分则将网络分包在内核与用户态之间传送，模拟物理链路的数据接收和发送。用户态程序通过 ioctl read write 系统调用 与字符设备 /dev/net/tun 进行数据交互。
* 通过 modinfo tun 和 modinfo tap  查看 tun/tap 设备驱动：

    ![](/.images/devops/network/tun-tap/tun-tap-03.png)

### **应用场景**
* #### VPN
    + 接收数据的流程

        ?> 接收机制如图所示，黑线是公网 IP，红线是解密后的内网 IP 包。VPN 进程监听的是公网 IP + 端口，数据包经过网卡到达协议栈，到达 VPN 进程，VPN 进程解密解包后，将数据通过 字符设备文件发送给虚拟设备，再次经过协议栈的路由，最终将数据发到用户程序。

        ![](/.images/devops/network/tun-tap/tun-tap-04.png)
    + 发送数据的流程

        ?> 发送机制如图所示，红线是内网 IP，黑色线是加密过后的公网 IP 包。应用程序发送目的 IP 为 内网 IP 的数据包，到达虚拟网卡，转到字符设备文件，被 VPN 进程读取到。经过封包加密后，通过协议栈路由到网卡，最终通过公网网卡发送出去。
        
        ![](/.images/devops/network/tun-tap/tun-tap-05.png)
    + 整体流程

        ![](/.images/devops/network/tun-tap/tun-tap-03.04.png)

## Reference
* https://blog.csdn.net/VE_Edge/article/details/122721863
* https://mp.weixin.qq.com/s/JJuvhjabZOlk4AVv9HkkRQ # vpn完整流程图片来源