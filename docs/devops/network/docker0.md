## bridge网络
?> 目前的理解是  
</br> 1. 容器中的eth0接口通过veth pair 连接到docker0 网桥。
</br> 2. docker0网桥创建的时候会自动生成一个网络接口并附有172.17.0.1的ip，参考
</br> 3. docker0 与 eth0 之间没有直接的关联，而是通过 ip_forward/ docker-proxy 等技术进行转发的。参考[文档](https://medium.com/@diegogabrielschurch/how-docker-network-works-bridge-driver-e4819459cc8a)， 有以下片段。
</br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`Looking at the diagram, the interface docker0 has a connection to the eth0 interface, this gives the ability to our containers to send pings to other machines on the internet. But in reality, this connection is made using a Linux firewall named “Iptables”.`

* #### 参考资料 [one](http://web.archive.org/web/20201109220442/https://developer.ibm.com/recipes/tutorials/networking-your-docker-containers-using-docker0-bridge/)

    ![](/.images/devops/network/docker0/docker0-04.png)
* #### 参考资料 [two](https://superuser.com/questions/1560357/is-docker0-virtual-bridge-or-virtual-interface)

    ![](/.images/devops/network/docker0/docker0-05.png)
* #### 参考资料 [three](https://stackoverflow.com/questions/37536687/what-is-the-relation-between-docker0-and-eth0) [图片引用](https://community.qingcloud.com/uploads/files/1457144202977-e84946a8e9df0ac6d109c35786ac4833.png)

    ![](/.images/devops/network/docker0/docker0-06.png)



## 验证docker0->eth0原理
!> 当前操作系统的iptables服务是关闭的。但是不影响netfilter以及 ip_forward 规则。

![](/.images/devops/network/docker0/docker0-01.png)

1. ### 关闭ip_forword

    ```shell
    # 查看当前系统ip_forward状态 
    cat /proc/sys/net/ipv4/ip_forward
    1

    # 临时关闭ip_forward
    sudo sysctl -w net.ipv4.ip_forward=0
    echo 0 | sudo tee /proc/sys/net/ipv4/ip_forward

    # 永久关闭
    # 修改系统的配置文件，如 /etc/sysctl.conf 或 /etc/sysctl.d/ 中的相关参数，并重新加载配置文件。例如，编辑 /etc/sysctl.conf 并添加或修改如下行：
    # 然后使用 sysctl -p 命令重新加载配置文件使其生效。
    net.ipv4.ip_forward = 0
    ```
        
    ![](/.images/devops/network/docker0/docker0-02.png) ![](/.images/devops/network/docker0/docker0-03.png)

2. ### 删除容器到eth0的DNAT

    ?> 目前需要一些netfilter知识。以及对docker-proxy的认知。发现清空iptables后，容器映射的服务还可以访问。

3. ### iptables 和 docker-proxy 相关
    
    ?> 删除iptables相关数据后，，还是可以直接访问，原理目前大概是 使用 docker-proxy 监听 0.0.0.0：13500 --> 172.17.0.2:25500 使用代理将流量请求到容器。
    <br> docker-proxy [源代码](https://github.com/docker/docker-ce/blob/master/components/engine/cmd/docker-proxy/main.go)

    #### Reference
    * https://blog.csdn.net/m0_45406092/article/details/105913959
    * https://serverfault.com/questions/375981/delete-a-iptables-chain-with-its-all-rules

## Reference
* https://medium.com/@diegogabrielschurch/how-docker-network-works-bridge-driver-e4819459cc8a # iptables
* https://superuser.com/questions/1560357/is-docker0-virtual-bridge-or-virtual-interface
* https://stackoverflow.com/questions/49070074/what-are-docker-proxy-processes-for    # docker-proxy
* http://web.archive.org/web/20201109220442/https://developer.ibm.com/recipes/tutorials/networking-your-docker-containers-using-docker0-bridge/
* https://superuser.com/questions/1560357/is-docker0-virtual-bridge-or-virtual-interface
* https://stackoverflow.com/questions/37536687/what-is-the-relation-between-docker0-and-eth0
* https://stackoverflow.com/questions/63948879/whats-the-docker0-there
* https://community.qingcloud.com/uploads/files/1457144202977-e84946a8e9df0ac6d109c35786ac4833.png
* https://community.qingcloud.com/topic/390/dockone技术分享-五-docker网络详解及libnetwork前瞻
* https://www.google.com.hk/search?q=is-docker0-virtual-bridge-or-virtual-interface&newwindow=1&sca_esv=588287231&ei=3DxwZYjyBvPr1e8Pq9y6-AY&ved=0ahUKEwiI0JblvfqCAxXzdfUHHSuuDm8Q4dUDCBA&uact=5&oq=is-docker0-virtual-bridge-or-virtual-interface&gs_lp=Egxnd3Mtd2l6LXNlcnAiLmlzLWRvY2tlcjAtdmlydHVhbC1icmlkZ2Utb3ItdmlydHVhbC1pbnRlcmZhY2UyFBAAGIAEGOMEGOkEGOoCGLQC2AEBMhQQABiABBjjBBjpBBjqAhi0AtgBATIUEAAYgAQY4wQY6QQY6gIYtALYAQEyFBAAGIAEGOMEGOkEGOoCGLQC2AEBMhQQABiJBRjjBBjpBBjqAhi0AtgBAUi3CVC0B1i2CHACeAGQAQCYAQCgAQCqAQC4AQPIAQD4AQH4AQKoAgXCAgoQABhHGNYEGLAD4gMEGAAgQYgGAZAGCroGBAgBGAc&sclient=gws-wiz-serp