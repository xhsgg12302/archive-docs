* ## Intro(TOMCAT)

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
        <br><br>使用命令启动 `export JPDA_ADDRESS="12302";export JPDA_SUSPEND=y;bin/catalina.sh jpda run`。<span style='color: #eb04f0'>【此处用`run`而非~`start`~, 使用start会在后台运行，debug会卡住，原因未知。】</span>
        <br>参考 [IDEA DEBUG](../../advance/debug.md) 在 IDEA 端设置后连接。

        ![](/.images/doc/framework/tomcat/tomcat-readme-02.gif ':size=100%')

* ## Reference