## Clion 安装 GDB 工具
```shell
# 结合brew安装 gdb@13.1, clion 不支持13.2
# PermissionsDarwin相关操作导入证书，一般不用重启。
# 添加gbd启动初始化脚本
echo 'set startup-with-shell off' >> ~/.gdbinit

# 重要的一点，使用GDB 得关闭 mac sip: 不然会一直hangs住
# 查看：csrutil status， 关闭：csrutil disable
# 结合图片进行gdb debug
```
```shell
make -v
# GNU Make 3.81
# This program built for i386-apple-darwin11.3.0
gcc -v 
# Apple clang version 14.0.3 (clang-1403.0.22.14.1)
g++ -v 
# Apple clang version 14.0.3 (clang-1403.0.22.14.1)
```


## Assert
![](../../../../.images/os/softwares/clion-debug-with-gdb.png)
![](../../../../.images/os/softwares/gdb-config-1.png)
![](../../../../.images/os/softwares/gdb-config-2.png)
![](../../../../.images/os/softwares/gdb-config-3.png)

## Reference
* https://sourceware.org/gdb/wiki/PermissionsDarwin
* https://apple.stackexchange.com/questions/420492/gdb-hangs-after-new-thread-on-macos
* https://stackoverflow.com/questions/60499508/why-does-gdb-hang-after-running
* [gdb hangs [New Thread of process ]](https://www.google.com.hk/search?q=gdb+hangs+%5BNew+Thread+of+process+%5D)