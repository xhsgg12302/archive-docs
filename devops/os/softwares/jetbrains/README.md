* ## Intro(Jetbrains)

    ### Jetbrains系列禁用macOS触控栏
    <!-- panels:start -->
    <!-- div:left-panel-65 -->
    > [?] 由于在 debug 的过程中需要用到 Fn 系列功能，所以不要让软件自定义 touch bar 相关内容。
    <br><br>解决办法（局部软件级别）：Help -> Editor Custom VM Options -> `-Dide.mac.touchbar.use=false`，[参考 youtrack|Disable-touchbar-support](https://youtrack.jetbrains.com/issue/IJPL-53320/Disable-touchbar-support)。
    <br>当然还有另外一种方式，全局系统级别的禁用：系统设置 -> 键盘 -> 触控栏设置 -> 触控栏显示切换到其他即可。
    <br><br>上述局部方式好像啥也不显示了，要想显示 `Show F1, F2, etc. keys on the Touch Bar`， [参考 Show F1, F2, etc](https://www.jetbrains.com/help/idea/touch-bar-support.html)
    <!-- div:right-panel-33 -->
    ![](/.images/devops/os/softwares/jetbrains-show-fn-01.png ':size=70%')
    <!-- panels:end -->

    ### WebStorm文件内容不能预览(打不开文件)
    
    > [?] 可能原因是新版本使用了老版本的插件，不兼容造成的原因。解决办法：`删除对应版本WebStorm中的plugins目录`。[参考](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360010609860-Intellij-cannot-view-any-file-content) 删除了额外的插件 [目录](https://intellij-support.jetbrains.com/hc/en-us/articles/206544519-Directories-used-by-the-IDE-to-store-settings-caches-plugins-and-logs)

    <!-- panels:start -->
    <!-- div:left-panel-40 -->
    ![](/.images/devops/os/softwares/jetbrains-readme-01.png ':size=100%')
    <!-- div:right-panel-60 -->
    ![](/.images/devops/os/softwares/jetbrains-readme-02.png ':size=100%')
    <!-- panels:end -->

    ### Idea调试异常信息显示(Unknown Source)

    > [?] 可能原因是当前使用的jar包在编译的时候没有加入调试信息，比如(`LineNumberTable`, [`LocalVariableTable`](/docs/doc/advance/javastrace.md#localvariabletable), etc. )
    <br> [参考1：Why do I see unknown source in stack traces when I have sources downloaded?](https://stackoverflow.com/questions/66565398/why-do-i-see-unknown-source-in-stack-traces-when-i-have-sources-downloaded/66565466)
    <br> [参考2：bouncy castle: How to get bcpkix-jdk15on-1.47.jar with debug information](https://stackoverflow.com/questions/12894129/bouncy-castle-how-to-get-bcpkix-jdk15on-1-47-jar-with-debug-information) <span style="padding-left:100px">[解决方案:version[-debug]](https://github.com/bcgit/bc-java/issues/1450)</span>

    <!-- panels:start -->
    <!-- div:left-panel-60 -->
    ![](/.images/devops/os/softwares/idea-stacktrace-unknown-source-01.png ':size=100%')
    <!-- div:right-panel-40 -->
    ![](/.images/devops/os/softwares/idea-stacktrace-unknown-source-02.png ':size=95%')
    <!-- panels:end -->

    ### Idea设置debug面板等宽字体

    > [?] 1). 因为需要对比debug内容，尤其是字节数组，所以设置[`JetBrains Mono`](https://www.jetbrains.com/zh-cn/lp/mono/)等宽字体。
    <br>2). 设置debug面板[\[参考\]](https://intellij-support.jetbrains.com/hc/en-us/community/posts/115000113064-How-to-change-debugger-font-size)，通过全局来控制或影响`Settings | Appearance & Behavior | Appearance | Use custom font`。Editor或者console面板可以通过`Settings | Editor | Font`，`Settings | Editor | Color Scheme | Console Font`单独设置.

    ![](/.images/devops/os/softwares/idea-display-width-font-01.png ':size=100%')

    ### Idea不能读取系统环境变量

    > [!] 原先因为调试IDEA启不动问题，手贱将idea启动目录下的一个C`/Applications/IntelliJ IDEA.app/Contents/bin/printenv`程序因为打印格式问题替换成了`/usr/bin/printenv`（辛好有备份:stuck_out_tongue_closed_eyes:），导致IDEA读取不到系统设置的环境变量。效果类似如下图所示。但是好在有大佬们的文章[Env variables not sourced when launched from Mac OS X Monterey Dock](https://issuetracker.google.com/issues/216364005#comment7)可以借鉴，最终成功解决。

    ![](/.images/devops/os/softwares/idea-not-read-env-01.png ':size=49%')
    ![](/.images/devops/os/softwares/idea-not-read-env-02.png ':size=49%')

    ### Idea编码设置

    > [!TIP] 默认情况下：使用idea创建的properties文件是`ISO 8859-1`编码格式（如果没有专门设置过的话）。
    <br>在使用中文的情况下，错误字符下面会有黄线警告，如果保存文件，使用其他十六进制查看的话，会是一个`?` **[0x3F]** 乱码。
    <br>原因如下：使用`ISO 8859-1`单字节编码保存了中文(多字节)，不认识，所以插入`?`，以表尊重。
    <br><br>还有另外需要注意的点：<span style='color:blue'>(java resource bundle，用于国际化的**i18n**)</span>
    <br>不论文件的编码方式是`ISO 8859-1`还是`utf-8`，文件内容必须是 unicode 或者 ascii ，例如`hello=\u9ed8\u8ba4\u5185\u5bb9`. 参考：[Resource bundles must be created in ASCII, or use Unicode escape codes to represent Unicode characters. Since you don't compile a property file to byte code, there is no way to tell the JVM which character set to use](https://docs.jboss.org/seam/2.2.1.CR3/reference/en-US/html/i18n.html#d0e14039)
    <br><br>如果需要编辑，可读，可以通过idea自带的`Transparent native-to-ascii conversion`功能将unicode展示成可读文字（GBK|UTF-8）
    <br>如果已经存在可读文字（GBK|UTF-8），需要将其转化成unicode，可以使用JAVA_HOME/bin中的工具`native2ascii`，
    <br>使用命令`native2ascii -encoding UTF-8 messages_cs.properties messages_cs_escaped.properties`进行转换。

    > [!CAUTION] UTF-8 is a multibyte encoding that can represent any Unicode character. ISO 8859-1 is a single-byte encoding that can represent the first 256 Unicode characters. Both encode ASCII exactly the same way. [参考](https://stackoverflow.com/questions/7048745/what-is-the-difference-between-utf-8-and-iso-8859-1/7048774#7048774)
    <br> first 256 unicode char [参考](https://www.utf8-chartable.de/) 、ISO 8859-1 编码表 [参考](https://en.wikipedia.org/wiki/ISO/IEC_8859-1#Code_page_layout)

    ![](/.images/devops/os/softwares/idea-encoding-01.png ':size=100%')

* ## Reference

    * https://intellij-support.jetbrains.com/hc/en-us/articles/206544519-Directories-used-by-the-IDE-to-store-settings-caches-plugins-and-logs
    * https://intellij-support.jetbrains.com/hc/en-us/community/posts/115000113064-How-to-change-debugger-font-size
    * https://stackoverflow.com/questions/66565398/why-do-i-see-unknown-source-in-stack-traces-when-i-have-sources-downloaded/66565466
    * https://issuetracker.google.com/issues/216364005#comment7