* ## 分析工具

    > [?] 通过工具进行分析，结合书中的概念学习innodb存储引擎。
    <br><br> **.frm** 解析工具如下：
    <br>[dbsake(a(s)wiss-(a)rmy-(k)nif(e) for MySQL)](https://dbsake.readthedocs.io/en/latest/readme.html),&nbsp;&nbsp;&nbsp; [github](https://github.com/abg/dbsake)
    <br> [mysqlfrm](https://github.com/mysql/mysql-utilities/blob/master/scripts/mysqlfrm.py), &nbsp;&nbsp;&nbsp;[download archive](https://downloads.mysql.com/archives/utilities/),&nbsp;&nbsp;&nbsp; [mysql-utilities pdf](https://downloads.mysql.com/docs/mysql-utilities-1.5-en.pdf)
    <br> [frm-parser](https://github.com/fpspammers/frm-parser)
    <br><br>**.ibd** 解析工具有：
    <br>[innodb-java-reader](https://github.com/alibaba/innodb-java-reader)
    <br>[innodb_ruby](https://github.com/jeremycole/innodb_ruby)

    - ### frm-parser

        > [!NOTE] [开源仓库](https://github.com/fpspammers/frm-parser)
        <br>使用frm解析工具对`/tmp/mysql/demos/record_format_demo.frm` ,进行分析，解析结果如下。

        > [!CAUTION]
        1). 对代码稍作修改，使用cmake构建，修改文件位置为绝对路径，`show_tables()`,`show_db()`函数中的文件位置也需要改变。

        ![](/.images/doc/framework/mysql/mysql-analyze-tool-01.png ':size=60%')

    - ### dbsake

        > [!NOTE] 参考[官方文档](https://dbsake.readthedocs.io/en/latest/readme.html#quickstart)进行安装测试。自定义安装位置,比如：`cd /usr/local/bin`
        <br>`curl -s get.dbsake.net > dbsake`
        <br>`chmod u+x dbsake`
        <br>`dbsake -?`

        <!-- panels:start -->
        <!-- div:left-panel-35 -->
        ![](/.images/doc/framework/mysql/mysql-analyze-tool-02.png ':size=100%')
        <!-- div:right-panel-52 -->
        ![](/.images/doc/framework/mysql/mysql-analyze-tool-03.png ':size=100%')
        <!-- panels:end -->

    - ### mysql-utilities

        > [!NOTE][开源仓库](https://github.com/mysql/mysql-utilities)
        <br><br>环境搭建，项目语法采用的是python2的，所以需要先安装 [python2](https://www.python.org/ftp/python/2.7.18/python-2.7.18-macosx10.9.pkg)，[下载页面](https://www.python.org/downloads/release/python-2718/)
        <br><br>如果使用`--server`参数的话，还得安装python数据库驱动。根据`package.py`中的安装描述,需要安装`Connector/Python 1.0.9`,[官方下载页面](https://downloads.mysql.com/archives/c-python/)，但是我启动的时候会报如下错误：
        <br>ERROR: The MYSQL Connector/Python module was found but it is either not properly installed or it is an old version. MySQL Utilities requires Connector/Python version > '(1, 2, 1)'. Download and install Connector/Python from http://dev.mysql.com.
        <br><br>根据错误信息从新下载1.2.2版本：[mysql-connector-python-1.2.2.zip](https://cdn.mysql.com/archives/mysql-connector-python-1.2/mysql-connector-python-1.2.2.zip)

        ![](/.images/doc/framework/mysql/mysql-analyze-tool-04.png ':size=60%')

    - ### innodb-java-reader

        > [!NOTE][开源仓库](https://github.com/alibaba/innodb-java-reader)
        <br>通过开源innodb文件java解析框架，对数据文件`/tmp/mysql/demos/record_format_demo.ibd`进行分析。例如如下获取所有行记录。

        > [!CAUTION]1). 当前使用的版本为 [9866b27](https://github.com/alibaba/innodb-java-reader/tree/9866b27eefdc61ed6f3282a68ce1948d462d6751)
        <br>2). 自定义了一些代码，比如`GenericRecord#toString`，修改一些错误逻辑：[`TableDefUtil#handleCharset`](https://github.com/alibaba/innodb-java-reader/blob/9866b27eefdc61ed6f3282a68ce1948d462d6751/innodb-java-reader/src/main/java/com/alibaba/innodb/java/reader/schema/TableDefUtil.java#L69)方法中判断应该是`indexof > -1`,而不是`indexof > 0`，另外扩展了自定义逻辑等。

        ![](/.images/doc/framework/mysql/book/readme-innodb-java-reader-01.png ':size=60%')
    
    - ### innodb_ruby

        > [!NOTE][开源仓库](https://github.com/jeremycole/innodb_ruby)
        <br>使用innodb_ruby工具进行文件`/tmp/mysql/ibdata1`分析：比如命令: (<span style='color: blue'>需要注意: 截图中使用脚本执行的 <span style='color: #89903f'>innodb_space</span> 命令，如果是工具的话需要转换，如下</span>)
        <br>`innodb_space -s /tmp/mysql/ibdata1 -T demos/record_format_demo space-page-type-regions`
        <br>`innodb_space -s /tmp/mysql/ibdata1 -T demos/record_format_demo -p 3 page-records`

        ![](/.images/doc/framework/mysql/book/readme-book-02.png ':size=60%')

        * #### innodb_ruby通过gem在线安装

            > [!WARNING]参考[官方文档](https://github.com/jeremycole/innodb_ruby/wiki#installing-using-rubygems)安装完成后发现使用不了`innodb_space`命令。可能多个环境造成的。排查过程如下：
            <br><br>1). 确认安装：首先，再次确认innodb_ruby是否已成功安装。可以在命令行输入以下命令来查看：`gem list innodb_ruby`
            <br>2). 查找可执行文件位置：找到gem安装的innodb_space可执行文件的位置。通常，gem的可执行脚本会被安装到Ruby的bin目录下：`gem environment | grep "EXECUTABLE DIRECTORY"`
            <br>3). 添加到系统路径即可。可以写入`.bash_profile`或者临时添加命令`export PATH="/usr/local/lib/ruby/gems/3.3.0/bin:$PATH`

        * #### innodb_ruby官方数据还原方法

            > [?] 1). 从[innodb_ruby代码仓库](https://github.com/jeremycole/innodb_ruby.git)克隆项目到本地，里面包含一些样例数据，比如[compact行格式数据库](https://github.com/jeremycole/innodb_ruby/tree/master/spec/data/sakila/compact)。
            <br>2). 因为数据是mysql内部文件的形式存放的，所以我们需要用一个mysql服务器来驱动这些数据，此处使用docker容器来处理，将这些文件挂载到容器中，达到还原的效果。
            <br><br>目的：<span style="color: blue">与`innodb_space`工具分析出的结论作适当对比</span>。

            **启动服务器**：

            > [?] 在docker环境中使用如下命令启动：
            <br>`docker run -d -p 3338:3338 --rm -e MYSQL_ROOT_PASSWORD='compact4321' -v /Users/stevenobelia/Documents/project_rubymine_test/innodb_ruby/spec/data/sakila/compact:/data/mysql --name mysql-5.6.49-innodb_ruby mysql:5.6.49 --datadir=/data/mysql  --port=3338`

            > [!CAUTION] 1). <span style="color: blue">MYSQL_ROOT_PASSWORD对应的密码可以随便给一个大于6位的就行，用于客户端链接。</span>
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