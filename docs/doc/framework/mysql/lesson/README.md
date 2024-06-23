* ## Intro(Lesson)

    + ### 目录结构

        1. [数据库相关概念](./01_database_related_concepts.md)
        2. [帮助函数](./02_function.md)
        3. [DQL-数据查询语言](./03_dql.md)
        4. [DML-数据操纵语言](./04_dml.md)
        5. [DDL-数据定义语言](./05_ddl.md)
        6. [TCL-事务控制语言](./06_tcl.md)
        7. [视图](./07_view.md)
        8. [变量](./08_variables.md)
        9. [存储过程和函数](./09_procedure_function.md)
        10. [流程控制](./10_flow_control.md)

    + ### 练习环境准备

        <!-- panels:start -->
        <!-- div:left-panel-55 -->
        > [?] 1). 启动之前准备好的数据卷
        <br>`docker run -dit --rm --name mysql-data registry.cn-hangzhou.aliyuncs.com/eli_w/busybox-with-mysql-datadir:1.2.0`
        <br><br>2). 挂载数据卷并启动服务器
        <br>`docker run -d -p 3336:3336 -e MYSQL_ROOT_PASSWORD='******' --name mysql-5.6.49-lesson --volumes-from mysql-data  mysql:5.6.49  --datadir=/mysql-data/mysql  --port=3336`
        <br><br>3). 使用客户端连接测试
        <br>`docker run -it --rm --name mysql-lesson-client --network=host -e LANG="C.UTF-8" mysql:5.6.49 mysql  -h 127.0.0.1 -u root -P 3336 -p`
        <!-- div:right-panel-45 -->
        ![](/.images/doc/framework/mysql/lesson/readme-lesson-01.png ':size=96%')
        <!-- panels:end -->

        <details><summary>lesson.sql</summary>

        [lesson.sql](./lesson.sql ':include :type=code')
        </detail>

    + ### 语法规范

        > [!NOTE]
        <br>`1). ` 不区分大小写，但建议<span style='color: blue'>关键字大写，表名，列名小写</span>
        <br>`2). ` 每条命令最好用分号结尾
        <br>`3). ` 每条命令可以根据需要进行`缩进或者换行`
        <br>`4). ` 注释：单行注释`# 注释文字`，单行注释`-- 注释文字`，多行注释`/* 注释文字 */`

    + ### 数据库ER图

         <!-- panels:start -->
        <!-- div:left-panel-78 -->
        ![](/.images/doc/framework/mysql/lesson/readme-lesson-02.png ':size=100%')
        <!-- div:right-panel-22 -->
        ![](/.images/doc/framework/mysql/lesson/readme-lesson-03.png ':size=98%')
        <!-- panels:end -->

* ## Reference
    + https://www.bilibili.com/video/BV12b411K7Zu