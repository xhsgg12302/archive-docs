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

        #### 修改插入记号(CARET)

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
        <br>`4).`: 如上 [修改插入记号CARET](#修改插入记号caret) 的操作，复制替换重启一气呵成。效果如下右 gif。
        <br><br><span style='color:blue'>如果需要此次编译出来 macOS 端的动态链接库`2ed-librime.1.11.2.dylib`，[点击下载](https://github.com/xhsgg12302/knownledges/blob/8e9b70a396b311a9f4efd411bb1e6b74226b6cfb/.images/other/misc/squirrel/2ed-librime.1.11.2.dylib)。</span>
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
        <br><span style='padding-left:2.9em'>卸载词库也很简单，只需要将补丁行`translator/dictionary: luna_pinyin_compact`注释即可，这样原来的就会起作用。
        <br><span style='color:blue'>词库去重部分暂时没处理。另外还有 [这样: issues/214](https://github.com/rime/weasel/issues/214) 的问题</span>
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

        #### 中英混输

        <!-- panels:start -->
        <!-- div:left-panel-66 -->
        > [?] 要想在中文模式下输入英文单词，有一种办法就是将输入的编码 通过自定义的 table 类型的**英文翻译器**进行转换，只需要进行相关的配置就行。
        <br><br>具体操作如下：
        <br>`1).`: 先生成字典配置`en_dict.dict.yaml`如右所示，网上下载这两个码表[`en.dict.yaml`](https://github.com/iDvel/rime-ice/blob/4fbf48903860e3940ca5aa8eab3185881943c18f/en_dicts/en.dict.yaml)(主表)、[`en_ext.dict.yaml`](https://github.com/iDvel/rime-ice/blob/4fbf48903860e3940ca5aa8eab3185881943c18f/en_dicts/en_ext.dict.yaml)(扩展表)。
        <br><span style='padding-left:2.9em'>重命名`en.dict.yaml` ==> `en_primary.dict.yaml`
        <br>`2).`: 配置自定义文件`luna_pinyin.custom.yaml`，新增英文翻译器（table_translator@english），主要配置如右。
        <br><span style='padding-left:2.9em'>还有一个默认的主翻译器，稍后要进行调频，所以一起配置了。
        <br>`3).`: 进行调频，其实就是给两个翻译器配置中的`initial_quality`进行赋值，参数可以 [参考:优化 Rime 英文输入体验/权重设定](https://dvel.me/posts/make-rime-en-better/#权重设定)
        <br>`4).`: 重新部署，如果没问题，日志没报错，那就是没问题了，直接输入体验就行。
        <br><span style='padding-left:2.9em'>但是我的日志报错:`Error loading table for dictionary 'en_dict'.`
        <br>`5).`: 解决方式就是暂时将我们的`en_dict`配置给主翻译器，让生成 **\*.bin** 词典数据后，再换回来。
        <br><span style='padding-left:2.7em'>`5.1).`互换右边第 4 和 17 行。
        <br><span style='padding-left:2.7em'>`5.2).`重新部署一次，让生成 bin 文件，如果成功的话会在用户目录下 build 文件夹生成 **en_dict.\***等文件。
        <br><span style='padding-left:2.7em'>`5.3).`互换右边第 17 和 4 行。
        <br><span style='padding-left:2.7em'>`5.4).`再次重新部署就没问题了。
        <br><br>![](/.images/other/misc/squirrel/squirrel-config-14.gif)
        <!-- div:right-panel-33 -->
        ```yaml
        # en_dict.dict.yaml

        # Rime dictionary (encoding: utf-8)

        ---
        name: en_dict
        version: "2024.10.06"
        sort: by_weight
        use_preset_vocabulary: true
        import_tables: [ en_primary, en_ext ]
        ...
        ```
        ```yaml
        patch:
            engine/translators/@before 2/: table_translator@english
            english:
                dictionary: en_dict
                # spelling_hints: 9
                # max_phrase_length: 4
                #enable_completion: false
                #enable_sentence: false
                #initial_quality: -0.5
                enable_sentence: false
                enable_user_dict: false
                initial_quality: 1.1
                comment_format:
                    - xform/~(.*)/\[$1\]/

            translator:
                dictionary: luna_pinyin_compact
                initial_quality: 1.2
        ```
        <!-- panels:end -->

        #### 微信表情快捷输入

        > [?] 翻找表情比较麻烦，还不知道什么意思，所以利用输入法配置一下。
        <br>采用自定义符号的形式，[参考:Rime 自定義短語文件樣例](https://gist.github.com/lotem/5440677)
        <br><span style='color:blue'>需要注意：这个是微信功能支持，输入表情格式的文字会转换为对应的表情，和输入法没关系。此处的输入法只是为了将输入`liu`转化为`[666]`，`touxiao`转化为`[偷笑]`等。</span>
        <br><br>具体操作如下：
        <br>`1).`: 因为默认配置里面已经写好相关引用了，所以只需要在用户目录下新建`custom_phrase.txt`文件，将微信表情相关的符号写入到里面就可以了。符号定义如下：
        <br>如果是百度输入法，则可以 [参考:baidu输入法个性短语导入](./sticker.md#baidu输入法个性短语导入)
        <br><br>![](/.images/other/misc/squirrel/squirrel-config-13.gif)

        <details><summary>微信表情自定义符号</summary>

        ```shell
        # Rime table
        # coding: utf-8
        #@/db_name  custom_phrase.txt
        #@/db_type	tabledb
        #
        # 用於【朙月拼音】系列輸入方案
        # 【小狼毫】0.9.21 以上
        #
        # 請將該文件以UTF-8編碼保存爲
        # Rime用戶文件夾/custom_phrase.txt
        #
        # 碼表各字段以製表符（Tab）分隔
        # 順序爲：文字、編碼、權重（決定重碼的次序、可選）
        #
        # 雖然文本碼表編輯較爲方便，但不適合導入大量條目
        #
        # no comment

        中州韻輸入法引擎	rime	3
        Rime Input Method Engine	rime	2
        http://code.google.com/p/rimeime/	rime	1
        xhsgg12302@126.com 	email

        # [微信分组]
        [微笑]	weixiao	10
        [撇嘴]	piezui	10
        [色]	se	10
        [发呆]	fadai	10
        [得意]	deyi	10
        [流泪]	liulei	10
        [害羞]	haixiu	10
        [闭嘴]	bizui	10
        [睡]	shui	10
        [大哭]	daku	10

        [尴尬]	ganga	10
        [发怒]	fanu	10
        [调皮]	tiaopi	10
        [呲牙]	ciya	10
        [惊讶]	jingya	10
        [难过]	nanguo	10
        [囧]	jiong	10
        [抓狂]	zhuakuang	10
        [吐]	tu	10
        [偷笑]	touxiao	10

        [愉快]	yukuai	10
        [白眼]	baiyan	10
        [傲慢]	aoman	10
        [困]	kun	10
        [惊恐]	jingkong	10
        [憨笑]	hanxiao	10
        [悠闲]	youxian	10
        [咒骂]	zhouma	10
        [疑问]	yiwen	10
        [嘘]	xu	10

        [晕]	yun	10
        [衰]	shuai	10
        [骷髅]	kulou	10
        [敲打]	qiaoda	10
        [再见]	zaijian	10
        [擦汗]	cahan	10
        [抠鼻]	koubi	10
        [鼓掌]	guzhang	10
        [坏笑]	huaixiao	10
        [右哼哼]	youhengheng	10

        [鄙视]	bishi	10
        [委屈]	weiqu	10
        [快哭了]	kuaikule	10
        [阴险]	yinxian	10
        [亲亲]	qinqin	10
        [可怜]	kelian	10
        [笑脸]	xiaolian	10
        [生病]	shengbing	10
        [脸红]	lianhong	10
        [破涕为笑]	potiweixiao	10

        [恐惧]	kongju	10
        [失望]	shiwang	10
        [无语]	wuyu	10
        [嘿哈]	heiha	10
        [捂脸]	wulian	10
        [奸笑]	jianxiao	10
        [机智]	jizhi	10
        [皱眉]	zhoumei	10
        [耶]	ye	10
        [吃瓜]	chigua	10

        [加油]	jiayou	10
        [汗]	han	10
        [天啊]	tiana	10
        [Emm]	emm	10
        [社会社会]	shehuishehui	10
        [旺柴]	wangchai	10
        [好的]	haode	10
        [打脸]	dalian	10
        [哇]	wa	10
        [翻白眼]	fanbaiyan	10

        [666]	liu	10
        [让我看看]	rangwokankan	10
        [叹气]	tanqi	10
        [苦涩]	kuse	10
        [裂开]	liekai	10
        [嘴唇]	zuichun	10
        [爱心]	aixin	10
        [心碎]	xinsui	10
        [拥抱]	yongbao	10
        [强]	qiang	10

        [弱]	ruo	10
        [握手]	woshou	10
        [胜利]	shengli	10
        [抱拳]	baoquan	10
        [勾引]	gouyin	10
        [拳头]	quantou	10
        [OK]	ok	10
        [合十]	heshi	10
        [啤酒]	pijiu	10
        [咖啡]	cafei	10
        ```
        </details>

        #### 配置符号直接上屏(GRAVE)

        > [?] 因为写 markdown 较多的缘故，所以经常用到`` ` ``反引号，用来注释重要的内容，中英切换比较费事，所以想不管中英模式，敲`` ` ``直接上屏。
        <br>如果直接通过补丁的方式覆盖的话， 对于加了`commit`的值会报错`copy on write failed; incompatible node type: commit` [参考/issues/504](https://github.com/rime/home/issues/504)， 所以得直接复制一份共享目录下面的 **symbols.yaml** 文件到用户目录，并改名为 **symbols_updated.yaml**。 采用迂回的方式对`luna_pinyin.schema.yaml`中的 **punctuator** 直接覆盖。这样既绕过了 commit 的问题，也不用修改原来的 symbols.yaml 文件。
        <br><br>具体操作如下：
        <br>`1).`: 修改 **symbols_updated.yaml** 中的 **half_shape** 如下图第一个片段。
        <br><span style='padding-left:2.9em'>正常来说应该已经生效了，但是配置文件中还有其他影响的部分，比如 反查前缀等。所以还需要进行 2，3两步。
        <br>`2).`: 补丁覆写：`reverse_lookup/prefix: ")"` ，目前不知道这个反查是什么东西，影响不大。
        <br>`3).`: 补丁覆写：`recognizer/patterns/reverse_lookup: "R:[a-z]*'?$"`。
        <br>`4).`: 重新部署查看效果。(gif中第一次输入后出现两个`` ` ``，是 clion 的自动补全效果，后面就正常了)
        <br><br>![](/.images/other/misc/squirrel/squirrel-config-12.gif) ![](/.images/other/misc/squirrel/squirrel-config-11.png ':size=38%') 

        #### 配置回车直接上屏

        > [?] 默认的回车操作是上屏 `编辑区域`的内容，源码可以 [参考: editor.cc#L189-L218](https://github.com/rime/librime/blob/aaaaaec344c22c1b3b8059190a00e4c532a2ab54/src/rime/gear/editor.cc#L189-L218)，相关配置解释可以 [参考 Rime_description.md#八其它/第5个](https://github.com/LEOYoon-Tsaw/Rime_collections/blob/9bda87def6724bb4fa8199a6a4ae7fa964695bef/Rime_description.md#八其它)
        <br>使回车和空格一样的操作按照如下配置就行。
        <br><br>具体操作如下：
        <br>`1).`: 在`luna_pinyin.custom.yaml`中加入补丁：`editor/bindings/Return: confirm`。

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

    + ### 其他

        > [!CAUTION] `1).`: 在计算机科学和编程中，`grave` 通常指的是“重音符”或“倒尖号”，在键盘上通常位于数字 1 键的左边，形状像一个倾斜的撇号，有时也被称为反引号 `` ` ``。 --- 来自阿里通义
        <br>`2).`: `space`理解为空格，这个算是基本常识了、但是`backspace`一度懵逼，后来一查，发现是`退格键`。:stuck_out_tongue_closed_eyes:

* ## Reference
    + https://rime.im
    + https://github.com/ssnhd/rime
    + 
    + wiki
    + https://github.com/rime/home/wiki
    + https://github.com/rime/squirrel/wiki
    + 
    + [有哪些好用且开源的输入法？](https://www.zhihu.com/question/274093588)
    + [RIME v0.16.1 小狼毫輸入法（支援Win， macOS， Linux）](https://briian.com/9216/)
    + [一位匠人的中州韵——专访Rime输入法作者佛振（图灵访谈）](https://m.ituring.com.cn/article/118072)