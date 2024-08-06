* ## Intro(TOMCAT)

    > [!] tomcat.version = 8.5.35 
    <br> [offical git repo | tag = 3.5.35](https://github.com/apache/tomcat/tree/8.5.35)  <span style='padding-left:5em' /> [learning git repo](https://github.com/12302-bak/tomcat-analysis)
    <br> [tomcat-8.5-doc](https://tomcat.apache.org/tomcat-8.5-doc/introduction.html)
    <br> [download 8.5.35](https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.35/)

    + ### 整体架构

        > [?] Connector（【coyote】处理socket连接，负责网络字节流）
        <br> Container（【Catalina】加载servlet处理具体请求）
    
    + ### 启动流程

        > [?] 先是初始化
        <br>BootStrap->Calaline->Server->Service
        <br>BootStrap->Calaline->Server->Service->[Engine->Host->Context]
        <br>BootStrap->Calaline->Server->Service->[Executor]
        <br>BootStrap->Calaline->Server->Service->[ProtocalHandler]
        <br>然后调用start启动整个tomcat

    + ### I/O模型和协议

        > [?] BIO，NIO，AIO（NIO2），APR
        <br>HTTP/1.1，HTTP/2，AJP（用于和apache服务器集成用来处理静态资源）

    + ### Jasper引擎

        > [?] index.jsp -> jspServlet(找到文件) -> 渲染成servlet.class-> 加载调用

    + ### Context
    
        > [?] 官方不建议直接在server.xml中配置，因为，server.xml文件只会在tomcat重新的时候重新加载。

        1. In an individual file at /META-INF/context.xml inside the application files
        2. In individual files (with a ".xml" extension) in the $CATALINA_BASE/conf/[enginename]/[hostname]/ directory
        3. Inside a Host element in the main conf/server.xml.

    + ### 安全

        > [?] `1).` 删除webapp下面的管理页面和其他项目
        <br>`2).` 禁用server-user.xml里面的权限或者直接删除
        <br>`3).` 修改tomcat默认关机指令，或者禁用
        <br>`4).` 配置错误页面。

* ## Reference