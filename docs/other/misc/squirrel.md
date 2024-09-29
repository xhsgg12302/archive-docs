* ## Intro(SQUIRREL)
    
    > [!CAUTION] 因为需要输入法配置灵活度高，所以找到开源的**鼠须管**，基于**中州韵**输入法引擎的一个 macosx 端实现。[详细介绍(自序、历史、概念、项目构成、开发计划)](https://github.com/rime/home/wiki/Introduction)
    <br><br>重新部署：[参考](https://github.com/rime/squirrel/issues/320)
    <br><span style='padding-left:2.7em'/>`/Library/Input\ Methods/Squirrel.app/Contents/MacOS/Squirrel --reload`
    <br><span style='padding-left:2.7em'/>`control + option + . `

    + ### 下载安装

        > [?] [下载页面](https://rime.im/download/)，[macOS 鼠须管 1.0.2 pkg 安装包](https://github.com/rime/squirrel/releases/download/1.0.2/Squirrel-1.0.2.pkg)
        <br><br> [macOS 编译指南](https://github.com/rime/squirrel/blob/master/INSTALL.md)
        <br>[鼠鬚管 Wiki](https://github.com/rime/squirrel/wiki)
        <br><br>![](/.images/other/misc/squirrel/squirrel-intro-01.png ':size=60%')

    + ### 配置

        - #### 配置文件位置

            > [!WARNING] 应运程序的安装位置: `/Library/Input\ Methods/Squirrel.app/Contents`
            <br>程序附带的共享配置: `/Library/Input\ Methods/Squirrel.app/Contents/SharedSupport/squirrel.yaml`
            <br>用户自定义覆写目录: `~/Library/Rime/`

            > [?] 可以将输入法展示区块分为三部分：inline(目标区)，preedit(编辑区)[可消失]，candidate(候选区)[不消失]
            <br>`inline_preedit__`：编辑区与目标区行内显示（简单理解为`编辑区`覆盖目标区）
            <br>`inline_candidate`：候选区与目标区行内显示（简单理解为`候选区`覆盖目标区）
            <br><span style='padding-left: 0.5em; color:blue'>同时覆盖的话，**编辑区**消失，**候选区**上位，</span>
            <br><br>![](/.images/other/misc/squirrel/squirrel-layout-01.png ':size=80%')
        - #### reference

            * https://github.com/rime/home/wiki/Configuration#rime-配置文件
            * [RIME v0.16.1 小狼毫輸入法（支援Win, macOS, Linux）](https://briian.com/9216/)
            * https://github.com/ssnhd/rime

    + ### 工具

        - [主題設計助手](https://github.com/rime/squirrel/wiki#歡迎來到鼠鬚管-wiki)

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