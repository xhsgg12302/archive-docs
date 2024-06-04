* ## Intro(MySql)

    + ### 目录结构

        1.  [第1章 装作自己是个小白-重新认识MySQL](./01_recognize.md)
        2.  [第2章 MySQL的调控按钮-启动选项和系统变量](./02_cmd-and-system-variables.md)
        3.  [第3章 乱码的前世今生-字符集和比较规则](./03_character_and_collation.md)
        4.  [第4章 从一条记录说起-InnoDB记录结构](./04_innodb-record-struct.md)
        5.  [第5章 盛放记录的大盒子-InnoDB数据页结构](./05_innodb-page-struct.md)
        6.  [第6章 快速查询的秘籍-B+树索引](./06_B+tree_index.md)
        7.  [第7章 好东西也得先学会怎么用-B+树索引的使用](./07_B+tree_index_use.md)

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

        > [?] 通过工具进行分析，结合书中的概念学习innodb存储引擎。

        - #### innodb-java-reader

            > [!NOTE][开源仓库](https://github.com/alibaba/innodb-java-reader)
            <br>通过开源innodb文件java解析框架，对数据文件`/tmp/mysql/demos/record_format_demo.ibd`进行分析。例如如下获取所有行记录。

            > [!ATTENTION]1). 当前使用的版本为 [9866b27](https://github.com/alibaba/innodb-java-reader/tree/9866b27eefdc61ed6f3282a68ce1948d462d6751)
            <br>2). 自定义了一些代码，比如`GenericRecord#toString`，修改一些错误逻辑：[`TableDefUtil#handleCharset`](https://github.com/alibaba/innodb-java-reader/blob/9866b27eefdc61ed6f3282a68ce1948d462d6751/innodb-java-reader/src/main/java/com/alibaba/innodb/java/reader/schema/TableDefUtil.java#L69)方法中判断应该是`indexof > -1`,而不是`indexof > 0`，另外扩展了自定义逻辑等。

            ![](/.images/doc/framework/mysql/book/readme-innodb-java-reader-01.png ':size=60%')
        
        - #### innodb_ruby

            > [!NOTE][开源仓库](https://github.com/jeremycole/innodb_ruby)
            <br>使用innodb_ruby工具进行文件`/tmp/mysql/ibdata1`分析：比如命令: (<span style='color: blue'>需要注意: 截图中使用脚本执行的 <span style='color: #89903f'>innodb_space</span> 命令，如果是工具的话需要转换，如下</span>)
            <br>`innodb_space -s /tmp/mysql/ibdata1 -T demos/record_format_demo space-page-type-regions`
            <br>`innodb_space -s /tmp/mysql/ibdata1 -T demos/record_format_demo -p 3 page-records`

            ![](/.images/doc/framework/mysql/book/readme-book-02.png ':size=60%')

            * ##### innodb_ruby通过gem在线安装

                > [!WARNING]参考[官方文档](https://github.com/jeremycole/innodb_ruby/wiki#installing-using-rubygems)安装完成后发现使用不了`innodb_space`命令。可能多个环境造成的。排查过程如下：
                <br><br>1). 确认安装：首先，再次确认innodb_ruby是否已成功安装。可以在命令行输入以下命令来查看：`gem list innodb_ruby`
                <br>2). 查找可执行文件位置：找到gem安装的innodb_space可执行文件的位置。通常，gem的可执行脚本会被安装到Ruby的bin目录下：`gem environment | grep "EXECUTABLE DIRECTORY"`
                <br>3). 添加到系统路径即可。可以写入`.bash_profile`或者临时添加命令`export PATH="/usr/local/lib/ruby/gems/3.3.0/bin:$PATH`

            * ##### innodb_ruby官方数据还原方法

                > [?] 1). 从[innodb_ruby代码仓库](https://github.com/jeremycole/innodb_ruby.git)克隆项目到本地，里面包含一些样例数据，比如[compact行格式数据库](https://github.com/jeremycole/innodb_ruby/tree/master/spec/data/sakila/compact)。
                <br>2). 因为数据是mysql内部文件的形式存放的，所以我们需要用一个mysql服务器来驱动这些数据，此处使用docker容器来处理，将这些文件挂载到容器中，达到还原的效果。
                <br><br>目的：<span style="color: blue">与`innodb_space`工具分析出的结论作适当对比</span>。

                **启动服务器**：

                > [?] 在docker环境中使用如下命令启动：
                <br>`docker run -d -p 3338:3338 --rm -e MYSQL_ROOT_PASSWORD='compact4321' -v /Users/stevenobelia/Documents/project_rubymine_test/innodb_ruby/spec/data/sakila/compact:/data/mysql --name mysql-5.6.49-innodb_ruby mysql:5.6.49 --datadir=/data/mysql  --port=3338`

                > [!ATTENTION] 1). <span style="color: blue">MYSQL_ROOT_PASSWORD对应的密码可以随便给一个大于6位的就行，用于客户端链接。</span>
                <br>2). `/Users/stevenobelia/Documents/project_rubymine_test/innodb_ruby/spec/data/sakila/compact`指的是克隆下来项目中的数据路径，也就是 mysql 的 datadir

                ![](/.images/doc/framework/mysql/book/readme-innodb-ruby-01.png ':size=100%')

                **客户端连接测试**:

                <!-- panels:start -->
                <!-- div:left-panel-70 -->
                > [?] 可以使用下列命令进行客户端连接测试：
                <br><br>`docker run -it --rm --network=host --name mysql-client -e LANG="C.UTF-8" mysql:5.6.49 mysql  -h 127.0.0.1 -u root -P 3338 -p`
                <br><br>输入如上`compact4321`密码登入。

                <!-- div:right-panel-30 -->
                ![](/.images/doc/framework/mysql/book/readme-innodb-ruby-02.png ':size=100%')
                <!-- panels:end -->

                **验证record_dump**:

                > [?] 按照wiki中的[record_dump](https://github.com/jeremycole/innodb_ruby/wiki#record-dump) 命令`innodb_space -s ibdata1 -T sakila/film -p 7 -R 128 record-dump` dump 出的结果如下左，对比查询出的数据记录如下右。

                <!-- panels:start -->
                <!-- div:left-panel-45 -->
                ![](/.images/doc/framework/mysql/book/readme-innodb-ruby-03.png ':size=100%')
                <!-- div:right-panel-55 -->
                ![](/.images/doc/framework/mysql/book/readme-innodb-ruby-04.png ':size=92%')
                <!-- panels:end -->

* ## Reference
    + [MySQL是怎样运行的：从根儿上理解MySQL.pdf]()
    + https://dev.mysql.com/doc/sakila/en/
    + https://dev.mysql.com/doc/index-other.html ，sakila数据集官方下载地址，引用自上一个官方install页面。
    + https://github.com/jeremycole/innodb_ruby
    + https://github.com/alibaba/innodb-java-reader