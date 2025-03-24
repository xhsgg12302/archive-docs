---
---

* ## Intro(网络栈)

    + ### 直接往网卡发送数据

        > [?] 通过对网络模型以及 tcp/ip 协议栈的了解，是可以**不通过协议栈处理**而使用系统调用直接往网卡上面发送数据的。示例如下：（在 linux 上面的实验，macosx 因为限制原因，修改不了 MAC source address，但是 Linux 完全没有问题。）
        <br><br>抓包命令`sudo tcpdump -i eth0 arp -w nic.pcap`， [nic.pcap file download](/.images/devops/network/network-stack/nic.pcap)
        <br><br>![](/.images/devops/network/network-stack/network-stack-nic-send-01.png ':size=100%')

        ```c [data-cc:400px]
        #include <stdio.h>
        #include <stdlib.h>
        #include <string.h>
        #include <unistd.h>
        #include <arpa/inet.h>
        #include <net/if.h>
        #include <sys/ioctl.h>
        #include <sys/socket.h>
        #include <linux/if_packet.h>
        #include <net/ethernet.h>

        int main() {
            int sockfd;
            struct ifreq if_idx;
            struct sockaddr_ll socket_address;
            char sendbuf[1024];
            unsigned char src_mac[6] = {0x00, 0x0c, 0x29, 0x7b, 0x3e, 0x1f}; // 本机 MAC 地址
            unsigned char dst_mac[6] = {0xff, 0xff, 0xff, 0xff, 0xff, 0xff}; // 广播 MAC 地址
            unsigned char *data = "Hello Ethernet!";
            int data_len = strlen(data);

            // 创建 Packet Socket
            if ((sockfd = socket(AF_PACKET, SOCK_RAW, htons(ETH_P_ALL))) == -1) {
                perror("socket");
                exit(EXIT_FAILURE);
            }

            // 获取网络接口索引
            memset(&if_idx, 0, sizeof(struct ifreq));
            strncpy(if_idx.ifr_name, "eth0", IFNAMSIZ - 1);
            if (ioctl(sockfd, SIOCGIFINDEX, &if_idx) < 0) {
                perror("SIOCGIFINDEX");
                close(sockfd);
                exit(EXIT_FAILURE);
            }

            // 填充以太网帧头
            struct ethhdr *eh = (struct ethhdr *)sendbuf;
            memcpy(eh->h_dest, dst_mac, 6); // 设置目标 MAC 地址
            memcpy(eh->h_source, src_mac, 6); // 设置源 MAC 地址
            eh->h_proto = htons(ETH_P_ARP); // 设置协议类型（这里假设是 ARP）

            // 填充数据部分
            memcpy(sendbuf + sizeof(struct ethhdr), data, data_len);

            // 设置目标地址
            memset(&socket_address, 0, sizeof(struct sockaddr_ll));
            socket_address.sll_family = AF_PACKET;
            socket_address.sll_protocol = htons(ETH_P_ARP);
            socket_address.sll_ifindex = if_idx.ifr_ifindex;
            socket_address.sll_halen = ETH_ALEN;
            memcpy(socket_address.sll_addr, dst_mac, 6);

            // 发送数据包
            if (sendto(sockfd, sendbuf, sizeof(struct ethhdr) + data_len, 0,
                    (struct sockaddr *)&socket_address, sizeof(struct sockaddr_ll)) < 0) {
                perror("sendto");
                close(sockfd);
                exit(EXIT_FAILURE);
            }

            printf("Data sent successfully!\n");
            close(sockfd);
            return 0;
        }
        ```

## 网络堆栈
![](/.images/devops/network/network-stack/network-stack-01.png ':size=40%')
![](/.images/devops/network/network-stack/network-stack-02.png ':size=57%')

## 协议分层
![](/.images/devops/network/network-stack/network-stack-03.png ':size=48%')
![](/.images/devops/network/network-stack/network-stack-04.png ':size=50%')


## Reference
* https://www.cs.dartmouth.edu/~sergey/netreads/path-of-packet/Network_stack.pdf
* https://xingkunz.github.io/2020/02/25/Linux网络内核源码分析-网络层之IP层处理/
* https://cloud.tencent.com/developer/article/1548027