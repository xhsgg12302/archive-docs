* ## Intro(Jetbrains)

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
    <br> [参考2：bouncy castle: How to get bcpkix-jdk15on-1.47.jar with debug information](https://stackoverflow.com/questions/12894129/bouncy-castle-how-to-get-bcpkix-jdk15on-1-47-jar-with-debug-information)

    <!-- panels:start -->
    <!-- div:left-panel-60 -->
    ![](/.images/devops/os/softwares/idea-stacktrace-unknown-source-01.png ':size=100%')
    <!-- div:right-panel-40 -->
    ![](/.images/devops/os/softwares/idea-stacktrace-unknown-source-02.png ':size=95%')
    <!-- panels:end -->

    ### Idea设置debug面板等宽字体

    > [?] 1). 因为需要对比debug内容，尤其是字节数组，所以设置[`JetBrains Mono`](https://www.jetbrains.com/zh-cn/lp/mono/)等宽字体。
    <br>2). 设置debug面板[\[参考\]](https://intellij-support.jetbrains.com/hc/en-us/community/posts/115000113064-How-to-change-debugger-font-size)，通过全局来控制或影响`Settings | Appearance & Behavior | Appearance | Use custom font`。Editor或者console面板可以通过`Settings | Editor | Font`，`Settings | Editor | Color Scheme | Console Font`单独设置.

    ![](/.images/devops/os/softwares/idea-display-width-font-01.png ':size=70%')

    ### Idea不能读取系统环境变量

    > [!] 原先因为调试IDEA启不动问题，手贱将idea启动目录下的一个C`/Applications/IntelliJ IDEA.app/Contents/bin/printenv`程序因为打印格式问题修改成了`/usr/bin/printenv`，导致IDEA读取不到系统设置的环境变量。效果类似如下图所示。但是好在有大佬们的文章[Env variables not sourced when launched from Mac OS X Monterey Dock](https://issuetracker.google.com/issues/216364005#comment7)可以借鉴，最终成功解决。

    ![](/.images/devops/os/softwares/idea-not-read-env-01.png ':size=49%')
    ![](/.images/devops/os/softwares/idea-not-read-env-02.png ':size=49%')

* ## Reference

    * https://intellij-support.jetbrains.com/hc/en-us/articles/206544519-Directories-used-by-the-IDE-to-store-settings-caches-plugins-and-logs
    * https://intellij-support.jetbrains.com/hc/en-us/community/posts/115000113064-How-to-change-debugger-font-size
    * https://stackoverflow.com/questions/66565398/why-do-i-see-unknown-source-in-stack-traces-when-i-have-sources-downloaded/66565466
    * https://issuetracker.google.com/issues/216364005#comment7