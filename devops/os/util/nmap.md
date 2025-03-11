
---
---

* ## Intro(Nmap | 参考指南)

    > [?] [拿来主义](https://nmap.org/man/zh/index.html#:~:text=是一款,服务的运行。)：Nmap (“Network Mapper(网络映射器)”) 是一款开放源代码的 网络探测和安全审核的工具。它的设计目标是快速地扫描大型网络，当然用它扫描单个主机也没有问题。Nmap以新颖的方式使用原始IP报文来发现网络上有哪些主机，那些 主机提供什么服务(应用程序名和版本)，那些服务运行在什么操作系统(包括版本信息)， 它们使用什么类型的报文过滤器/防火墙，以及一堆其它功能。虽然Nmap通常用于安全审核， 许多系统管理员和网络管理员也用它来做一些日常的工作，比如查看整个网络的信息， 管理服务升级计划，以及监视主机和服务的运行。
    <br><br>example:
    <br>`nmap -v -A scanme.nmap.org`
    <br>`nmap -v -sP 192.168.0.0/16 10.0.0.0/8`
    <br>`nmap -v -iR 10000 -P0 -p 80`

    + ### 端口状态

        | state | explain |
        | - | - |
        | open | 意味着目标机器上的应用程序正在该端口监听连接/报文 |
        | close | 端口没有应用程序在它上面监听 |
        | filtered | 意味着防火墙，过滤器或者其它网络障碍阻止了该端口被访问 <br> open/close \| filtered :有可能开放/关闭 或者被过滤，拦截等。<br>比如对于`open \| filtered`来说，如果发送的报文有返回的时候认为是 close 的，那没返回的话就无法确定到底是被过滤了，还是真的 open，`close \| filtered` 同理 |
        | unfiltered | Nmap 根据目前的探测无法确定它们是关闭还是开放（极少出现的情况吧） |

    + ### 主机发现
    + ### 扫描方式
    + ### 服务探测
    + ### 选项样例

* ## Reference
    + https://nmap.org/man/zh/index.html

## Options
| option | expalin
-|-
| --packet-trace        | 打印数据包
| --traceroute          | 路由追踪 
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
## Principle

## Examples

## References