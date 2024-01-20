
* ## debugger

    + ### 选项参数解释

        |index | explain|
        | -: | - |
        |-Xdebug |启用调试特性|
        |-Xrunjdwp| <sub-options> 在目标 VM 中加载 JDWP 实现。它通过传输和 JDWP 协议与独立的调试器应用程序通信。下面介绍一些特定的子选项从 Java V5 开始，您可以使用 -agentlib:jdwp 选项，而不是 -Xdebug 和 -Xrunjdwp。但如果连接到 V5 以前的 VM，只能选择 -Xdebug 和 -Xrunjdwp。|
        |-Djava.compiler=NONE| 禁止 JIT 编译器的加载|
        |transport| 传输方式，有 socket 和 shared memory 两种，我们通常使用 socket（套接字）传输，但是在 Windows 平台上也可以使用shared memory（共享内存）传输。|
        |server（y/n）| VM 是否需要作为调试服务器执行|
        |address| 调试服务器的端口号，客户端用来连接服务器的端口号|
        |suspend（y/n）| 值是 y 或者 n，若为 y，启动时候自己程序的 VM 将会暂停（挂起），直到客户端进行连接，若为 n，自己程序的 VM 不会挂起|
    
    + ### 样例

        ?> Command line arguments for remote JVM.

        | JDK版本 | (attach mode)启动追加命令[参考下图] |
        | - | - |
        | JDK9 or later | -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:8800 |
        | JDK 5-8 | -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8800 |
        | JDK 1.4.x | -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8800 |
        | JDK 1.3.x or earliar | -Xnoagent -Djava.compiler=NONE -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8800 |

        ![](/.images/doc/advance/debug/debug-01.png ':size=60%')

* ## mode

    ?> debug mode:
    <br>`Attach`: 此种模式下，server=y,address=port。被调试的代码端充当服务器开放端口等待`jdi客户端`去连接。
    <br>`Listen`: 此种模式下，server=n,address=ip:port。应当先在ip机器上启动`jdi客户端`去监听端口port，然后启动需要被调试的代码。

    !> 需要注意的是：suspend=y/n. 指定是否被调试JVM需要被挂起。
    <br>&nbsp;&nbsp;&nbsp;attach模式下： 如果调试简单application，则最好是`suspend=y`,这样的话不至于还没attach上targetVM,程序已经运行完了。如果是web应用，因为一直在运行。所以`suspend=n`也无妨。后续只要attach上targetVM就可以对targetVM进行控制。
    <br>&nbsp;&nbsp;&nbsp;listen模式下： 感觉没有什么卵用。启动targetVM 的时候就要与指定ip:port的`jdi客户端`进行连接。比如：idea 设置远程debug 为listen模式。先启动监听本地localhost:8800.顺便打上断点。然后再启动需要被调试的JVM，启动的时候不管`suspend=y/n`,都会步入断点。

    + ### attach

        - **targetVM**

            ?> `java -agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=8800 _draft.test.debug.HelloWorld` 运行后会暂停，等待jdi客户端attach后才开始执行。

        - **JDI client**

            ![](/.images/doc/advance/debug/debug-02.png ':size=80%')

    + ### listen

        - **targetVM**

            ?> `java -agentlib:jdwp=transport=dt_socket,server=n,suspend=n,address=localhost:8800 _draft.test.debug.HelloWorld` 等待下图jdi客户端监听开始之后再运行此命令。

        - **JDI client**

            ![](/.images/doc/advance/debug/debug-03.png ':size=80%')

        - **IDEA本地debug按钮(采用listen方式debug)**

            ```shell
            # 启动命令及参数
            /Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/bin/java 
                -agentlib:jdwp=transport=dt_socket,address=127.0.0.1:57982,suspend=y,server=n 
                -javaagent:/Users/stevenobelia/Library/Caches/JetBrains/IntelliJIdea2023.2/captureAgent/debugger-agent.jar 
                -Dfile.encoding=UTF-8 
                -classpath /Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/charsets.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/lib/tools.jar:/Applications/IntelliJ IDEA.app/Contents/lib/idea_rt.jar 
                _draft.test.debug.HelloWorld
            Connected to the target VM, address: '127.0.0.1:57982', transport: 'socket'
            ```

## Reference
* https://blog.csdn.net/ywlmsm1224811/article/details/98611454
* https://blog.csdn.net/JimFire/article/details/120174611
* 
* https://docs.oracle.com/javase/7/docs/technotes/guides/jpda/jdwp-spec.html
* https://stackoverflow.com/questions/138511/what-are-java-command-line-options-to-set-to-allow-jvm-to-be-remotely-debugged