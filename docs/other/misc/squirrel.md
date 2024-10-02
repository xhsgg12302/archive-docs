* ## Intro(RIME | SQUIRREL)
    
    > [!CAUTION] 因为需要输入法配置灵活度高，所以找到开源的**鼠须管**，基于**中州韵**输入法引擎的一个 macosx 端实现。[详细介绍(自序、历史、概念、项目构成、开发计划)](https://github.com/rime/home/wiki/Introduction)
    <br><br>重新部署：[参考](https://github.com/rime/squirrel/issues/320)
    <br><span style='padding-left:2.7em'/>`/Library/Input\ Methods/Squirrel.app/Contents/MacOS/Squirrel --reload`
    <br><span style='padding-left:2.7em'/>`control + option + . `

    + ### 下载安装

        > [?] [下载页面](https://rime.im/download/)，[macOS 鼠须管 1.0.2 pkg 安装包](https://github.com/rime/squirrel/releases/download/1.0.2/Squirrel-1.0.2.pkg)(属于将引擎中州韵代码作为 git 子模块，编译成静态库文件供鼠须管使用)
        <br><br> [macOS 编译指南](https://github.com/rime/squirrel/blob/master/INSTALL.md)
        <br>[鼠鬚管 Wiki](https://github.com/rime/squirrel/wiki)
        <br><br>![](/.images/other/misc/squirrel/squirrel-intro-01.png ':size=60%')

    + ### 配置

        - #### 配置文件位置

            > [!WARNING] 应运程序的安装位置: `/Library/Input\ Methods/Squirrel.app/Contents`
            <br>程序附带的共享配置: `/Library/Input\ Methods/Squirrel.app/Contents/SharedSupport/`
            <br>用户自定义覆写目录: `~/Library/Rime/`
            <br><br>YAML 配置文件的升级语法 [参考](https://github.com/rime/home/wiki/Configuration#rime-配置文件)
            
        - #### 外观-Squirrel(鼠须管)

            > [!TIP|label:可参考样例]
            [样例01](https://github.com/rime/squirrel/blob/master/data/squirrel.yaml) <span style='padding-left:1em'>[样例02](https://github.com/LEOYoon-Tsaw/Rime_collections/blob/master/squirrel.custom.yaml) <span style='padding-left:1em'>[样例03](https://github.com/ssnhd/rime/blob/master/配置文件/squirrel.custom.yaml)

            **图片引用自 [官方的示例图](https://github.com/rime/home/wiki/CustomizationGuide#一例定製小狼毫配色方案)，小狼毫和鼠须管外观配置基本一致。如需设置，则可以参考 [主題設計助手](https://github.com/rime/squirrel/wiki#歡迎來到鼠鬚管-wiki) 或者 [润笔 - Rime 设置小助手](https://pdog18.github.io/rime-soak/#/theme)**

            ![](/.images/other/misc/squirrel/squirrel-config-02.png ':size=60%')

        - #### 引擎-Librime(中州韵)

            **根据配置文档自己画的配置关系图如下：**
            ![](/.images/other/misc/squirrel/squirrel-config-01.png ':size=100%')

            > [!CAUTION] 因为官方不建议修改的 Shared 文件的原因，所以有些属性就得通过 **custom** 文件进行覆盖或者补丁。

        - #### 自定义及解释

            > [?] 可以将输入法展示区块分为三部分：inline(目标区)，preedit(编辑区)[可消失]，candidate(候选区)[不消失]
            <br>`inline_preedit__`：编辑区与目标区行内显示（简单理解为`编辑区`覆盖目标区）
            <br>`inline_candidate`：候选区与目标区行内显示（简单理解为`候选区`覆盖目标区）
            <br><span style='padding-left: 0.5em; color:blue'>同时覆盖的话，**编辑区**消失，**候选区**上位，</span>
            <br><br>![](/.images/other/misc/squirrel/squirrel-layout-01.png ':size=80%')
            
            > [?] 修改拼写分隔符: `luna_pinyin.custom.yaml`。由原来的`delimiter: " '"` 到 `delimiter: "'"`

            <!-- panels:start -->
            <!-- div:left-panel-50 -->
            > [?] ~修改翻页为~`-|=`: 在位置`default.custom.yaml`。
            <br>key_binder: 增加两个 bindings <span style='color: blue'>（结果没生效）</span>
            <br><span style='padding-left:2.7em'/>`{ when: paging, accept: minus, send: Page_Up}`
            <br><span style='padding-left:2.7em'/>`{ when: has_menu, accept: equal, send: Page_Down}`
            <br> 大意了，原来预置的`key_bindings.yaml`里买就有相关快捷键的定义，但是自己手贱，又重新在`default.custom.yaml`里面重新定义了，并且没有绑定相关键位，发现不生效，以为原来的预置的键位没有绑定上去。其实应该是在 custom 文件里面进行追加或者修改的，不是直接定义<span style='color: blue'>(会覆盖)</span>。
            <br><br>例如：右边`default.custom.yaml`代码中**追加**和**修改**示例。
            <!-- div:right-panel-50 -->
            ```yaml
            # default.custom.yaml
            patch:
                # append
                key_binder/+:
                    bindings/+:
                    - { when: has_menu, accept: at, send: 'm' }
                
                # update
                key_binder/bindings/@0:
                    { when: has_menu, accept: minus, send: Print }
            ```
            <!-- panels:end -->

        - #### reference

            * https://github.com/rime/home/wiki/Configuration#rime-配置文件
            * [RIME v0.16.1 小狼毫輸入法（支援Win, macOS, Linux）](https://briian.com/9216/)
            * https://github.com/ssnhd/rime

    + ### Rime引擎编译与调试

        > [?] 由于需要查看 yaml 文件生成后到底是什么样的，以及配置了为什么没有生效等问题。所以将源代码拉取下来，进行分析，结合日志打印出的信息以及 AIGC 等问答，最后修改 librime 输入法引擎部分代码，实现测试，验证等效果。
        <br><br>修改的commit为： [aaaaaec3](https://github.com/rime/librime/tree/aaaaaec344c22c1b3b8059190a00e4c532a2ab54)
        <br>编译参考文档： [Rime with Mac](https://github.com/rime/librime/blob/aaaaaec344c22c1b3b8059190a00e4c532a2ab54/README-mac.md)
        <br>文件变更：[feat: add yaml dump to temp dir for test and verify.
](https://github.com/12302-bak/librime/commit/2758163b9716914ac534339b06a39045001e3a0b)

        ![](/.images/other/misc/squirrel/squirrel-core-01.gif)

* ## Other

    > [!CAUTION] 在计算机科学和编程中，`grave` 通常指的是“重音符”或“倒尖号”，在键盘上通常位于数字 1 键的左边，形状像一个倾斜的撇号，有时也被称为反引号 `` ` ``。 --- 来自阿里通义

* ## Reference
    + https://rime.im
    + 
    + wiki
    + https://github.com/rime/home/wiki
    + https://github.com/rime/squirrel/wiki
    + 
    + [有哪些好用且开源的输入法？](https://www.zhihu.com/question/274093588)
    + [一位匠人的中州韵——专访Rime输入法作者佛振（图灵访谈）](https://m.ituring.com.cn/article/118072)