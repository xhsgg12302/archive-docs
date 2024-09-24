* ## Intro(TOMCAT-CORE)

    > [!] tomcat.version = 8.5.35 
    <br> [offical git repo | tag = 3.5.35](https://github.com/apache/tomcat/tree/8.5.35)  <span style='padding-left:5em' /> [learning git repo](https://github.com/12302-bak/tomcat-analysis)
    <br> [tomcat-8.5-doc](https://tomcat.apache.org/tomcat-8.5-doc/introduction.html)
    <br> [download 8.5.35](https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.35/)

    ![](/.images/doc/framework/tomcat/core/tomcat-process-01.png ':size=100%')

    ![](/.images/doc/framework/tomcat/core/tomcat-process-02.png ':size=100%')

    + ### 概念解释
    
    + ### 整体架构

        > [?] Connector（【coyote】处理socket连接，负责网络字节流）
        <br> Container（【Catalina】加载servlet处理具体请求）

        https://stackoverflow.com/questions/32985051/what-are-the-tomcat-component-what-is-catalina-and-coyote

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

    + ### JK protocol

    + ### 类加载器

    + ### MISC

        - #### SynchronizedQueue\<T\>

            > [!NOTE] 提供一个同步队列，使用数组实现，用insert,remove控制边界，可以循环使用。默认`size=128`。

        - #### SynchronizedStack\<T\>
        
        - #### SharedExecutor

            > [?] 在`server.xml`中配置的`<Executor>`标签，[引用](https://tomcat.apache.org/tomcat-8.5-doc/config/http.html#Common_Attributes) 中的 `executor`属性。
            <br>A reference to the name in an Executor element. If this attribute is set, and the named executor exists, the connector will use the executor, and all the other thread attributes will be ignored. Note that if a shared executor is not specified for a connector then the connector will use a private, internal executor to provide the thread pool.
            <br><br>解析赋值在`org.apache.catalina.startup.ConnectorCreateRule#begin`中。

        - #### InternalExecutor

            > [?] 内部线程池参数解读 ，参考 [文档的Connector标准实现](https://tomcat.apache.org/tomcat-8.5-doc/config/http.html#Standard_Implementation) 部分。
            <br>`1).` `minSpareThreads（最小备用线程数）`: The minimum number of threads always kept running. This includes both active and idle threads. If not specified, the default of <span style='color: blue;text-decoration: underline;' >10</span> is used. If an executor is associated with this connector, this attribute is ignored as the connector will execute tasks using the executor rather than an internal thread pool. Note that if an executor is configured any value set for this attribute will be recorded correctly but it will be reported (e.g. via JMX) as -1 to make clear that it is not used. 
            <br>`2).` `maxThreads`: The maximum number of request processing threads to be created by this Connector, which therefore determines the maximum number of simultaneous requests that can be handled. If not specified, this attribute is set to <span style='color: blue;text-decoration: underline;' >200</span>. If an executor is associated with this connector, this attribute is ignored as the connector will execute tasks using the executor rather than an internal thread pool. Note that if an executor is configured any value set for this attribute will be recorded correctly but it will be reported (e.g. via JMX) as -1 to make clear that it is not used.

            > [!] 外部控制值的配置：`<Connector port="8080" protocol="HTTP/1.1" executor="tomcatThreadPool123"  minSpareThreads="12"`
            <br> 大致原理就是通过自省的`IntrospectionUtils`工具进行赋值: `Connector#setProperty() --> AbstractProtocol#setMinSpareThreads() --> endpoint#setMinSpareThreads()`;

            ```java
            public void createExecutor() {
                internalExecutor = true;
                TaskQueue taskqueue = new TaskQueue();
                TaskThreadFactory tf = new TaskThreadFactory(getName() + "-exec-", daemon, getThreadPriority());
                executor = new ThreadPoolExecutor(getMinSpareThreads(), getMaxThreads(), 60, TimeUnit.SECONDS,taskqueue, tf);
                taskqueue.setParent( (ThreadPoolExecutor) executor);
            }

            public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue, ThreadFactory threadFactory) {
                super(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue, threadFactory, new RejectHandler());
                prestartAllCoreThreads();
            }
            ```

            <!-- panels:start -->
            <!-- div:left-panel-50 -->
            ```java
            // 最小备用线程数
            private int minSpareThreads = 10;
            public void setMinSpareThreads(int minSpareThreads) {
                this.minSpareThreads = minSpareThreads;
                Executor executor = this.executor;
                if (internalExecutor && executor instanceof java.util.concurrent.ThreadPoolExecutor) {
                    // The internal executor should always be an instance of
                    // j.u.c.ThreadPoolExecutor but it may be null if the endpoint is
                    // not running.
                    // This check also avoids various threading issues.
                    ((java.util.concurrent.ThreadPoolExecutor) executor).setCorePoolSize(minSpareThreads);
                }
            }
            public int getMinSpareThreads() {
                return Math.min(getMinSpareThreadsInternal(), getMaxThreads());
            }
            private int getMinSpareThreadsInternal() {
                if (internalExecutor) {
                    return minSpareThreads;
                } else {
                    return -1;
                }
            }
            ```
            <!-- div:right-panel-50 -->
            ```java
            /**
             * 
             * Maximum amount of worker threads.
             */
            private int maxThreads = 200;
            public void setMaxThreads(int maxThreads) {
                this.maxThreads = maxThreads;
                Executor executor = this.executor;
                if (internalExecutor && executor instanceof java.util.concurrent.ThreadPoolExecutor) {
                    // The internal executor should always be an instance of
                    // j.u.c.ThreadPoolExecutor but it may be null if the endpoint is
                    // not running.
                    // This check also avoids various threading issues.
                    ((java.util.concurrent.ThreadPoolExecutor) executor).setMaximumPoolSize(maxThreads);
                }
            }
            public int getMaxThreads() {
                if (internalExecutor) {
                    return maxThreads;
                } else {
                    return -1;
                }
            }
            ```
            <!-- panels:end -->

        - #### TaskQueue_任务队列

            ```java
            @Override
            public boolean offer(Runnable o) {
            //we can't do any checks
                if (parent==null) return super.offer(o);
                //we are maxed out on threads, simply queue the object
                if (parent.getPoolSize() == parent.getMaximumPoolSize()) return super.offer(o);
                //we have idle threads, just add it to the queue
                if (parent.getSubmittedCount()<=(parent.getPoolSize())) return super.offer(o);
                //if we have less threads than maximum force creation of a new thread
                if (parent.getPoolSize()<parent.getMaximumPoolSize()) return false;
                //if we reached here, we need to add it to the queue
                return super.offer(o);
            }
            ```
        - #### getHandler()

            ```java
            public AbstractHttp11Protocol(AbstractEndpoint<S> endpoint) {
                super(endpoint);
                setConnectionTimeout(Constants.DEFAULT_CONNECTION_TIMEOUT);
                ConnectionHandler<S> cHandler = new ConnectionHandler<>(this);
                setHandler(cHandler);
                getEndpoint().setHandler(cHandler);
            }
            ```

* ## Reference

    + https://github.com/apache/tomcat/tree/8.5.35
    + https://github.com/12302-bak/tomcat-analysis
    + https://tomcat.apache.org/tomcat-8.5-doc/introduction.html
    + https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.35/
    + https://tomcat.apache.org/connectors-doc/ajp/ajpv13a.html <span style='padding-left: 2em'>【AJP protocol introduce】</span>  <span style='padding-left: 2em'>[【AJP protocol CVE】](https://yq1ng.github.io/2021/05/19/cve-2020-1938-you-ling-mao-ghostcat-tomcat-ajp-xie-yi-ren-yi-wen-jian-du-qu-jsp-wen-jian-bao-han-lou-dong-fen-xi/)</span>
    + 
    + https://www.bilibili.com/video/BV1dJ411N7Um
    + https://segmentfault.com/a/1190000023475177
    + 
    + tomcat启动类中使用反射调用的意义
    + https://stackoverflow.com/questions/7672466/why-does-tomcat-use-reflection-for-catalina-instancilization
    + 
    + 以下是源码调试环境搭建
    + https://www.bilibili.com/video/BV1dJ411N7Um?p=5
    + https://www.cnblogs.com/kukuxjx/p/17701288.html
    + https://www.cnblogs.com/grasp/p/10061577.html
    + https://www.cnblogs.com/LiPengFeiii/p/15228789.html
    + https://blog.csdn.net/xuan_lu/article/details/106314487