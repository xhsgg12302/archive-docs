<!-- [toc] -->
<!-- varibale -->
[12302]:http://mvn.wtfu.site
[我的主页]:https://wtfu.site
[dojocat]: https://octodex.github.com/images/dojocat.jpg ":size=400 The Dojocat"

<h1 style="text-align:center; font-size: 50px">以下语法仅针对项目环境生效</h1>

* ## Example

    + ### 01-嵌入html

        `<font style="font-size:1.3em;font-weight:bold;"> 目录 </font>`
        <br><font style="font-size:1.3em;font-weight:bold;"> 目录 </font> 

    + ### 02-markdown语法
    
        **1). 普通文本**

        title: "post blog title"
        date: 2020-05-23

        ---

        > 2). 下面表格的形式展示各种小文本块

        |  上标：X<sub>2</sub> | 下标：O<sup>2</sup> | `文本块2` | ![favicon.ico](/.images/_media/national-emblem.png "h") |
        | - | :- | :-: | - :|
        | **粗体1** | __加粗2__ | _斜体1_  | *斜体2* |
        | ~~删除线1~~ | <s>删除线2</s> | ___斜体加粗1___ | ***斜体加粗2***  | ~~_删除斜体线_~~ |

        <hr>

        **3). 代码块**

        ```java
        public static void main(String[] args){
            String temp = "hello"
            System.out.println(temp);
        }
        ```
        ---
        **4). 任务列表**

        - [x] GFM task list 1
        - [x] GFM task list 2
        - [ ] GFM task list 3
            - [ ] GFM task list 3-1
            - [x] GFM task list 3-2
            - [ ] GFM task list 3-3
        - [ ] GFM task list 4
            - [ ] GFM task list 4-1
            - [x] GFM task list 4-2
    
        ----

        **5). 锚点链接**

        页外链接[mysql安装](/docs/doc/framework/mysql/install?id=源码安装)
        <br>页内锚点[05-emoji表示](#_05-emoji表示)

        ---
        **6). 引用变量**

        [key]:value
        <br>直接把key放入[],这样显示文本是key，链接为value
        <br>[12302] [我的主页]  ← 这里有两，只会显示第一个，估计识别成两个中括号的模式了（因为鼠标放上去显示链接是第二个变量值，两个中括号中间空格无效）
        <br>[12302]
        <br>[我的主页]

    + ### 03-图片缩放居中

        ![Stormtroopocat](https://octodex.github.com/images/stormtroopocat.jpg ":size=400x400  :align=center The Stormtroopocat")
        ![Minion](https://octodex.github.com/images/minion.png ":size=250")
        ![Alt text][dojocat]

    + ### 04 标签页tabs

        <!-- tabs:start -->
        #### **Ubuntu**
        Discover practical insights into VMware alternatives such as OpenStack and MicroCloud to transform your cloud infrastructure
        <!-- tabs:start -->
        ##### **Ubuntu:18.4**
        End of standard support for 18.04 LTS - 31 May 2023
        ##### **Ubuntu:20.4**
        嗨！你知道我们有中文站吗？立即带我去！ ›
        <!-- tabs:end -->
        #### **Centos:6.7**
        Community-driven free software effort focused around the goal of providing a rich base platform for open source communities to build upon.
        <!-- tabs:end -->

    + ### 05-Emoji表示

        > [?] [参考](https://gist.github.com/rxaviers/7360908)

        :100: :stuck_out_tongue_closed_eyes: :sweat_smile: :neckbeard: :imp: :star: :sweat_drops: :fire:
        <!-- 
            https://docsify.js.org/#/zh-cn/plugins?id=emoji
            :100:
            [参考]([/docs/devops/build/cmake?id=cmake-100)
            不需要解析emoji 
            https://github.com/docsifyjs/docsify/issues/742
        -->
    
    + ### 06-反斜杠转义

        > [?] 反斜杠 转义 `` # https://github.com/docsifyjs/docsify/issues/1881
        <br>single backslash (status: opening)

    + ### 07-段落逃逸

        > [?] 如果`end of the each li`需要跳出bullet,则需添加一空白行。需要注意空白行, 得预留一个跟外部顶格一样多的空格。比如`start of file text` 前面有四个空格,所以要添加有四个空格的空白行。
        
        * paragraph

            + inner paragraph

            start of file text

                1. li one
                2. li two

                    text of li two

            > [!NOTE] end of the each li [逃离一个]

        > [!TIP] end of the each li [逃离两个]

        * phase

            第二个段落内容

    
        > [!WARNING|label:逃离三个] 逃离段落，最后一个段落添加空行，以及新开四个空格开头的空行，然后按照格式写正文即可。

        ---

        > [?] 两个紧挨着的 `panel`，如果第一个有层级关系，比如在第二个开始之前添加前一行添加一个空格。并且添加换一个类似分割线(---)的东西。
        <!-- panels:start -->
        <!-- div:title-panel -->
        ##### Coordinating Tasks
        <!-- div:left-panel-100 -->
        1. __Handoffs__ (Each task enables, triggers, or calls next one Usually fastest but can be brittle)
        2. __Callbacks to per-handler dispatcher__
            * Sets state, attachment, etc
            * A variant of GoF Mediator pattern
        3. __Queues__ (For example, passing buffers across stages)
        4. __Futures__
            * When each task produces a result
            * Coordination layered on top of join or wait/notify

     
        ---
        <!-- panels:end -->

        <!-- panels:start -->
        <!-- div:title-panel -->
        ##### Using PooledExecutor
        <!-- div:left-panel-100 -->
        1. __A tunable worker thread pool__
        2. __Main method execute(Runnable r)__
        3. __Controls for:__
            * The kind of task queue (any Channel)
            * Maximum number of threads
            * Minimum number of threads
            * "Warm" versus on-demand threads
            * Keep-alive interval until idle threads die (to be later replaced by new ones if necessary)
            * Saturation policy (block, drop, producer-runs, etc)

     
        ---
        <!-- panels:end -->
        
    + ### 08-数学符号

        [github支持，没有空格](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/writing-mathematical-expressions)
        <br>[latex 符号支持](https://en.wikibooks.org/wiki/LaTeX/Mathematics)
        <br>[应用示例](/docs/algo/tree/03-01-AVL-tree.md)

        行内的公式${\color{red}E=mc^2}$行内的公式，行内的${\color{green}log_2N}$公式，行内的${\color{purple}(\sqrt{3x-1}+(1+x)^2)}$公式，行内的${\color{blue}\sin(\alpha)^{\theta}=\sum_{i=0}^{n}(x^i + \cos(f))}$公式
        
        多行公式
        
        $$
        \displaystyle
        \left( \sum\_{k=1}^n a\_k b\_k \right)^2
        \leq
        \left( \sum\_{k=1}^n a\_k^2 \right)
        \left( \sum\_{k=1}^n b\_k^2 \right)
        $$

        $$
        \displaystyle 
            \frac{1}{
                \Bigl(\sqrt{\phi \sqrt{5}}-\phi\Bigr) e^{
                \frac25 \pi}} = 1+\frac{e^{-2\pi}} {1+\frac{e^{-4\pi}} {
                1+\frac{e^{-6\pi}}
                {1+\frac{e^{-8\pi}}
                {1+\cdots} }
                } 
            }
        $$

        $$
        f(x) = \int_{-\infty}^\infty
            \hat f(\xi)\,e^{2 \pi i \xi x}
            \,d\xi
        $$

    + ### 09-查看mermaid版本号

        > [?] [参考](https://github.com/orgs/community/discussions/37498#discussioncomment-7152968)
        
        ```mermaid
        info
        ```

    + ### 10-面板布局

        <!-- panels:start -->
        <!-- div:title-panel -->
        ##### Hello World
        <!-- div:left-panel-40 -->
        > [?] If you are on widescreen, checkout the *right* panel, *right* there →
        
        <!-- div:right-panel-60 -->
        > [?] This is an example panel.
        <br>You can see it's usage in practice in the docs listed below:
        
        - [Fairlay API](https://fairlay.com/api)
        - [FLAP services](https://docs.flap.cloud/#/create_new_service?id=special-files)
    
    <small>please contact me if you use docsify-example-panels. i would like to display it here too.</small>
        <!-- panels:end -->

    + ### 11-文字样式

        ?> [offical site](https://github.com/fzankl/docsify-plugin-flexible-alerts)

        > [?] [offical site](https://github.com/fzankl/docsify-plugin-flexible-alerts)
        <br>hello newline

        !> hello world

        > [!] hello world tip
        <br>hello newline

        > hello
        abc

        > [!NOTE] An alert of type 'note' using global style 'callout'.

        > [!NOTE|style:flat]
        > An alert of type 'note' using alert specific style 'flat' which overrides global style 'callout'.

        > [!TIP|style:flat|label:My own heading|iconVisibility:hidden]
        > An alert of type 'tip' using alert specific style 'flat' which overrides global style 'callout'.
        > In addition, this alert uses an own heading and hides specific icon.

        > [!COMMENT]
        > An alert of type 'comment' using style 'callout' with default settings.

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        > [!TIP]
        > An alert of type 'tip' using global style 'callout'.

        > [!WARNING]
        > An alert of type 'warning' using global style 'callout'.

        > [!ATTENTION]
        > An alert of type 'attention' using global style 'callout'.

        <!-- div:right-panel-50 -->
        > [!TIP|style:flat]
        > An alert of type 'tip' using locality style 'flat'.

        > [!WARNING|style:flat]
        > An alert of type 'warning' using locality style 'flat'.

        > [!ATTENTION|style:flat]
        > An alert of type 'attention' using locality style 'flat'.
        <!-- panels:end -->

        > [?] 扩展样式1

        > [!] 扩展样式2

    + ### 12-折叠展开

        <details open>
            <summary>default open</summary>
            hello
        </details>
        <details>
            <summary>This is a super cool title</summary><!-- Good place for a CTA (Call to Action) -->
            <!-- leave an empty line *️⃣  -->
            <p>This is a super cool paragraph</p>
            <small>This is a super cool small paragraph</small>
            <b>Veni Vidi Vici</b>
        </details>
        <!-- leave an empty line *️⃣  -->

    + ### 13-在线渲染markdown和html

        | html      | https://htmlpreview.github.io/?https://github.com/openjdk/jdk/blob/jdk8-b120/hotspot/agent/doc/index.html | 
        | - | - |
        | markdown  | https://mdrender.github.io/?https://github.com/xhsgg12302/knownledges/blob/master/docs/doc/advance/jvm/tools/jvisualvm.md |

        [样例1](../doc/advance/jvm/tools/hsdb.md#reference) 
        [样例2](../doc/advance/jvm/tools/jvisualvm.md#reference) 

    + ### 14-控制p标签宽高以及代码块大小

* ## Reference

    - ### syntax
        + https://pandao.github.io/editor.md/
        + https://github.com/pandao/editor.md
        + https://marked.js.org/
    
    - ### content 
        + [404页面](https://imageranger.com/tips/how_to_resize_images/)
        + [代码行号](https://sa-token.cc/doc.html#/)
        + [更换谷歌字体](https://u.sb/css-cdn/)

**以下是github 语法**[参见](https://docs.github.com/zh/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax#alerts),好像不支持缩写书写，部分兼容docsify

* List

    > [!NOTE]
    > the style content in the indent block

> [!NOTE]
> Useful information that users should know, even when skimming content.

> [!TIP]
> Helpful advice for doing things better or more easily.

> [!IMPORTANT]
> Key information users need to know to achieve their goal.

> [!WARNING]
> Urgent info that needs immediate user attention to avoid problems.

> [!CAUTION]
> Advises about risks or negative outcomes of certain actions.