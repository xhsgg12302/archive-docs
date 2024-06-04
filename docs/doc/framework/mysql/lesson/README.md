* ## Intro(Lesson)

    + ### 练习环境准备

        <!-- panels:start -->
        <!-- div:left-panel-55 -->
        > [?] 1). 启动之前准备好的数据卷
        <br>`docker run -dit --rm --name mysql-data registry.cn-hangzhou.aliyuncs.com/eli_w/busybox-with-mysql-datadir:1.0.0`
        <br><br>2). 挂载数据卷并启动服务器
        <br>`docker run -d -p 3336:3336 -e MYSQL_ROOT_PASSWORD='******' --name mysql-5.6.49-lesson --volumes-from mysql-data  mysql:5.6.49  --datadir=/mysql-data/mysql  --port=3336`
        <br><br>3). 使用客户端连接测试
        <br>`docker run -it --rm --name mysql-lesson-client --network=host -e LANG="C.UTF-8" mysql:5.6.49 mysql  -h 127.0.0.1 -u root -P 3336 -p`
        <!-- div:right-panel-45 -->
        ![](/.images/doc/framework/mysql/lesson/readme-lesson-01.png ':size=96%')
        <!-- panels:end -->

* ## Reference
    + https://www.bilibili.com/video/BV12b411K7Zu