* ## Intro(Jetbrains)

    ### WebStorm文件内容不能预览(打不开文件)
    
    ?> 可能原因是新版本使用了老版本的插件，不兼容造成的原因。解决办法：`删除对应版本WebStorm中的plugins目录`。[参考](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360010609860-Intellij-cannot-view-any-file-content) 删除了额外的插件 [目录](https://intellij-support.jetbrains.com/hc/en-us/articles/206544519-Directories-used-by-the-IDE-to-store-settings-caches-plugins-and-logs)

    <!-- panels:start -->
    <!-- div:left-panel-40 -->
    ![](/.images/devops/os/softwares/jetbrains-readme-01.png ':size=100%')
    <!-- div:right-panel-60 -->
    ![](/.images/devops/os/softwares/jetbrains-readme-02.png ':size=100%')
    <!-- panels:end -->

    ### Idea调试异常信息显示(Unknown Source)

    ?> 可能原因是当前使用的jar包在编译的时候没有加入调试信息，比如(`LineNumberTable`, [`LocalVariableTable`](/docs/doc/advance/javastrace.md#localvariabletable), etc. )
    <br> [参考1：Why do I see unknown source in stack traces when I have sources downloaded?](https://stackoverflow.com/questions/66565398/why-do-i-see-unknown-source-in-stack-traces-when-i-have-sources-downloaded/66565466)
    <br> [参考2：bouncy castle: How to get bcpkix-jdk15on-1.47.jar with debug information](https://stackoverflow.com/questions/12894129/bouncy-castle-how-to-get-bcpkix-jdk15on-1-47-jar-with-debug-information)

    <!-- panels:start -->
    <!-- div:left-panel-60 -->
    ![](/.images/devops/os/softwares/idea-stacktrace-unknown-source-01.png ':size=100%')
    <!-- div:right-panel-40 -->
    ![](/.images/devops/os/softwares/idea-stacktrace-unknown-source-02.png ':size=95%')
    <!-- panels:end -->

* ## Reference

    * https://intellij-support.jetbrains.com/hc/en-us/articles/206544519-Directories-used-by-the-IDE-to-store-settings-caches-plugins-and-logs
    * https://stackoverflow.com/questions/66565398/why-do-i-see-unknown-source-in-stack-traces-when-i-have-sources-downloaded/66565466