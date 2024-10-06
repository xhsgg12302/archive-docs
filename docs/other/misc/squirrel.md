* ## Intro(RIME | SQUIRREL)
    
    > [!CAUTION] 因为需要输入法配置灵活度高，所以找到开源的**鼠须管**，基于**中州韵**输入法引擎的一个 macosx 端实现。[详细介绍(自序、历史、概念、项目构成、开发计划)](https://github.com/rime/home/wiki/Introduction)
    <br><br>重新部署：[参考](https://github.com/rime/squirrel/issues/320)
    <br><span style='padding-left:2.7em'/>`/Library/Input\ Methods/Squirrel.app/Contents/MacOS/Squirrel --reload`
    <br><span style='padding-left:2.7em'/>`control + option + . `

    + ### 下载安装

        > [?] [下载页面](https://rime.im/download/)，[macOS 鼠须管 1.0.2 pkg 安装包](https://github.com/rime/squirrel/releases/download/1.0.2/Squirrel-1.0.2.pkg)(属于将引擎中州韵代码作为 git 子模块，编译成动态链接库供鼠须管使用)
        <br><br> [macOS 编译指南](https://github.com/rime/squirrel/blob/master/INSTALL.md)
        <br>[鼠鬚管 Wiki](https://github.com/rime/squirrel/wiki)
        <br><br>![](/.images/other/misc/squirrel/squirrel-intro-01.png ':size=60%')

    + ### 配置

        - #### 配置文件位置

            > [!WARNING] 应运程序的安装位置: `/Library/Input\ Methods/Squirrel.app/Contents`
            <br>程序附带的共享配置: `/Library/Input\ Methods/Squirrel.app/Contents/SharedSupport/`
            <br>用户自定义覆写目录: `~/Library/Rime/`
            <br><br>配置文件目录及文件分布参考 [(RimeWithSchemata / Rime 中的數據文件分佈及作用)](https://github.com/rime/home/wiki/RimeWithSchemata#rime-中的數據文件分佈及作用)
            <br>配置文件的 YAML 升级语法参考 [(Configuration / Rime 配置文件)](https://github.com/rime/home/wiki/Configuration#rime-配置文件)
            <br>对于定制选项可参考 [(CustomizationGuide / 定製指南)](https://github.com/rime/home/wiki/CustomizationGuide#定製指南)
            
        - #### 外观-Squirrel(鼠须管)

            > [!TIP|label:可参考样例]
            [01-official/squirrel.yaml](https://github.com/rime/squirrel/blob/master/data/squirrel.yaml) <span style='padding-left:1em'>[02-LEOYoon-Tsaw/squirrel.custom.yaml](https://github.com/LEOYoon-Tsaw/Rime_collections/blob/master/squirrel.custom.yaml) <span style='padding-left:1em'>[03-ssnhd/squirrel.custom.yaml](https://github.com/ssnhd/rime/blob/master/配置文件/squirrel.custom.yaml)

            **图片引用自 [官方的示例图](https://github.com/rime/home/wiki/CustomizationGuide#一例定製小狼毫配色方案)，小狼毫和鼠须管外观配置基本一致。如需设置，则可以参考 [主題設計助手](https://github.com/rime/squirrel/wiki#歡迎來到鼠鬚管-wiki) 或者 [润笔 - Rime 设置小助手](https://pdog18.github.io/rime-soak/#/theme)**

            ![](/.images/other/misc/squirrel/squirrel-config-02.png ':size=60%')

        - #### 引擎-Librime(中州韵)

            > [!TIP|label:文档和样例]
            `1).`: 配置文档：[01-RimeWithSchemata](https://github.com/rime/home/wiki/RimeWithSchemata) <span style='padding-left:1em'>[02-雪齋的文檔](https://github.com/LEOYoon-Tsaw/Rime_collections/blob/master/Rime_description.md) <span style='padding-left:1em'>[03-CustomizationGuide](https://github.com/rime/home/wiki/CustomizationGuide#定製指南)
            <br>`2).`: 参考样例：[01-rime-luna-pinyin](https://github.com/rime/rime-luna-pinyin/blob/master/luna_pinyin.schema.yaml) <span style='padding-left:1em'>[02-rime-prelude](https://github.com/rime/rime-prelude/blob/master/default.yaml) <span style='padding-left:1em'>[03-綜合演練 / hello](https://github.com/lotem/rimeime/blob/master/doc/tutorial/hello_7/hello.schema.yaml)

            **根据配置文档自己画的配置关系图如下：**
            ![](/.images/other/misc/squirrel/squirrel-config-01.png ':size=100%')

            > [!CAUTION] 因为官方不建议修改的 **Shared** 文件的原因，所以有些属性就得通过 **custom** 文件进行覆盖或者补丁。

    - ### 自定义及解释

        #### 外观相关参数
        
        > [?] 可以将输入法展示区块分为三部分：inline(目标区)，preedit(编辑区)[可消失]，candidate(候选区)[不消失]
        <br>`inline_preedit__`：编辑区与目标区行内显示（简单理解为`编辑区`覆盖目标区）
        <br>`inline_candidate`：候选区与目标区行内显示（简单理解为`候选区`覆盖目标区）
        <br><span style='padding-left: 0.5em; color:blue'>同时覆盖的话，**编辑区**消失，**候选区**上位，</span>
        <br><br>![](/.images/other/misc/squirrel/squirrel-layout-01.png ':size=80%')
        
        #### 分隔符修改

        > [?] 修改拼写分隔符: `luna_pinyin.custom.yaml`。由原来的`delimiter: " '"` 到 `delimiter: "'"`
        <br>`speller/delimiter`：引用（RimeWithSchemata / 【三】最高武藝）中的注释：**隔音符號用「'」；第一位的空白用來自動插入到音節邊界處**。
        <br><span style='padding-left:2.7em'/>翻译过来就是可以手动通过第二位对拼音进行分割，比如`西安`这个的拼音`xi'an`，就可以手动打`'`，其余自动分割的用第一个符号。
        <br><span style='padding-left:2.7em'/>改完后`delimiter: "'"`的一二位因为一样，所以使用一个就可以了。
        <br><br>![](/.images/other/misc/squirrel/squirrel-config-06.png ':size=80%')

        #### 翻页配置

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

        #### 修改CARET

        > [?] 修改 [caret](https://en.wikipedia.org/wiki/Caret)(插入记号)：符号`‸`，此处的符号引用自 [UI Improvements / 10](https://github.com/rime/squirrel/pull/848)。
        <br>~查看 librime 引擎中相关代码，发现在 [此处](https://github.com/rime/librime/blob/aaaaaec344c22c1b3b8059190a00e4c532a2ab54/tools/rime_api_console.cc#L46) 使用了`|`模拟了插入记号，所以感觉应该是在每个客户端(squirrel|weasel)中去自自己实现的。然后转到 squirrel，在 [此处 SquirrelInputController.swift](https://github.com/rime/squirrel/blob/b110a83c4d0a61a889afbc1e6783a6f32bb279d1/sources/SquirrelInputController.swift#L530) 发现相关代码，其中 caretPos 就是指定了位置，但是并没有看出来是怎么使用的，比如是直接将插入符号追加到编辑区 还是 在后续调用中根据位置直接设置的。swift 代码不太好看，无从下手了，所以没有解决。在此处仅做个记录。~:stuck_out_tongue_closed_eyes:
        <br><br>更新上述错误表述：后面查看代码的时候发现在 librime 引擎处有 [kCaretSymbol 定义](https://github.com/rime/librime/blob/aaaaaec344c22c1b3b8059190a00e4c532a2ab54/src/rime/context.cc#L38)，而且在下面一行处有用户可配置的选项`soft_cursor`，大概熟悉代码后发现这只是一个开关(false|true)，用来控制是否显示插入符号的开关，通过 debug 验证后发现可行。于是添加如下图括起来的类似配置，在本地进行测试，发现在 Squirrel 端并不生效。于是查看 squirrel 端代码，发现有 [这样的判断](https://github.com/rime/squirrel/blob/b110a83c4d0a61a889afbc1e6783a6f32bb279d1/sources/SquirrelInputController.swift#L440-L443)，进而对`soft_cursor`进行了覆写，所以没生效。但是根据它的判断条件也可以进行验证，一般对于浏览器，都是进行强制内联(inline)的。所以在浏览器地址栏输入就不会出现插入符号了。
        <br>![](/.images/other/misc/squirrel/squirrel-config-05.png ':size=83%')
        <br><br>接着，为了实现修改插入记号的初始想法，就必须修改引擎代码然后重新编译出动态链接库，替换 squirrel 自己编译出来的。步骤如下：
        <br>`1).`: 修改 [kCaretSymbol 定义](https://github.com/rime/librime/blob/aaaaaec344c22c1b3b8059190a00e4c532a2ab54/src/rime/context.cc#L38) 中 38 行代码为：`static const string kCaretSymbol("\xe2\x86\x9e");`，`\xe2\x86\x9e`就是你想替换的任何 UTF-8 符号的十六进制编码。比如我此处的就是`↞`。
        <br>`2).`: 此处使用 clion + camke 的方式编译出动态链接库`/path/librime/cmake-build-release/lib/librime.1.11.2.dylib`
        <br>`3).`: 进入 squirrel 输入法目录：`cd /Library/Input Methods/Squirrel.app/Contents/Frameworks`
        <br>`4).`: 备份 squirrel 自己编译出来的：`sudo mv librime.1.dylib librime.1.dylib.bak`
        <br>`5).`: 使用刚才编译出的进行替换：`sudo cp /path/librime/cmake-build-release/lib/librime.1.11.2.dylib librime.1.dylib`
        <br>`6).`: 重启输入法进行验证：`/Library/Input\ Methods/Squirrel.app/Contents/MacOS/Squirrel --quit`，过一会自己就启动了。
        <br><br><span style='color:blue'>如果需要此次编译出来 macOS 端的动态链接库`librime.1.11.2.dylib`，[点击下载](https://github.com/xhsgg12302/knownledges/raw/f99570a76c657c8a61297277d4260e7e913a5780/.images/other/misc/squirrel/librime.1.11.2.dylib)。</span>
        <br><span style='color:blue;padding-left:2.7em'>需要注意的是：</span>
        <br><span style='padding-left:2.7em'>`a).`: 这次编译出来的 Release 版本大小为`3.4M`，原来的为`7.0M`，~不知道什么区别，仅供测试~。(后来发现跟插件有关系，比如 lua)
        <br><span style='padding-left:2.7em'>`b).`: 浏览器中编辑框中最后的那个竖线`|`是闪动的光标，正好闪动的时候截的图。
        <br><br>![](/.images/other/misc/squirrel/squirrel-config-04.gif)  ![](/.images/other/misc/squirrel/squirrel-config-03.png ':size=40%')

        #### 添加LUA脚本

        > [?] `lua`脚本使用，直接输入日期之类的
        <br>使用方法：
        <br>`a).`: 在用户配置目录`~/Library/Rime/`新建文件`rime.lua`，里面的内容 [参考 hchunhui/librime-lua/wiki](https://github.com/hchunhui/librime-lua/wiki)
        <br>`b).`: 在`luna_pinyin.custom.yaml`中添加 `engine/translators/@before 0/: lua_translator@date_translator`， 启用 lua 日期翻译器。效果如下左 gif。
        <br><br>但是这样的话，自己编译的`librime.1.11.2.dylib`里面没有启用插件，需要将插件编译进去，编译过程如下：
        <br>`1).`: 目前 rime 生态体系支持的插件有 [这些 plugins](https://github.com/rime/librime/blob/aaaaaec344c22c1b3b8059190a00e4c532a2ab54/README.md#plugins)
        <br>`2).`: 如果想要安装某个插件，比如`hchunhui/librime-lua`，进入到源码目录 **librime** ，在终端执行`./install-plugins.sh hchunhui/librime-lua`。会将插件仓库下载到当前目录的`plugins`中。
        <br><span style='padding-left:2.9em'>其他插件类似，但是`hchunhui/librime-lua`，这个插件的 **[CMakeLists.txt](https://github.com/hchunhui/librime-lua/blob/fa6563cf7b40f3bfbf09e856420bff8de6820558/CMakeLists.txt#L5)** 第五行有点问题，我本地是有 lua-5.4.6 的，但是它没检测到，将`lua54` 改成`lua5.4`即可通过重建 cmake 工程。
        <br>`3).`: 将 clion 切换到 **Release** profile， 重新 build 即可重新生成 `cmake-build-release/lib/librime.1.11.2.dylib`。
        <br>`4).`: 如上 [修改CARET](#修改caret) 的操作，复制替换重启一气呵成。效果如下右 gif。
        <br><br>![](/.images/other/misc/squirrel/squirrel-config-07.gif)  ![](/.images/other/misc/squirrel/squirrel-config-08.gif)

        #### 添加词库

        > [?] 添加搜狗 [网络](https://pinyin.sogou.com/dict/detail/index/4)，[计算机](https://pinyin.sogou.com/dict/detail/index/15117) 等相关词库。并使用在线工具 [词库转换器](https://gaoweix.com/im-dict-converter/)，将 scel 文件转换成 txt，比如：**网络流行新词【官方推荐】.txt** 、**计算机词汇大全【官方推荐】.txt**
        <br><br>秉承不修改 Shared 目录文件的原则，通过给`luna_pinyin.schema.yaml`打补丁，修改**translator/dictionary** 的值。
        <br>具体操作如下：
        <br>`1).`: 将 Shared 目录 **/Library/Input\ Methods/Squirrel.app/Contents/SharedSupport/** 中的`luna_pinyin.dict.yaml`复制一份到用户目录 **~/Library/Rime/** ，并改名为`luna_pinyin_compact.dict.yaml`。
        <br>`2).`: 编辑`luna_pinyin_compact.dict.yaml`,在 **use_preset_vocabulary** 一行后面添加`import_tables: [ sougou_pinyin_network, sougou_pinyin_computer ]`
        <br>`3).`: 新建文件`sougou_pinyin_network.dict.yaml`，先写入如下模板头，剩下的通过命令将 txt 内容追加到后面即可。计算机同样的方式。
        <br>`4).`: 导入命令：`cat /path/网络流行新词【官方推荐】.txt >> sougou_pinyin_network.dict.yaml`
        <br>`5).`: 将原来`luna_pinyin.schema.yaml`中定义的`translator/dictionary: luna_pinyin` 通过打补丁的方式替换，这样明月拼音相关方案中的词典都会替换。
        <br><span style='padding-left:2.9em'>补丁方法：在`luna_pinyin.custom.yaml`添加内容补丁：`translator/dictionary: luna_pinyin_compact`
        <br><span style='padding-left:2.9em'>卸载词库也很简单，只需要将补丁行`translator/dictionary: luna_pinyin_compact`注释即可。
        <br><span style='color:blue'>词库去重部分暂时没处理。</span>
        <br><br>![](/.images/other/misc/squirrel/squirrel-config-09.gif)  ![](/.images/other/misc/squirrel/squirrel-config-10.gif)

        <!-- panels:start -->
        <!-- div:left-panel-33 -->
        ```yaml
        # luna_pinyin_compact.dict.yaml

        # Rime dictionary
        # encoding: utf-8
        #

        ---
        name: luna_pinyin_compact
        version: "2024.10.06"
        sort: by_weight
        use_preset_vocabulary: true
        import_tables: [ sougou_pinyin_network, sougou_pinyin_computer ]
        ...

        〇  ling
        ㄓ  zhi
        ㄔ  chi
        ㄕ  shi
        ㄖ  ri
        # ...
        ```
        <!-- div:right-panel-33 -->
        ```yaml
        # sougou_pinyin_network.dict.yaml

        # Rime dictionary
        # encoding: utf-8
        #

        ---
        name: sougou_pinyin_network
        version: "2024.10.06"
        sort: by_weight
        use_preset_vocabulary: true
        ...

        阿拜多斯
        阿贝贝
        阿贝多
        阿策
        阿蝉
        啊对对对
        # ...
        ```
        <!-- div:right-panel-33 -->
        ```yaml
        # sougou_pinyin_computer.dict.yaml

        # Rime dictionary
        # encoding: utf-8
        #

        ---
        name: sougou_pinyin_computer
        version: "2024.10.06"
        sort: by_weight
        use_preset_vocabulary: true
        ...

        阿姆达尔定律
        阿帕网
        埃尔布朗基
        埃尔米特函数
        埃克特
        艾丽莎病毒
        # ...
        ```
        <!-- panels:end -->

        - #### reference

            * https://github.com/rime/home/wiki/Configuration#rime-配置文件
            * [RIME v0.16.1 小狼毫輸入法（支援Win, macOS, Linux）](https://briian.com/9216/)
            * https://github.com/ssnhd/rime

    + ### Rime引擎

        <p style="text-align: center;">  <strong><a href="https://github.com/rime/librime/tree/aaaaaec344c22c1b3b8059190a00e4c532a2ab54" target="_blank" rel="noopener">版本：[2024/09/29]：https://github.com/rime/librime/tree/aaaaaec344c22c1b3b8059190a00e4c532a2ab54</a></strong></p>

        - #### 源码处理参考

            > [!COMMENT|style:flat] 
            `engine` 中的所有模块组合 [gears_module.cc](https://github.com/rime/librime/blob/aaaaaec344c22c1b3b8059190a00e4c532a2ab54/src/rime/gear/gears_module.cc)
            <br>`engine` 中对于按键的处理流程 [ConcreteEngine::ProcessKey](https://github.com/rime/librime/blob/aaaaaec344c22c1b3b8059190a00e4c532a2ab54/src/rime/engine.cc#L99C1-L122C2)
            <br>`engine` 中处理器`processor`中 **fluid_editor/fluency_editor** 和 **express_editor** 的处理 [异同](https://github.com/rime/librime/blob/aaaaaec344c22c1b3b8059190a00e4c532a2ab54/src/rime/gear/editor.cc#L189-L218)

        - #### 编译与调试

            > [!NOTE|style:flat] 由于需要查看 yaml 文件生成后到底是什么样的，以及配置了为什么没有生效等问题。所以将源代码拉取下来，进行分析，结合日志打印出的信息以及 AIGC 等问答，最后修改 librime 输入法引擎部分代码，实现测试，验证等效果。
            <br><br>修改的commit为： [aaaaaec3](https://github.com/rime/librime/tree/aaaaaec344c22c1b3b8059190a00e4c532a2ab54)
            <br>编译参考文档： [Rime with Mac](https://github.com/rime/librime/blob/aaaaaec344c22c1b3b8059190a00e4c532a2ab54/README-mac.md)
            <br>文件变更：[feat: add yaml dump to temp dir for test and verify.
    ](https://github.com/12302-bak/librime/commit/2758163b9716914ac534339b06a39045001e3a0b)

            ![](/.images/other/misc/squirrel/squirrel-core-01.gif)

* ## Other

    > [!CAUTION] `1).`: 在计算机科学和编程中，`grave` 通常指的是“重音符”或“倒尖号”，在键盘上通常位于数字 1 键的左边，形状像一个倾斜的撇号，有时也被称为反引号 `` ` ``。 --- 来自阿里通义
    <br>`2).`: `space`理解为空格，这个算是基本常识了、但是`backspace`一度懵逼，后来一查，发现是`退格键`。:stuck_out_tongue_closed_eyes:

* ## Reference
    + https://rime.im
    + 
    + wiki
    + https://github.com/rime/home/wiki
    + https://github.com/rime/squirrel/wiki
    + 
    + [有哪些好用且开源的输入法？](https://www.zhihu.com/question/274093588)
    + [一位匠人的中州韵——专访Rime输入法作者佛振（图灵访谈）](https://m.ituring.com.cn/article/118072)