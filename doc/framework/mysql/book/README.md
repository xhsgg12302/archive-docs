---
weight: 10
---

* ## Intro(MySQL是怎样运行的：从根儿上理解MySQL)

    > [!] 书中用到的  MySQL 版本是 **5.7.21**

    + ### 目录结构

        1. [第01章 装作自己是个小白-重新认识MySQL](./01_recognize.md)
        2. [第02章 MySQL的调控按钮-启动选项和系统变量](./02_cmd-and-system-variables.md)
        3. [第03章 乱码的前世今生-字符集和比较规则](./03_character_and_collation.md)
        4. [第04章 从一条记录说起-InnoDB记录结构](./04_innodb-record-struct.md)
        5. [第05章 盛放记录的大盒子-InnoDB数据页结构](./05_innodb-page-struct.md)
        6. [第06章 快速查询的秘籍-B+树索引](./06_B+tree_index.md)
        7. [第07章 好东西也得先学会怎么用-B+树索引的使用](./07_B+tree_index_use.md)
        8. [第08章 数据的家-MySQL的数据目录](./08_data_home_with_datadir.md)
        9. [第09章 存放页面的大池子-InnoDB的表空间](./09_innodb_table-space.md)
        10. [第10章 条条大路通罗马-单表访问方法](./10_single-table-access.md)
        11. [第11章 两个表的亲密接触-连接的原理](./11_multi-table-join.md)
        12. [第12章 谁最便宜就选谁-MySQL基于成本的优化](./12_optimize-selection.md)
        13. [第13章 兵马未动，粮草先行-InnoDB统计数据是如何收集的](./13_innodb-data-collection.md)
        14. [第14章 不好看就要多整容-MySQL基于规则的优化（内含关于子查询优化二三事儿）](./14_rule-based-optimization.md)
        15. [第15章 查询优化的百科全书-Explain详解（上）](./15_explain-key-01.md)
        16. [第16章 查询优化的百科全书-Explain详解（下）](./16_explain-key-02.md)
        17. [第17章 神兵利器-optimizer trace表的神器功效](./17_optimizer-trace.md)
        18. [第18章 调节磁盘和CPU的矛盾-InnoDB的 Buffer Pool](./18_buffer-pool.md)
        19. [第19章 从猫爷被杀说起-事务简介](./19_transaction-intro.md)
        20. [第20章 说过的话就一定要办到-redo日志（上）](./20_redo-01.md)
        21. [第21章 说过的话就一定要办到-redo日志（下）](./21_redo-02.md)
        22. [第22章 后悔了怎么办-undo日志（上）](./22_undo-01.md)
        23. [第23章 后悔了怎么办-undo日志（下）](./23_undo-02.md)
        24. [第24章 一条记录的多幅面孔-事务的隔离级别与 MVCC](./24_transaction-isolate-and-mvcc.md)
        25. [第25章 工作面试老大难-锁](./25_lock.md)
        26. [第26章 写作本书时用到的一些重要的参考资料](./26_reference.md)

    + ### 书中数据库分析环境准备

        <!-- tabs:start -->
        #### **5.7.21**
        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        > [?] 1). 启动一个干净的数据库，并将datadir映射到宿主机`/tmp/mysql`目录，方便后期使用(innodb_ruby|innodb-java-reader)工具对数据进行分析。
        <br>`docker run -d -p 3339:3339 --rm -e MYSQL_ALLOW_EMPTY_PASSWORD='yes' -v /tmp/mysql:/data/mysql --name mysql-5.7.21-learning--mysql-book mysql:5.7.21 --datadir=/data/mysql  --port=3339`
        <br><br>2). 使用客户端连接并执行sql查看效果
        <br>`docker run -it --rm --network=host --name mysql-book-client -e LANG="C.UTF-8" mysql:5.7.21 mysql  -h 127.0.0.1 -u root -P 3339 -p` ，空密码连接(直接回车就行)
        <br><br>`create database demos; USE demos;`
        <br>`CREATE TABLE record_format_demo (c1 VARCHAR(10), c2 VARCHAR(10) NOT NULL, c3 CHAR(10), c4 VARCHAR(10)) CHARSET=ascii ROW_FORMAT=COMPACT;`
        <br>`INSERT INTO record_format_demo(c1, c2, c3, c4) VALUES('aaaa', 'bbb', 'cc', 'd'),('eeee', 'fff', NULL, NULL);`
        <!-- div:right-panel-50 -->
        ![](/.images/doc/framework/mysql/book/readme-book-00.png ':size=97%')
        <!-- panels:end -->

        #### **5.6.49**
        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        > [?] 1). 启动一个干净的数据库，并将datadir映射到宿主机`/tmp/mysql`目录，方便后期使用(innodb_ruby|innodb-java-reader)工具对数据进行分析。
        <br>`docker run -d -p 3339:3339 --rm -e MYSQL_ALLOW_EMPTY_PASSWORD='yes' -v /tmp/mysql:/data/mysql --name mysql-5.6.49-learning--mysql-book mysql:5.6.49 --datadir=/data/mysql  --port=3339`
        <br><br>2). 使用客户端连接并执行sql查看效果
        <br>`docker run -it --rm --network=host --name mysql-book-client -e LANG="C.UTF-8" mysql:5.6.49 mysql  -h 127.0.0.1 -u root -P 3339 -p` ，空密码连接(直接回车就行)
        <br><br>`create database demos; USE demos;`
        <br>`CREATE TABLE record_format_demo (c1 VARCHAR(10), c2 VARCHAR(10) NOT NULL, c3 CHAR(10), c4 VARCHAR(10)) CHARSET=ascii ROW_FORMAT=COMPACT;`
        <br>`INSERT INTO record_format_demo(c1, c2, c3, c4) VALUES('aaaa', 'bbb', 'cc', 'd'),('eeee', 'fff', NULL, NULL);`
        <!-- div:right-panel-50 -->
        ![](/.images/doc/framework/mysql/book/readme-book-01.png ':size=100%')
        <!-- panels:end -->
        <!-- tabs:end -->

    + ### 分析工具

        > [!] [分析工具参见](../analyze_tools.md)

* ## Reference
    + [MySQL是怎样运行的：从根儿上理解MySQL.pdf]()
    + https://dev.mysql.com/doc/sakila/en/
    + https://dev.mysql.com/doc/index-other.html ，sakila数据集官方下载地址，引用自上一个官方install页面。
    + https://github.com/jeremycole/innodb_ruby
    + https://github.com/alibaba/innodb-java-reader