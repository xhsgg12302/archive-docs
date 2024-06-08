hello os

> [!] 操作系统


* ## 硬盘

	> [?] VDA和SDA是Linux系统中的磁盘驱动器名称。SDA 是默认的硬盘驱动器名称。SDA 通常指第一块硬盘，而SDB指第二块硬盘以此类推。VDA 是虚拟磁盘驱动器的默认名称。当使用虚拟化技术如VMware或Xen时，主机系统会将虚拟机磁盘挂载为VDA， VDB等。总之，SDA 通常是真实硬盘驱动器，而 VDA 通常是虚拟磁盘驱动器。

	1. ### 查看文件和文件夹大小

		* du -h --max-depth=0 * （列出当前文件夹下所有文件包括文件夹的大小）
		* du -sh    （统计当前文件夹的总和）
		* du -sh ./* 统计当前目录各文件夹大小
		* free -h 查看内存大小
		* lsblk 查看分区和磁盘
		* df -h 查看空间使用情况
		* fdisk -l 分区工具查看分区信息
		* cfdisk /dev/sda 查看分区
		* blkid 查看硬盘label别名
		* cat /proc/cpuinfo | grep "cpu cores" |uniq 查看CPU核心数

	
		> [!CAUTION] <span style='color: blue'>Macosx 查看文件实际占用字节：</span>
		<br><br>`stat -f%z record_format_demo.frm`
		<br>`wc -c record_format_demo.frm`

