* ## Intro(VPS)

    + ### 购买

        [ECS 入口](https://ecs.console.aliyun.com/)

        ![](/.images/other/misc/vps/vps-config-preview-01.png ':size=100% :align=center')

    + ### 配置远程登录

        > [?] `1).` Ali `重置密码` ，使用 ssh 登录:`47.94.20.118` 主机：`i-2zeeuol1151zswgxvpf5`
        <br><br>`2).` 修改主机名`hostname`
        <br><span style='padding-left:2.7em'/>使用 hostname 查看，或者`cat /etc/hostname`.
        <br><span style='padding-left:2.7em'/>使用`sudo hostnamectl set-hostname hostname`进行更改。
        <br><br>`3).` 配置ssh登录
        <br><span style='padding-left:2.3em'/>`3.1)` 上传ssh公匙，[参考](/docs/devops/network/ssh.md#ssh-copy-id使用) `ssh-copy-id -i iname remote`.
        <br><span style='padding-left:2.3em'/>`3.2)` 配置sshd后台进程：`vim /etc/ssh/sshd_config` [FILE](https://github.com/12302-bak/configure-files/blob/master/conf/_home/.ssh/_etc_ssh/sshd_config)
        <br><span style='padding-left:2.3em'/>`3.3)` 控制台配置安全组，开放 ssh 登录端口。
        <br><span style='padding-left:2.3em'/>`3.4)` 打开密码登录`PasswordAuthentication yes`，但是阻止 **root** 使用密码登录: `PermitRootLogin prohibit-password`，其他用户依然可以使用密码登录。
        <br><span style='padding-left:2.3em'/>`3.5)` ~配置源和安全组~
        <br><br>`4).` 安装 **docker** [参考 tab[Ubuntu:20.4/22.04]](/docs/devops/docker/docker.md#安装 )

    + ### 新建用户

        > [?] 创建新用户
        <br>`sudo useradd -m 新用户名`，这里 -m 选项表示为新用户创建家目录
        <br>`sudo passwd 新用户名`，设置密码
        <br>`grep 新用户名 /etc/passwd`，验证用户
        <br><br>权限（可选）
        <br>`添加 sudo 权限:` `sudo usermod -aG sudo [--shell /bin/bash] 新用户名`
        <br>`添加 root 权限:` 如果想让用户拥有 root 权限 ，可以执行 `sudo vim /etc/sudoers` ，增加`someone ALL=(ALL) ALL`
        <br><br>删除用户
        <br>`sudo userdel -r 新用户名`

    + ### 安装 JDK

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        > [!NOTE] 命令
        <br>`sudo apt-get install openjdk-8-jdk`
        <br>配置path路径：
        <br>`export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`
        <!-- div:right-panel-50 -->
        ```shell
        # 查看版本
        $ java -version
        openjdk version "1.8.0_422"
        OpenJDK Runtime Environment (build 1.8.0_422-8u422-b05-1~22.04-b05)
        OpenJDK 64-Bit Server VM (build 25.422-b05, mixed mode)
        ```
        <!-- panels:end -->

    + ### 安装 Mysql

        > [?] [参考：mysql docker 安装](/doc/framework/mysql/install/#docker) 
        <br>使用 docker 的方式处理就行，安装命令如下：
        <br>`1).` 加载数据：`docker run -dit --restart=always --name mysql-data registry.cn-hangzhou.aliyuncs.com/eli_w/busybox-with-mysql-datadir:1.2.0`
        <br>`2).` 运行服务：`docker run -d -p ****:3306 -e MYSQL_ROOT_PASSWORD='****' --name mysql-5.6.49 --volumes-from mysql-data  mysql:5.6.49  --datadir=/mysql-data/mysql  --character-set-server=utf8mb4  --collation-server=utf8mb4_unicode_ci`
        <br>`3).` 内部测试：`docker run -it --rm --network=host -e LANG="C.UTF-8" mysql:5.6.49 mysql  -h 127.0.0.1 -u root -P **** -p`

    + ### 安装 Tomcat

        > [?] [下载](https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.35/bin/apache-tomcat-8.5.35.tar.gz)
        <br> 修改默认端口

        - #### 创建服务开机自启
        
            ```shell
            # vim /etc/systemd/system/tomcat8.service

            [Unit]
            Description=tomcat 8 Service
            After=network.target

            [Service]
            # Type=simple
            Type=forking
            User=eli
            WorkingDirectory=/home/eli/apache-tomcat-8.5.35
            ExecStart=/home/eli/apache-tomcat-8.5.35/bin/startup.sh
            ExecStop=/home/eli/apache-tomcat-8.5.35/bin/shutdown.sh
            Restart=

            [Install]
            WantedBy=multi-user.target
            ```

    + ### 安装 Nginx

        > [?] 官方源码下载  ~[1.16.1](https://nginx.org/download/nginx-1.16.1.tar.gz)~、[1.26.2](https://nginx.org/download/nginx-1.26.2.tar.gz)
        <br>解压：`tar -zxvf nginx-1.26.2.tar.gz`
        <br>安装依赖库：`sudo apt-get install libpcre3-dev libssl-dev`
        <br>自定义openssl：`wget -c https://openssl.org/source/old/1.0.2/openssl-1.0.2u.tar.gz`、源码目录：`/data/openssl-1.0.2u`
        <br>进入且配置编译选项：[configure 配置选项文档](https://nginx.org/en/docs/configure.html)
        <br><span style='padding-left:2.7em'/>`cd nginx-1.26.2/`、
        <br><span style='padding-left:2.7em'/>`vim src/http/ngx_http_header_filter_module.c` # 49-50
        <br><span style='padding-left:2.7em'/>`./configure --prefix=/usr/local/nginx/ --with-http_gzip_static_module --with-http_ssl_module --with-http_v2_module --with-openssl=/data/openssl-1.0.2u --with-debug [--without-http_fastcgi_module]`
        <br><br>Nginx 运行配置：[nginx.conf 参考](https://github.com/12302-bak/configure-files/blob/master/conf/_other/nginx/conf/nginx.conf)
        <br><br>证书自动化部署 [参考](/docs/devops/nginx/nginx.md#使用-acmesh-自动化管理-ssltsl证书) 、 [tencent NGINX SSL 配置参考](https://cloud.tencent.com/document/product/400/4143)

        > [!CAUTION|style:flat|label:注意备案以及云服务商阻断策略]  Ali 443需要备案，不然就是莫名的访问不通，被服务商检测到后发送rst，然后 nginx 也以为客户端中断了。
        <br> 可以使用如下命令测试 `curl -v -k -H 'Host:wtfu.site' https://127.0.0.1`，测试网站的https配置是否正确，或者起作用。
        <br><br>https 好像是通过SNI进行检测的。但是出现的现象是：最新版火狐，Safari，curl都不行，但是 chrome 可以，具体没有深究。
        - #### 创建服务开机自启

            ```shell
            # vim /usr/lib/systemd/system/nginx.service

            Description=nginx - high performance web server
            After=network.target remote-fs.target nss-lookup.target
            [Service]
            Type=forking
            ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
            ExecReload=/usr/local/nginx/sbin/nginx -s reload
            ExecStop=/usr/local/nginx/sbin/nginx -s stop
            [Install]
            WantedBy=multi-user.target

            # manipulate
            systemctl disable nginx.service 关闭开机自启
            systemctl enable nginx.service 开启开机自启
            systemctl status nginx.service 查看状态
            systemctl restart nginx.service 重启服务
            systemctl list-units --type=service 查看所有服务
            ```
    + ### openssl

        > [?] https://wiki.openssl.org/index.php/Compilation_and_Installation#Configure_.26_Config

        ```shell
        # https://openssl-library.org/source/old/1.0.2/index.html
        # https://df.tips/t/topic/1843
        make clean
        ./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl shared
        ```

    + ### 安装 Frp

        > [?] 下载安装
        <br> https://github.com/fatedier/frp/releases/download/v0.34.1/frp_0.34.1_linux_amd64.tar.gz
        <br>`rsync -aze ssh --del --progress /Users/stevenobelia/Downloads/temporary-download/frp_0.34.1_linux_amd64.tar.gz  12302:/data`

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ##### 基础配置

        ```shell
        # vim /data/frp_0.34.1_linux_amd64/frps.ini

        [common]
        bind_port = 
        authentication_method = 
        token = 
        ```
        <!-- div:right-panel-50 -->
        ##### 创建服务开机自启

        ```shell
        # vim /usr/lib/systemd/system/frps.service
        
        [Unit]
        Description=Frp Server Service
        After=network.target

        [Service]
        Type=simple
        User=nobody
        Restart=on-failure
        RestartSec=5s
        ExecStart=/data/frp_0.34.1_linux_amd64/frps -c /data/frp_0.34.1_linux_amd64/frps.ini

        [Install]
        WantedBy=multi-user.target
        ```
        <!-- panels:end -->

    + ### 安装 sub-converter

        > [?] `docker run -d --restart=always --name subconverter  -p 13500:25500 tindy2013/subconverter:0.7.2`

    + ### 部署文档细节

        > [?] docs of knownledges
        <br><br>采用`easingthemes/ssh-deploy@v2.2.11`通过私匙`github_rsa_2048`部署方式。<span style='color:blue'>（注意开放云服务商 IP 白名单）</span>