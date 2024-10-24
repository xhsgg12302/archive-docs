* ## Intro(Burp-Suite-Pro)
    
    > [?] 抓包软件

    <!-- panels:start -->
    <!-- div:left-panel-50 -->
    ![](/.images/devops/os/mac/burp-suite-pro-activate-01.png ':size=100% 关于')
    <!-- div:right-panel-50 -->
    ![](/.images/devops/os/mac/burp-suite-pro-activate-02.png ':size=100% 激活信息')
    <!-- panels:end -->

    + ### 下载

        > [?] [组合包：Burp_Suite_Pro_v1.7.31_Loader_Keygen.zip 包括注册机jar包和官方的jar包](https://pan.baidu.com/s/1ftFPsURlTD_sCIzHziKwKQ?pwd=5rbq)
        <br>也就是当前这个使用的是官方的，而不是修改过的。
        <br><br>[单独注册机下载：burp-loader-keygen.jar](https://pan.baidu.com/s/1cCj6ESFtc7ebwXNXbFZczQ?pwd=picz)。 <span style="color: #dc3545;">需要注意注册机有的有后门，可以参考：[恶意软件逆向：burpsuite 序列号器后门分析](https://www.anquanke.com/post/id/96866/)</span>
        <br><br>[官方下载地址: https://portswigger.net/burp/releases/professional-1-7-31](https://portswigger.net/burp/releases/professional-1-7-31)
        <br>[单独应用下载：burpsuite_pro_v1.7.31.jar](https://portswigger-cdn.net/burp/releases/download?product=pro&version=1.7.31&type=Jar)。 
        <br>可以通过`shasum -a 256 burpsuite_pro_v1.7.31.jar`，`md5 burpsuite_pro_v1.7.31.jar`查看当前包的sha256和md5与官方进行对比。

        ![](/.images/devops/os/mac/burp-suite-pro-01.png ':size=50%')
        ![](/.images/devops/os/mac/burp-suite-pro-02.png ':size=49%')

    + ### 激活

        > [?] [placeholder](https://www.vuln.cn/8847)

    + ### 制作APP

        > [?] 使用如下命令制作 Mac APP,参考：[Automator中的burp-suite-pro](./automator.md#burp-suite-pro)
        <br> JDK1.8启动命令 `java -Xbootclasspath/p:/path/burp-loader-keygen.jar -jar /path/burpsuite_pro_v1.7.31.jar`

## Reference
* https://pan.baidu.com/s/1ftFPsURlTD_sCIzHziKwKQ?pwd=5rbq
* https://www.52pojie.cn/thread-691448-1-1.html
* https://www.52pojie.cn/thread-997035-1-1.html
* [恶意软件逆向：burpsuite 序列号器后门分析](https://www.anquanke.com/post/id/96866/)