* ## Intro(MySql)

    + ### innodb_ruby数据还原

        ?> 1). 从[innodb_ruby代码仓库](https://github.com/jeremycole/innodb_ruby.git)克隆项目到本地，里面包含一些样例数据，比如[compact行格式数据库](https://github.com/jeremycole/innodb_ruby/tree/master/spec/data/sakila/compact)。
        <br>2). 因为数据是mysql内部文件的形式存放的，所以我们需要用一个mysql服务器来驱动这些数据，此处使用docker容器来处理，将这些文件挂载到容器中，达到还原的效果。
        <br><br>目的：<span style="color: blue">与`innodb_space`工具分析出的结论作适当对比</span>。

        - #### 启动数据服务器

            ?> 在docker环境中使用如下命令启动：
            <br>`docker run -d -p 3338:3338 --rm -e MYSQL_ROOT_PASSWORD='compact4321' -v /Users/stevenobelia/Documents/project_rubymine_test/innodb_ruby/spec/data/sakila/compact:/data/mysql --name mysql-5.6.49-innodb_ruby mysql:5.6.49 --datadir=/data/mysql  --port=3338`
            <br><br>可以使用下列命令进行客户端连接测试：
            <br>`docker run -it --rm --network=host --name mysql-client -e LANG="C.UTF-8" mysql:5.6.49 mysql  -h 127.0.0.1 -u root -P 3338 -p`
            <br>输入如上`compact4321`密码登入。

            > [!ATTENTION] 1). <span style="color: blue">MYSQL_ROOT_PASSWORD对应的密码可以随便给一个大于6位的就行，用于客户端链接。</span>
            <br>2). `/Users/stevenobelia/Documents/project_rubymine_test/innodb_ruby/spec/data/sakila/compact`指的是克隆下来项目中的数据路径，也就是 mysql 的 datadir

            <!-- panels:start -->
            <!-- div:left-panel-50 -->
            ##### mysql服务器
            ![](/.images/doc/framework/mysql/book/readme-innodb-ruby-01.png ':size=100%')
            <!-- div:right-panel-50 -->
            ##### mysql客户端
            ![](/.images/doc/framework/mysql/book/readme-innodb-ruby-02.png ':size=100%')
            <!-- panels:end -->

        - #### 验证record_dump

            ?> 按照wiki中的[record_dump](https://github.com/jeremycole/innodb_ruby/wiki#record-dump) 命令`innodb_space -s ibdata1 -T sakila/film -p 7 -R 128 record-dump` dump 出的结果如下左，对比查询出的数据记录如下右。

            <!-- panels:start -->
            <!-- div:left-panel-45 -->
            ![](/.images/doc/framework/mysql/book/readme-innodb-ruby-03.png ':size=100%')
            <!-- div:right-panel-55 -->
            ![](/.images/doc/framework/mysql/book/readme-innodb-ruby-04.png ':size=92%')
            <!-- panels:end -->

        - #### Reference
            + https://github.com/jeremycole/innodb_ruby/wiki

* ## Reference
    + [MySQL是怎样运行的：从根儿上理解MySQL.pdf]()