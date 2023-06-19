## 1. 查看文件和文件夹大小
```shell
du -h --max-depth=0 * （列出当前文件夹下所有文件包括文件夹的大小）
du -sh    （统计当前文件夹的总和）

du -sh ./* 统计当前目录各文件夹大小
free -h 查看内存大小
lsblk 查看分区和磁盘
df -h 查看空间使用情况
fdisk -l 分区工具查看分区信息
cfdisk /dev/sda 查看分区
blkid 查看硬盘label别名
cat /proc/cpuinfo | grep "cpu cores" |uniq 查看CPU核心数
```