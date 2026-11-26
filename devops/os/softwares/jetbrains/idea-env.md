---
description: "本文记录 IDEA 开发环境的配置与插件清单，涵盖常用插件、生成 serialVersionUID、配色与快捷键设置、添加框架支持等，并整理反编译 jar、Github 授权失败、Gradle 模块设置及 Jrebel 激活等技巧。"
def_keywords: "IDEA, 插件, serialVersionUID, 配色, 快捷键, 反编译, Jrebel, 配置"
---

* ## Intro(idea-env | idea 环境)

    + ### 插件

        | column1      | column2                        | column3                   |
        | ------------ | ------------------------------ | ------------------------- |
        | Lombok       | Maven Helper                   | Mybaits Log Plugin        |
        | Translation  | Find bugs                      | RestfulToolkit            |
        | .ignore      | Alibaba Java Coding Guidelines | jclasslib bytecode viewer |
        | Codota       | Material Theme UI              | SequenceDiagram           |
        | StickyScroll |                                |                           |

    + ### 配置

        - #### 生成serialVersionUID

            > [?] [参考](https://intellij-support.jetbrains.com/hc/en-us/community/posts/14718197525906-intellij-serialversionuid-generate):
            <br> File -> Settings -> Editor -> Inspections -> JVM Languages : Find `serialization class without 'serialVersionUID'` and check it.
            <br> Back to the editor, clicks on the class name, ALT + ENTER (Windows), it will prompts the Add serialVersionUID field option.A new serialVersionUID is auto-generated.

            ![](/.images/devops/os/softwares/idea-config-serial-01.png ':size=70%')

        - #### 通用配色

            > [?] File -> Settings -> Editor -> Color Scheme -> General : 
            <br><span style='padding-left:2.7em'/> 变量颜色：`Code / Identifier under caret`:`{bg: C931E0, esm: 5B786A}`，**write** :`{bg: 8A6A0D, esm: BA6387}`

            ![](/.images/devops/os/softwares/idea-config-color-01.png ':size=70%')

        - #### 快捷键冲突

            > [?] idea代码块注释<span style='color:blue'>[⇧⌘/]</span>和系统快捷键冲突

            ![](/.images/devops/os/mac/macosx-keyboard-conflict-02.png ':size=70%x500')

        - #### 添加框架支持

            > [?] 比如添加框架支持或者添加为maven项目，（Add Maven Projects | Add Framework Support）等通过全局查找 [【手动添加】](https://www.jetbrains.com/help/idea/adding-support-for-frameworks-and-technologies.html#manually-add-facet-to-module)

            ![](/.images/devops/os/softwares/idea-search-anywhere-01.png ':size=49%') ![](/.images/devops/os/softwares/idea-search-anywhere-02.png ':size=49%')

    + ### 其他

        - #### 使用idea 插件反编译整个jar到文件夹

            `java -cp "D:\Program Files\JetBrains\IntelliJ IDEA 2018.2.8\plugins\java-decompiler\lib\java-decompiler.jar" org.jetbrains.java.decompiler.main.decompiler.ConsoleDecompiler`

        - #### 登录github授权总是失败

            > [!] 此次的原因是因为浏览器代理使用http:8889,而idea使用socks5:1089

        - #### idea gradle create separate module per source set in gradle.xml

            https://dkimitsa.github.io/2020/01/09/idea-disabling-separate-module-per-source-set/ </br>
            https://blog.jetbrains.com/idea/2019/05/intellij-idea-starts-the-2019-2-early-access-program/  
            add：
            
            ```xml
            <option name="resolveModulePerSourceSet" value="false" />
            ```

        - #### jrebel激活
        
            http://jrebel.qekang.com/ </br>
            https://idea.javatiku.cn/ </br>
            https://www.jb51.net/article/201533.htm </br>
            http://jrebel-license.jiweichengzhu.com/ </br>
            https://jrebel.com.cn/#/ <br>
            https://www.jpy.wang/page/jrebel.html
            
            + ##### 历史记录

                - [ ] http://idea.javatiku.cn/460bbf73-1d7d-4b5e-a537-43ea8b23f502
                - [ ] https://jrebel.com.cn/65827c00-5e65-4c1e-a358-1f6f2f96880e (0wetmj@outlook.com)
                - [ ] https://jrebel.com.cn/c904ed35-1491-48f8-98b4-c8c2ad6678da
                - [ ] https://jrebel.speedcow.top/e74fd726-f540-4dda-77fa-df484c286d3b
                - [ ] http://42.193.18.168:8088/4e7defb7-09ad-45da-bf9c-11eda2ab0f6a (879271507@qq.com) (https://www.jpy.wang/page/jrebel.html)
                - [x] https://jrebel.haitim.cn/05b4a954-84f2-48ae-8cc2-02dc09a7468e
                - [x] https://jyoryo.com/{uuid}
                - [x] https://jrebel.wtfu.site/3c9fc43d-0e48-4370-9b9c-6c2520685ade
                - [ ] http://42.193.18.168:8088/8c9dca0b-19e6-4a7a-894e-0299d0fd9f5a (971122716@qq.com) (https://www.jpy.wang/page/jrebel.html) (兼容 2023.4.0 +)

            + ##### Issues

                > [!WARNING|style:flat] 1、`Failed to update seat. Cannot read the array length because "sigBytes" is null`，解决办法参考[issues-36](https://github.com/ilanyu/ReverseProxy/issues/36#issuecomment-2797182859)

* ## References

    + https://stackoverflow.com/questions/28389006/how-to-decompile-to-java-files-intellij-idea
    + https://intellij-support.jetbrains.com/hc/en-us/community/posts/14718197525906-intellij-serialversionuid-generate