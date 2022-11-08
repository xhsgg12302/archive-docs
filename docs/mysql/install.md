## 压缩包安装
* ### centos7
    - 准备
        ```shell
        # 下载MySQL5.6.49
        $ wget -c https://cdn.mysql.com/archives/mysql-5.6/mysql-5.6.49-linux-glibc2.12-x86_64.tar.gz
        # 检查是否安装过mysql,如果有先卸载, 
        # 用yum -y remove删除,如果有多个,一个一个执行,卸载不掉的用 rpm -ev
        $ rpm -qa|grep -i mysql
        # 删除所有mysql的文件夹
        $ find / -name mysql|xargs rm -rf
        # 卸载系统自带的Mariadb
        $ rpm -qa|grep mariadb
        $ rpm -ev --nodeps mariadb-libs-5.5.68-1.el7.x86_64
        # 创建my.cnf 文件,或者使用原有的进行拷贝
        $ cp /usr/local/mysql/support-files/my-default.cnf /etc/my.cnf
        # 创建mysql用户组并且创建一个用户名为mysql的用户并加入mysql用户组
        $ groupadd mysql && useradd -g mysql mysql
        # 创建数据目录或者使用原有的数据目录
        $ mkdir data && mv data mysql-5.6.49/
        # 移动安装文件夹到 /usr/local/ 并且重命名
        $ mv mysql-5.6.49-linux-glibc2.12-x86_64 mysql-5.6.49 && mv mysql-5.6.49 /usr/local/
        # 设置目录所属组和用户
        $ chown -R mysql:mysql mysql-5.6.49/
        ```
    - 正式安装
        ```shell
        # 安装
        ./scripts/mysql_install_db --user=mysql --basedir=/usr/local/mysql-5.6.49 --datadir=/usr/local/mysql-5.6.49/mysql_data/mysql --explicit_defaults_for_timestamp
        # 自启动
        $ cp ./support-files/mysql.server /etc/rc.d/init.d/mysqld
        $ chmod +x /etc/rc.d/init.d/mysqld
        $ chkconfig --add mysqld
        $ chkconfig --list mysqld
        $ service mysqld start
        # 关闭防火墙
        $ systemctl status firewalld
        $ systemctl stop firewalld
        ```
    - 测试及后续
        ```shell
        # 以root账户登录mysql,默认是没有密码的
        $ bin/mysql -uroot -p
        # 修改密码
        $ use mysql;
        $ update user set password=password('12345678') where user='mysql' and host='localhost';
        # 设置远程登录
        $ GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
        $ FLUSH PRIVILEGES;
        ```

## 配置文件
* ### my.cnf
    ```shell
    [root@c7 mysql_data]# cat /etc/my.cnf
    # For advice on how to change settings please see
    # http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html

    [mysqld]
    #
    # Remove leading # and set to the amount of RAM for the most important data
    # cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
    # innodb_buffer_pool_size = 128M
    #
    # Remove leading # to turn on a very important data integrity option: logging
    # changes to the binary log between backups.
    # log_bin
    #
    # Remove leading # to set options mainly useful for reporting servers.
    # The server defaults are faster for transactions and fast SELECTs.
    # Adjust sizes as needed, experiment to find the optimal values.
    # join_buffer_size = 128M
    # sort_buffer_size = 2M
    # read_rnd_buffer_size = 2M
    basedir=/usr/local/mysql-5.6.49
    datadir=/usr/local/mysql-5.6.49/mysql_data/mysql
    #socket=/var/lib/mysql/mysql.sock

    # Disabling symbolic-links is recommended to prevent assorted security risks
    symbolic-links=0
    # addr
    bind-address=0.0.0.0
    # port
    port=3306
    # skip-grant-tables
    # 服务端使用的字符集默认为UTF8
    character-set-server=utf8
    # 创建新表时将使用的默认存储引擎
    default-storage-engine=INNODB
    # Recommended in standard MySQL setup
    sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
    max_connections=200
    max_allowed_packet=16M
    lower_case_table_names=1

    [mysql]
    # 设置mysql客户端默认字符集
    default-character-set=utf8

    [client]
    # # 设置mysql客户端连接服务端时默认使用的端口
    # port=3306
    default-character-set=utf8

    [mysqld_safe]
    log-error=/var/log/mysqld.log
    pid-file=/var/run/mysqld/mysqld.pid
    ```

## 修改密码的几种方式
* 用SET PASSWORD命令
    > `SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpass'); FLUSH PRIVILEGES;`
* 用mysqladmin
    > `mysqladmin -u root password "newpass"` <br>
    > `如果设置过 mysqladmin -u root password oldpass "newpass"`
* 用UPDATE直接编辑user表
    > `UPDATE user SET Password = PASSWORD('newpass') WHERE user = 'root'; FLUSH PRIVILEGES;`
* 在丢失root密码的时候，可以这样
    > `mysqld_safe --skip-grant-tables & mysql -u root mysql` <br>
    > `UPDATE user SET password=PASSWORD("new password") WHERE user='root'; FLUSH PRIVILEGES;`
    
## Reference
* https://dev.mysql.com/downloads/mysql/
* https://developer.aliyun.com/article/951525