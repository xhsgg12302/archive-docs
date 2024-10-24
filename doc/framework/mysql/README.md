* ## .frm

    1. ### .frm 格式
        - https://web.archive.org/web/20220520211318/https://dev.mysql.com/doc/internals/en/frm-file-format.html
        - https://dbsake.readthedocs.io/en/latest/appendix/frm_format.html#frm-format
        - https://sucs.swan.ac.uk/~grepwood/teamx/frm_2.html

    2. ### .frm 解析工具
        > [!] [解析工具参见](./analyze_tools.md)

    3. ### .frm 参考链接

        - [MySQL Forums Forum List  »  Data Recovery](https://forums.mysql.com/read.php?156,188552,188552)
        - [mysql 从 frm 文件恢复 table 表结构的3种方法 ](https://www.cnblogs.com/dreamanddead/p/recover-mysql-table-structure-from-frm-file.html)
        - [解析MySQL frm文件](https://blog.51cto.com/u_16213319/8545282)
        - [mySQL数据库中.frm和.myi和.myd和.ibd文件是什么文件?](https://blog.csdn.net/liu1123055728/article/details/122824425)
        - [gem install 提示 You don't have write permissions for the /Library/Ruby/Gems/2.3.0 directory](https://blog.csdn.net/LYYCasablanca000/article/details/86024688)
        - [MySQL 页完全指南——浅入深出页的原理](https://xie.infoq.cn/article/e5a721616fc4cf100b73fa296)

* ## comment[40100]

    + https://stackoverflow.com/questions/9298368/in-mysql-what-does-this-mean-40100-default-character-set-latin1

* ## select(\g|\G)

    + https://stackoverflow.com/questions/2277014/why-the-g-in-select-from-table-name-g


* ## character,collation

    |后缀|英文释义|描述|
    |:--:|:--:|:--:|
    | _ai | accent insensitive |不区分重音| 
    | _as | accent sensitive |区分重音| 
    | _ci | case insensitive |不区分大小写| 
    | _cs | case sensitive |区分大小写| 
    | _bin | binary |以二进制方式比较|

* ## mysql对库和表的概览查询

    > [?] 
    查看服务器版本号：`select version();`
    <br>查看所有的数据库：`show databases;`
    <br>查看当前使用的库：`select database();`
    <br>查看当前库中存在的表：`show tables;`
    <br>查看其他库中存在的表：`show tables from mysql;`

* ## mysql查看建表语句

    <!-- panels:start -->
    <!-- div:left-panel-50 -->
    `show create table record_format_demo;`

    ![](/.images/doc/framework/mysql/readme-03.png ':size=100%')
    <!-- div:right-panel-50 -->
    `desc employees;`

    ![](/.images/doc/framework/mysql/readme-03-01.png ':size=75%')
    <!-- panels:end -->

* ## mysql查看数据库中表的状态
    
    `show table status in myemployees where name = 'record_format_demo'\G`
    
    ![](/.images/doc/framework/mysql/readme-02.png ':size=50%')

* ## 用于显示当前会话的状态信息

    > [?] 包括当前使用的数据库、服务器版本、连接信息等
    <br>mysql> `\s`

    ![](/.images/doc/framework/mysql/readme-01.png)

* ## clear command not work

    `\! clear` `\! cls`
    + https://stackoverflow.com/questions/8807836/how-to-clear-mysql-screen-console-in-windows

* ## Reference

