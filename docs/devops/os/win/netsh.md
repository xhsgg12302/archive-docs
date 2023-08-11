```shell
# 1、语法
netsh interface ip set|show interface|address|dnsservers
netsh interface set|add|show interface

# 2、实践

netsh interface ip show interface
netsh interface ip set address name="本地连接" source=static|dhcp 192.168.0.192 255.255.255.0 192.168.0.1
netsh interface ip set dnsservers name="本地连接" source=static addr = 1.2.4.8 primary
netsh interface ip add dnsservers name="本地连接"  114.114.114.114 index=2
netsh interface set interface [admin=] enabled|disabled [connect=] connected|disconnected [newname=] xhs12302 

# 3、路由表
route print
route delete [traget]
route add [-p] 0.0.0.0 mask 0.0.0.0 192.168.1.1 metric 2 if 2
```

![](/.images/devops/os/util/netsh.png)
