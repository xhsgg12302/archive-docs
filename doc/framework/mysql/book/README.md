* ## Intro(MySql)

    + ### 目录结构

        1. [第1章 装作自己是个小白-重新认识MySQL](./01_recognize.md)
        2. [第2章 MySQL的调控按钮-启动选项和系统变量](./02_cmd-and-system-variables.md)
        3. [第3章 乱码的前世今生-字符集和比较规则](./03_character_and_collation.md)
        4. [第4章 从一条记录说起-InnoDB记录结构](./04_innodb-record-struct.md)
        5. [第5章 盛放记录的大盒子-InnoDB数据页结构](./05_innodb-page-struct.md)
        6. [第6章 快速查询的秘籍-B+树索引](./06_B+tree_index.md)
        7. [第7章 好东西也得先学会怎么用-B+树索引的使用](./07_B+tree_index_use.md)
        8. [第8章 数据的家-MySQL的数据目录](./08_data_home_with_datadir.md)
        9. [第9章 存放页面的大池子-InnoDB的表空间](./09_innodb_table-space.md)

    + ### 书中数据库分析环境准备

        <!-- panels:start -->
        <!-- div:left-panel-55 -->
        > [?] 1). 启动一个干净的数据库，并将datadir映射到宿主机`/tmp/mysql`目录，方便后期使用(innodb_ruby|innodb-java-reader)工具对数据进行分析。
        <br>`docker run -d -p 3339:3339 --rm -e MYSQL_ALLOW_EMPTY_PASSWORD='yes' -v /tmp/mysql:/data/mysql --name mysql-5.6.49-learning--mysql-book mysql:5.6.49 --datadir=/data/mysql  --port=3339`
        <br><br>2). 使用客户端连接并执行sql查看效果
        <br>`docker run -it --rm --network=host --name mysql-book-client -e LANG="C.UTF-8" mysql:5.6.49 mysql  -h 127.0.0.1 -u root -P 3339 -p` ，空密码连接(直接回车就行)
        <br><br>`create database demos; USE demos;`
        <br>`CREATE TABLE record_format_demo (c1 VARCHAR(10), c2 VARCHAR(10) NOT NULL, c3 CHAR(10), c4 VARCHAR(10)) CHARSET=ascii ROW_FORMAT=COMPACT;`
        <br>`INSERT INTO record_format_demo(c1, c2, c3, c4) VALUES('aaaa', 'bbb', 'cc', 'd'),('eeee', 'fff', NULL, NULL);`
        <!-- div:right-panel-45 -->
        ![](/.images/doc/framework/mysql/book/readme-book-01.png ':size=100%')
        <!-- panels:end -->

    + ### 分析工具

        > [!] [分析工具参见](../analyze_tools.md)

* ## Reference
    + [MySQL是怎样运行的：从根儿上理解MySQL.pdf]()
    + https://dev.mysql.com/doc/sakila/en/
    + https://dev.mysql.com/doc/index-other.html ，sakila数据集官方下载地址，引用自上一个官方install页面。
    + https://github.com/jeremycole/innodb_ruby
    + https://github.com/alibaba/innodb-java-reader