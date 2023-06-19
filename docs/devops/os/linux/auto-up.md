# 配置Linux开机自启动 


## 方式一，(rc.local)启动说明

- 脚本位置

  `/etc/rc.local -> /etc/rc.d/rc.local`
  
- 脚本内容
  ```shell
    #!/bin/bash
    # THIS FILE IS ADDED FOR COMPATIBILITY PURPOSES
    #
    # It is highly advisable to create own systemd services or udev rules
    # to run scripts during boot instead of using this file.
    #
    # In contrast to previous versions due to parallel execution during boot
    # this script will NOT be run after all other services.
    #
    # Please note that you must run 'chmod +x /etc/rc.d/rc.local' to ensure
    # that this script will be executed during boot.
    
    touch /var/lock/subsys/local
    
    
    # nexus(maven personal server)
    # https://download.sonatype.com/nexus/oss/nexus-2.14.10-01-bundle.tar.gz
    su elizabeth -c "/home/elizabeth/nexus/nexus-2.14.10-01/bin/runself"
    
    # tomcat launch by self
    su elizabeth -c "/home/elizabeth/tomcat-8/apache-tomcat-8.0.53/bin/runself"
    
    # frp(forNAT)
    su elizabeth -c "/home/elizabeth/frp/frp_0.20.0_linux_amd64/frp_0.20.0_linux_amd64/runself"
    
    #su elizabeth -c "nohup /home/elizabeth/frp/frp_0.20.0_linux_amd64/frp_0.20.0_linux_amd64/frps -c frps.ini >frps.log 2>&1 &"
    
    
    # redis into end to ensure boot successfully
    /home/elizabeth/redis-4/redis-4.0.11/src/redis-server
    
    exit 0

  ```
  
 - nexus(runself)
   
    ```shell
    #!/bin/bash
    
    
    export JAVA_HOME=/usr/java/jdk1.8.0_181
    export PATH=$JAVA_HOME/bin:$PATH
    
    
    /home/elizabeth/nexus/nexus-2.14.10-01/bin/nexus start
    ```
    
    
- tomcat(runself)  
    >-rwxr-xr-x 1 elizabeth root

    ```shell
    #!/bin/bash
    
    export JAVA_HOME=/usr/java/jdk1.8.0_181
    export PATH=$JAVA_HOME/bin:$PATH
    
    /home/elizabeth/tomcat-8/apache-tomcat-8.0.53/bin/startup.sh
    ```
    
- frp(forNAT-runself)
    > -rwxrwxrwx 1 elizabeth root

    ```shell
    #!/bin/bash
    
    echo frpStrating ....
    
    cd /home/elizabeth/frp/frp_0.20.0_linux_amd64/frp_0.20.0_linux_amd64
    
    # 路径和命令必须分开执行，不知道为什么也就是先cd ,后命令
    ./frps -c frps.ini >/dev/null 2>&1 &
    
    echo frpStarted ...
    ```
    
    
## 方式二，（chkconfig）启动说明

- 知识点

  > 简介
  ```shell
    Linux的运行级别有7个，而这7个运行级都有各自的功能，某个程序在一个运行级别里面运行在
    另一个级别就不启动，这7个运行级别可以通过如下命令查看
    # cat /etc/inittab 文件中有相应的注释
    
    这6个运行级别分别是：
        0 系统停止 
        1 单用户模式 
        2 多用户模式除开NFS功能（进入无网络服务） 
        3 完整多用户模式 
        4 未使用 
        5 X11图形模式 
        6 重启
    一般情况下我们处于的运行级在3或者5，那么如何切换呢，使用"init + 运行级别"回车即可直接进入，
    比如输入init 0电脑就会关机，输入init 6电脑就会重启
    
    这6种模式还有许多功能，比如说如果忘记了root密码就可以进入单用户模式，
    在启动后的提示符界面下输入init = /bin/sh rw就进入了模式1，把root文件挂为读写就可以跳过
    系统认证，直接用passwd程序来更改root用户口令了，然后再启动到正常的运行级去即可


  ```
  > 制作启动脚本
  
  1 `vim /etc/init.d/zentao`
  
  ```shell
    #!/bin/bash
    
    # chkconfig: 2345 85 15
    # description: auto luanch service of zentao
    #              It has a lot of features
    # processname: zentao
    
    case "$1" in
        start  ) echo 'start'  ;;
        stop   ) echo 'stop'   ;;
        restart) echo 'restart';;
        status ) echo 'status' ;;
        *      ) echo 'invalid';;
    esac
  ```
  
  
  2 `将启动脚本权限改为可执行`
  
    > chmod a+x /etc/init.d/zentao

  3 `添加启动项并启动`
  
    > chkconfig --add zentao
    
    > chkconfig --zentao on
    
  4 `检查是否设置成功`
    
    > chkconfig –list |grep zentao

  5 `需要注意`
  
    ```shell
    在这个脚本的编写过程中，其中开头有几个虽然是以注释形式出现的属性，
    但是要注册服务的话必须拥有，其中chkconfig 2345 85 15 这三个数字表示。
    2345:表示0-6这七个等级开关（上图最后一行），
    85表示开启时候的优先级，15表示关闭的时候的优先级，
    所以开启的时候尽量数字写大点，优先级低点，关闭的时候优先级高点，最先关闭。
    ```
    
## 方式三，（ntsysv）图形化配置（root）