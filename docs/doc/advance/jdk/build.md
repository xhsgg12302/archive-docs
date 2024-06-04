## 编译JDK

* ### build

    ```shell
    # 下载xcodeapp[(~7G), OS version [13.3.1 (22E261)]
    wget -c https://download.developer.apple.com/Developer_Tools/Xcode_14.3.1/Xcode_14.3.1.xip
    # 切换xcode 到xcodeapp
    sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
    sudo xcodebuild -license

    # 配置
    # disable-warnings-as-errors选项是禁止把warning 当成error
    # --with-debug-level=slowdebug。用来设置编译的级别，可选值为release、fastdebug、slowde-bug，越往后进行的优化措施就越少，带的调试信息就越多。默认值为release。slowdebug 含有最丰富的调试信息，没有这些信息，很多执行可能被优化掉，我们单步执行时，可能看不到一些变量的值。所以最好指定slowdebug 为编译级别。
    # with-jvm-variants 编译特定模式的HotSpot虚拟机，可选值：server、client、minimal、core、zero、custom
    # configure 命令承担了依赖项检查、参数配置和构建输出目录结构等多项职责，如果编译过程中需要的工具链或者依赖项有缺失，命令执行后会得到明确的提示，并给出该依赖的安装命令。
    bash configure --disable-warnings-as-errors --with-debug-level=slowdebug --with-jvm-variants=server
    # 编译
    make images

    # 生成compile_commands.json文件
    make compile-commands
    ```

* ### loaded to clion

    ![](/.images/doc/advance/jdk/jdk-build-01.png ':size=45%')
    ![](/.images/doc/advance/jdk/jdk-build-04.png ':size=54%')
    ![](/.images/doc/advance/jdk/jdk-build-02.png ':size=40%')
    ![](/.images/doc/advance/jdk/jdk-build-03.png ':size=40%')

* ### debug

    > [?] 使用lldb debug的时候会出现 Signal: SIGSEGV (signal SIGSEGV) ,`目前切换到gdb断点不生效，未解决。`
    <br> 可以[参考](https://lrting.top/backend/11766/)添加下列代码解决。如果是gdb的话，[参考](https://jiawanggjia.github.io/post/openjdk-bian-yi-zhi-nan/)

    <!-- tabs:start -->
    #### **lldb**
    ```shell
    # ～/.lldbinit 文件内容
    # break set -n main -C "process handle --pass true --stop false SIGSEGV" -C "process handle --pass true --stop false SIGBUS"
    br set -n main -o true -G true -C "pro hand -p true -s false SIGSEGV SIGBUS"
    ```
    #### **gdb**
    ```shell
    # ~/.gdbinit
    handle SIGSEGV pass noprint nostop
    handle SIGBUS pass noprint nostop
    ```
    <!-- tabs:end -->
    ![](/.images/doc/advance/jdk/jdk-build-05.png ':size=45%')
    ![](/.images/doc/advance/jdk/jdk-build-06.png ':size=45%')


* ### debug with java

    > [!] 保证两边代码一致，如果有修改变动，记得重新编译代码。
    <br>可以使用clion自带的功能，在运行之前进行处理。before launch

    ```java
    //测试java代码
    package org.example;

    public class Main {
        public static void main(String[] args) {
            for (int i = 0; i < 5; i++) {
                System.out.println("Hello world!");
            }
            Main main = new Main();
            String name = main.getClass().getName();
            System.out.println(name);
        }
    }
    ```

    ```shell
    # idea 配置
    Remote JVM Debug: localhost 13328
    # clion 配置
    Program arguments: -agentlib:jdwp=transport=dt_socket,server=n,address=localhost:13328,suspend=y org.example.Main
    Working directory: /Users/stevenobelia/Documents/project_idea_test/test-jdk/target/classes
    ```
    ![](/.images/doc/advance/jdk/jdk-build-08.png ':size=45%')
    ![](/.images/doc/advance/jdk/jdk-build-09.png ':size=54%')
    ![](/.images/doc/advance/jdk/jdk-build-07.png ':size=70%')


## Reference
* https://github.com/openjdk/jdk/blob/master/doc/building.md
* https://segmentfault.com/a/1190000040305260
* https://www.jetbrains.com/help/clion/compilation-database.html
* https://segmentfault.com/a/1190000041074433?utm_source=sf-backlinks
* https://blog.csdn.net/qq_25825005/article/details/127163309
* https://hg.openjdk.org/jdk8u/jdk8u/raw-file/tip/README-builds.html