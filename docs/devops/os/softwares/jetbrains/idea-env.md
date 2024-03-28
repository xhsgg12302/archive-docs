
+ ## 插件

    | column1      | column2                        | column3                   |
    | ------------ | ------------------------------ | ------------------------- |
    | Lombok       | Maven Helper                   | Mybaits Log Plugin        |
    | Translation  | Find bugs                      | RestfulToolkit            |
    | .ignore      | Alibaba Java Coding Guidelines | jclasslib bytecode viewer |
    | Codota       | Material Theme UI              | SequenceDiagram           |
    | StickyScroll |                                |                           |

+ ## 配置

    - ### 生成serialVersionUID

        ?> [参考](https://intellij-support.jetbrains.com/hc/en-us/community/posts/14718197525906-intellij-serialversionuid-generate):
        <br> File -> Settings -> Editor -> Inspections -> JVM Languages : Find `serialization class without 'serialVersionUID'` and check it.
        <br> Back to the editor, clicks on the class name, ALT + ENTER (Windows), it will prompts the Add serialVersionUID field option.A new serialVersionUID is auto-generated.

        ![](/.images/devops/os/softwares/idea-config-serial-01.png ':size=70%')

+ ## 其他

    - ### 使用idea 插件反编译整个jar到文件夹

    `java -cp "D:\Program Files\JetBrains\IntelliJ IDEA 2018.2.8\plugins\java-decompiler\lib\java-decompiler.jar" org.jetbrains.java.decompiler.main.decompiler.ConsoleDecompiler`

    - ### 登录github授权总是失败

        !> 此次的原因是因为浏览器代理使用http:8889,而idea使用socks5:1089

    - ### idea gradle create separate module per source set in gradle.xml

        https://dkimitsa.github.io/2020/01/09/idea-disabling-separate-module-per-source-set/ </br>
        https://blog.jetbrains.com/idea/2019/05/intellij-idea-starts-the-2019-2-early-access-program/  
        add：
        
        ```xml
        <option name="resolveModulePerSourceSet" value="false" />
        ```
    
    - ### idea 激活 无限重置仓库(内置插件方式)

        http://idea.javatiku.cn/ </br>
        https://gitee.com/pengzhile/ide-eval-resetter.git

    - ### idea 激活方式2

        https://jetbra.in/s </br>
        https://3.jetbra.in/  
        下载相印的jar包，根据vmoptions文件夹配置，然后放入激活码。激活配置目录  在 application 下 content, clion.vmoptions

    - ### jrebel激活
    
        http://jrebel.qekang.com/ </br>
        https://idea.javatiku.cn/ </br>
        https://www.jb51.net/article/201533.htm </br>
        http://jrebel-license.jiweichengzhu.com/ </br>
        https://jrebel.com.cn/#/

        + 历史记录

            - [ ] http://idea.javatiku.cn/460bbf73-1d7d-4b5e-a537-43ea8b23f502
            - [x] https://jrebel.com.cn/65827c00-5e65-4c1e-a358-1f6f2f96880e (0wetmj@outlook.com)

* ## References

    + https://stackoverflow.com/questions/28389006/how-to-decompile-to-java-files-intellij-idea
    + https://intellij-support.jetbrains.com/hc/en-us/community/posts/14718197525906-intellij-serialversionuid-generate