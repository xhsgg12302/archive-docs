hello fs

* [ext2-fs](./ext2.md)

* ## Problem

    + ### Linux U盘格式化救急

        > [?] **挂载磁盘不成功显示mount: /mnt: wrong fs type, bad option, bad superblock..............**
        <br><br>输入 `lsblk -a -o NAME,PHY-SEC,TRAN,PATH,MAJ:MIN,SIZE,TYPE,FSTYPE,FSSIZE,FSAVAIL,FSUSED,FSUSE%,FSVER,RO,MOUNTPOINT,VENDOR,REV` 
        <br>查看 U盘 属于那个设备，一般根据大小，生产厂商可以判断，另外有没有文件系统之类的。
        <br><br>![](/.images/devops/os/core/fs/fs-problem-u-01.png ':size=80%')
        <br><br>可以看到 sdb 并没有文件系统格式
        <br>输入`mkfs -t ext2 /dev/sdb`格式化磁盘
        <br>输入`mount /dev/sdb2 /mnt`