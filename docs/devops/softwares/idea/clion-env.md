## clion安装GDB工具
```shell
# 结合brew安装 gdb@13.1, clion 不支持13.2
# 做gdb证书相关工具。重启。
# 添加gbd启动初始化脚本
echo 'set startup-with-shell off' >> ~/.gdbinit
# 结合图片进行gdb debug
```


## Reference
* [gdb hangs [New Thread of process ]](https://www.google.com.hk/search?q=gdb+hangs+%5BNew+Thread+of+process+%5D&newwindow=1&source=hp&ei=2uC9ZLiSCtLU4-EP8YaCCA&iflsig=AD69kcEAAAAAZL3u6kX9p3fVZ5__fzwBuI3iXkRwueBJ&ved=0ahUKEwi45-OBpaaAAxVS6jgGHXGDAAEQ4dUDCAk&uact=5&oq=gdb+hangs+%5BNew+Thread+of+process+%5D&gs_lp=Egdnd3Mtd2l6IiJnZGIgaGFuZ3MgW05ldyBUaHJlYWQgb2YgcHJvY2VzcyBdMgcQIRigARgKSJt5UABYm3dwAngAkAEAmAHSBKABgFaqAQsyLTIxLjEyLjEuMbgBA8gBAPgBAcICBxAAGIoFGEPCAgsQLhiABBjHARjRA8ICBRAAGIAEwgIJEAAYigUYChhDwgIFEC4YgATCAgsQABiKBRgKGEMYKsICBxAAGIAEGArCAgcQABgMGIAEwgIHEC4YDBiABMICBRAhGKABwgIHEAAYExiABMICBhAAGB4YE8ICCBAAGAUYHhgTwgIEEAAYHg&sclient=gws-wiz)
* https://apple.stackexchange.com/questions/420492/gdb-hangs-after-new-thread-on-macos
* https://stackoverflow.com/questions/60499508/why-does-gdb-hang-after-running
* https://sourceware.org/gdb/wiki/PermissionsDarwin