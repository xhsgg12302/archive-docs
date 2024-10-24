* ## Intro(TOMCAT)

    > [!] 正常启动参数
    <br>`/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/bin/java`
    <br><span style='padding-left: 2em'/>`-Djava.util.logging.config.file=/Users/stevenobelia/software/apache-tomcat-8.5.53/conf/logging.properties`
    <br><span style='padding-left: 2em'/>`-Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager `
    <br><span style='padding-left: 2em'/>`-Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources `
    <br><span style='padding-left: 2em'/>`-Dorg.apache.catalina.security.SecurityListener.UMASK=0027 `
    <br><span style='padding-left: 2em'/>`-agentlib:jdwp=transport=dt_socket,address=12302,server=y,suspend=y `
    <br><span style='padding-left: 2em'/>`-Dignore.endorsed.dirs= `
    <br><span style='padding-left: 2em'/>`-classpath /Users/stevenobelia/software/apache-tomcat-8.5.53/bin/bootstrap.jar:/Users/stevenobelia/software/apache-tomcat-8.5.53/bin/tomcat-juli.jar `
    <br><span style='padding-left: 2em'/>`-Dcatalina.base=/Users/stevenobelia/software/apache-tomcat-8.5.53 `
    <br><span style='padding-left: 2em'/>`-Dcatalina.home=/Users/stevenobelia/software/apache-tomcat-8.5.53 `
    <br><span style='padding-left: 2em'/>`-Djava.io.tmpdir=/Users/stevenobelia/software/apache-tomcat-8.5.53/temp `
    <br><span style='padding-left: 2em'/>`org.apache.catalina.startup.Bootstrap start`
    
    + ### CATALINA_BASE VS CATALINA_HOME

        > [!TIP] 参考 [CATALINA_HOME_and_CATALINA_BASE](https://tomcat.apache.org/tomcat-8.5-doc/introduction.html#CATALINA_HOME_and_CATALINA_BASE) 、[tomcat-catalina-base-and-catalina-home-variables](https://stackoverflow.com/questions/3090398/tomcat-catalina-base-and-catalina-home-variables)
        <br><br>CATALINA_HOME：表示 tomcat 安装根目录，（可以理解为启动程序，主要包含一些二进制.jar文件和执行脚本）
        <br>CATALINA_BASE：表示 tomcat 实例的运行配置根目录，（主要是配置，日志，部署的应用，其他运行依赖等，并且优先级比**CATALINA_HOME**高）
        <br><br>为什么需要有**CATALINA_BASE**目录。默认情况下，两个指向相同的目录。如果需要运行多个tomcat实例的话需要手动指定**CATALINA_BASE**目录。这样做的好处有以下几点(都是在运行多实例的情况下)：
        <br>`1).` 对于管理tomcat版本升级来说更为简单，咱们只需要替换tomcat运行依赖即可，即**CATALINA_HOME**中的jar包
        <br>`2).` 避免复制tomcat运行依赖（节省磁盘空间）
        <br>`3).` 共享某些设置，比如`setenv.sh`。
        <br><br><span style='color:blue'>需要注意：**CATALINA_BASE**目录至少包含`conf/server.xml` 、`conf/web.xml`两个文件。</span>

    + ### Context配置

        > [!TIP] 参考 [A word on Contexts](https://tomcat.apache.org/tomcat-8.5-doc/deployer-howto.html#A_word_on_Contexts)、[Defining a context](https://tomcat.apache.org/tomcat-8.5-doc/config/context.html#Defining_a_context)
        <br><br>`Context`表示一个**tomcat web application**。为了在 tomcat 中配置一个 Context ,需要一个叫作**Context Descriptor**的东西。这个是一个包含Context配置相关的xml文件。
        <br><br>每个应用单独的Context
        <br>`1).` `$CATALINA_BASE/conf/[enginename]/[hostname]/[webappname].xml`<span style='padding-left:2em'/>【idea中tomcat使用这种方式:如下图】
        <br>`2).` `$CATALINA_BASE/webapps/[webappname]/META-INF/context.xml`
        <br>`3).` `Inside a Host element in the main conf/server.xml.`
        <br><br>默认(共享)的Context
        <br>`1).` `$CATALINA_BASE/conf/context.xml`，所有的web应用都会加载。
        <br>`2).` `$CATALINA_BASE/conf/[enginename]/[hostname]/context.xml.default`，每一个虚拟主机都会加载。
        <br><br>在早期的版本中，这些配置被放在`server.xml`中。现在虽不鼓励这么做，但是也支持。因为对Context做修改的话，如果不重启tomcat的话，`server.xml`是不会被重载的。这会表现出较强的侵入性。所以默认的Context配置会复写任何在`server.xml`中定义的`<Context>`元素。可以使用`override=true`属性来阻止这种行为。

        - #### IDEA中配置Context的方式

            ![](/.images/doc/framework/tomcat/tomcat-readme-03.png ':size=100%')

    + ### BIN目录介绍

        ```
        bin
        ├── bootstrap.jar
        ├── catalina.sh
        ├── ciphers.sh
        ├── commons-daemon.jar
        ├── configtest.sh
        ├── daemon.sh
        ├── digest.sh
        ├── setclasspath.sh
        ├── shutdown.sh
        ├── startup.sh
        ├── tomcat-juli.jar
        ├── tool-wrapper.sh
        └── version.sh
        └── ...
        ```

        > [?] apache-tomcat-8.5.35/bin 目录结构如上图所示，摘取部分主要的。进行分析: 最重要的是`catalina.sh`。
        <br><br>首先是`version.sh`，获取当前脚本`version.sh`的所在目录。然后执行同级目录下面的`catalina.sh` 效果等同于`catalina.sh version "$@"`。
        <br>其次是`startup.sh`，这个原理等同于上一个。相当于执行`catalina start "$@"`。
        <br>然后是`setclasspath.sh`有必要提一下，这个主要用来确保**JAVA_HOME/JRE_HOME**，
        <br><span style='padding-left:3em'/>以及检测第一个如果为`debug`的话要保证 JAVA_HOME/bin目录下存在`jdb`，用来调试tomcat程序。以及定义变量`_RUNJAVA`、`_RUNJDB`。
        <br><span style='padding-left:3em'/>这个文件的使用方式是在`catalina.sh`中使用`.`dot命令在当前shell中执行内容。
        <br><br>最后一个`catalina.sh`，使用方法如下所示:
        <br>![](/.images/doc/framework/tomcat/tomcat-readme-01.png ':size=90%')

    + ### 调试本地或远程的Tomcat

        > [?] 因为需要比较 源码运行，与直接本地启动tomcat后里面运行参数的异同，所以需要搭建debug环境。参考文章如下：
        <br>https://cwiki.apache.org/confluence/display/TOMCAT/Developing
        <br>https://stackoverflow.com/questions/16689274/how-to-start-debug-mode-from-command-prompt-for-apache-tomcat-server
        <br><br> 如`catalina.sh`的 Usage 中所述，可以使用 `catalina.sh debug` 命令，但是这个是使用`jdb`程序启动的，不太会用。
        <br>所以选用另外一种使用`catalina.sh jpda start`，这个使用 [jdwp(Java Debug Wire Protocol)](https://docs.oracle.com/javase/7/docs/technotes/guides/jpda/jdwp-spec.html) 协议，方便与现有ide结合使用 *Attach模式* 调试，比如 **IJ idea**。
        <br><br>使用命令启动 `export JPDA_ADDRESS="12302";export JPDA_SUSPEND=y;bin/catalina.sh jpda run`。<span style='color: #eb04f0'>【此处用`run`而非~`start`~：不会再另一个shell中运行程序，而在当前窗口，方便停止与日志查看】</span>
        <br>参考 [IDEA DEBUG](../../advance/debug.md) 在 IDEA 端设置后连接。

        ![](/.images/doc/framework/tomcat/tomcat-readme-02.gif ':size=100%')

* ## Reference

    + https://tomcat.apache.org/tomcat-8.5-doc/index.html
    + https://stackoverflow.com/questions/3090398/tomcat-catalina-base-and-catalina-home-variables