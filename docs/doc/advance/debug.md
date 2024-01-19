
* ## Intro

    |index | explain|
    | -: | - |
    |-Xdebug |启用调试特性|
    |-Xrunjdwp| <sub-options> 在目标 VM 中加载 JDWP 实现。它通过传输和 JDWP 协议与独立的调试器应用程序通信。下面介绍一些特定的子选项从 Java V5 开始，您可以使用 -agentlib:jdwp 选项，而不是 -Xdebug 和 -Xrunjdwp。但如果连接到 V5 以前的 VM，只能选择 -Xdebug 和 -Xrunjdwp。|
    |-Djava.compiler=NONE| 禁止 JIT 编译器的加载|
    |transport| 传输方式，有 socket 和 shared memory 两种，我们通常使用 socket（套接字）传输，但是在 Windows 平台上也可以使用shared memory（共享内存）传输。|
    |server（y/n）| VM 是否需要作为调试服务器执行|
    |address| 调试服务器的端口号，客户端用来连接服务器的端口号|
    |suspend（y/n）| 值是 y 或者 n，若为 y，启动时候自己程序的 VM 将会暂停（挂起），直到客户端进行连接，若为 n，自己程序的 VM 不会挂起|

* ## debugger

    ?> Command line arguments for remote JVM.

    | JDK版本 | 启动追加命令[参考下图] |
    | - | - |
    | JDK9 or later | -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:8800 |
    | JDK 5-8 | -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8800 |
    | JDK 1.4.x | -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8800 |
    | JDK 1.3.x or earliar | -Xnoagent -Djava.compiler=NONE -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8800 |

    ![](/.images/doc/advance/debug/debug-01.png ':size=60%')

## Reference
* https://blog.csdn.net/ywlmsm1224811/article/details/98611454
* https://blog.csdn.net/JimFire/article/details/120174611