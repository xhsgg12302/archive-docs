## BIN目录可执行文件

* ### 查看目录

    ```shell
    # tree -L 3 /usr/local/opt/mysql@5.7/bin/

    /usr/local/opt/mysql@5.7/bin/
    ├── innochecksum
    ├── mysql
    ├── mysql.server -> ../support-files/mysql.server
    ├── mysql_client_test
    ├── mysql_client_test_embedded
    ├── mysql_install_db
    ├── mysql_plugin
    ├── mysql_secure_installation
    ├── mysql_upgrade
    ├── mysqladmin
    ├── mysqlbinlog
    ├── mysqld
    ├── mysqld_multi
    ├── mysqld_safe
    ├── mysqldump
    ├── mysqldumpslow
    ├── mysqlshow
    ├── mysqltest
    └── ...
    ```


## 启动Mysql服务器程序
* ### Unix里启动服务器程序
    + mysqld
    > mysqld这个可执行文件代表mysql服务器程序，运行这个可执行文件就可以直接启动一个服务进程，但是这个命令不常用，一般会结合其他脚本使用。 比如下面这个。
    + mysqld_safe
    > mysqld_safe是一个可执行脚本，它会嫁接调用mysqld。而且还顺带启动了一个监控进程。这个监控进程会在服务器挂了的时候重新启动它。另外，使用脚本启动的时候，它会将服务器的出错信息和其他诊断信息重新定向到某个文件中，产生错误日志，这样可以方便我们找出发生错误的原因。
    + mysql.server
    > mysql.server 也是已给启动脚本，它会间接的调用个mysqld_safe,在调用mysql.server时在后边指定 start参数就可以启动服务进程了。like this: `mysql.server start`. 需要注意的是mysql.server文件其实是一个链接文件。它的实际文件是 ../support-files/mysql.server. MacOS操作系统会帮我们在bin目录下创建一个指向实际文件的链接文件。另外 还可以通过 stop 来停止, `mysql.server stop`.
    + mysqld_multi
    >  我们一台计算机上也可以运行多个服务器实例，也即多个mysql服务进程。这个命令可以对每一个服务器进程的启动货停止进行监控。这个命令的使用也比较复杂。有兴趣可以自己想办法。

* ### window里启动服务器程序
    + mysqld
    > 同样，在mysql安装目录下的bin文件夹下有一个mysqld可执行文件，在命令行输入mysqld，或者直接双击运行它就算启动了mysql的服务进程。  
    + 以服务的方式运行
        ```shell
        # "完整的可执行文件路径" --install [-manual(表示是否手动开启)] [服务名]
        # mysqld: C:\Program Files\MySQL\MySQL Server 5.7\bin\mysqld
        $ "C:\Program Files\MySQL\MySQL Server 5.7\bin\mysqld" --install
        # 注册之后就可以通过服务管理命令启动了
        $ net start mysql
        $ net stop mysql
        ```

## 启动Mysql客户端程序