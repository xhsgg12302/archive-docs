# Linux 防火墙

## iptables-services
* ### 服务操作
    |index|content|
    -|-
    |安装| `yum install iptables-services` |
    |配置| ` vim /etc/sysconfig/iptables ` |
    |永久保存| ` service iptables save `|
    |状态| `systemctl status iptables` |
    |启动| `systemctl status start` |
    |停止| `systemctl status stop` |
    |重启| `systemctl status restart` |
    |关闭自启服务| `chkconfig iptables off` |
    |开启自启服务| `chkconfig iptables on` |

* ### 格式 （-t没有 默认filter）
!> iptables [-t 表名] 操作选项 [链名] [条件] [-j 控制类型]

* ### 样例
    ```shell
    # 地址转发与伪装:
    # 先进行如下的配置
    # 1.1.1.200                    1.1.1.100(eth1)                  172.25.254.72
    # server虚拟机                172.25.254.233(eth0)                    真机
    #                              desktop虚拟机                       
    # 源地址地址转发（SNAT）：伪装
    iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source 172.25.254.233

    # 目的地地址转发（DNAT）：转发
    # desktop中设置:
    iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 22 -j DNAT --to-dest 1.1.1.200:22
    ```

    + ` iptables -X -F -Z ` `：重置防火墙`
    + ` iptables -nL INPUT `
    + ` iptables -P INPUT DROP ` `：修改INPUT链默认策略，默认所有链都是[ACCEPT]`
    + ` iptables -t filter -I INPUT -p tcp --dport 80 -j DROP `
    + ` iptables -D INPUT -p tcp --dport 80 -j DROP `
    + ` iptables -D INPUT -p tcp -s 42.89.72.49 --dport 80 -j REJECT `
    + ` cat blacklist.ip | xargs -I % sudo iptables -t filter -A INPUT -s % -j DROP `
    + ` iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEP `
    + ` iptables -I INPUT -i eth0 -p tcp --tcp-flags SYN,RST,ACK SYN -j DROP`
    + ` iptables -t filter -A INPUT -p icmp -j REJECT `
    + ` iptables -I INPUT -p icmp --icmp-type 8 -j DROP ` `:我们可以ping别人,但是别人不能ping我们,[OUTPUT|0]相反`
    + ` iptables -t nat -A POSTROUTING -s 192.168.100.0/24 -o ens33 -j SNAT --to-source 192.168.200.10 `



* ### 注意事项
    ```shell
    1. iptables可以使用扩展bai模块来进行数据包du的匹配，zhi语法就是 -m modaole_name, 所以
    -m tcp 的意思是使用zhuan tcp 扩展模块shu的功能 (tcp扩展模块提供权了 --dport, --tcp-flags, --sync等功能）
    其实只用 -p tcp 了话， iptables也会默认的使用 -m tcp 来调用 tcp模块提供的功能。
    但是 -p tcp 和 -m tcp是两个不同层面的东西，一个是说当前规则作用于 tcp 协议包，而后一是说明要使用iptables的tcp模块的功能 (--dport 等)

    1. DROP,REJECT  第一个直接丢弃，需要等待客户端链接超时，而REJECT会返回一个关闭连接信息。响应快点。

    2. 使用nat 时注意修改内核参数 `echo "net.ipv4.ip_forward=1" >> /usr/lib/sysctl.d/50-default.conf`(重启) 
    或者直接 `echo 1 > /proc/sys/net/ipv4/ip_forward` 使用`sysctl -a | grep ip_forward` 查看修改后的属性
    ```


## firewalld
* ### OMITTED


## Reference
* [linux中的防火墙的基本设置---firewlld、iptables](https://blog.csdn.net/ly2020_/article/details/90747911 )