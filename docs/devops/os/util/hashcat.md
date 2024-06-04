# hashcat 简单使用

> [!] <https://github.com/hashcat/hashcat.git>

* ## rar crack

```shell
# 提取hash值，注意裁剪文件名
rar2john 9.rar > example0.hash
# output:
# $rar5$16$8345875512857bc69f0a8059ffce0166$15$7880153df518399181c9e2df158b7249$8$f627000464e14963
# 使用hashcat撞库
hashcat -a 3 -m 13000 -O -1 '?d?l' -D 1,2 -d 3,5 example0.hash '?l?l?l123'
# 自定义字符集
hashcat -a 3 -m 13000 -1 '?d?l' -D 1,2 -d 3,5 example0.hash '?1?1?1?1?1?1'
# hashcat.potfile
# /Users/stevenobelia/.local/share/hashcat/hashcat.potfile
# 查看破解记录
hashcat --show example0.hash
```

## 注意：

> 安装john-ripper后需要链接rar2john
> <https://superuser.com/questions/1652553/john-the-ripper-zip2john-command-not-found-mac/1670026>
> 创建软链

    ln -s /usr/local/Cellar/john-jumbo/1.9.0_1/share/john/rar2john /usr/local/bin/rar2john

## 错误1:

    /Users/user/.cache/hashcat/kernels/shared.77c81869.kernel: No such file or directory

    * Device #2: Kernel /usr/local/share/hashcat/OpenCL/shared.cl build failed.

> <https://github.com/hashcat/hashcat/issues/3121>
> **mkdir -p \~/.cache/hashcat/kernels/**

## 错误2

    /Users/user1/.local/share/hashcat/sessions/usage.pid: No such file or directory

    /Users/user1/.local/share/hashcat: No such file or directory

> <https://github.com/hashcat/hashcat/issues/3044>
> **mkdir -p /&lt;user&gt;/.local/share/hashcat/sessions**

## reference

*   [hashcat 官网](https://hashcat.net)
*   [Hashcat 的使用手册总结 ](https://www.cnblogs.com/chk141/p/12220288.html)