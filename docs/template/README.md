[toc]
<!-- varibale -->
[12302]:http://mvn.wtfu.site
[我的主页]:https://wtfu.site
[dojocat]: https://octodex.github.com/images/dojocat.jpg ":size=400 The Dojocat"

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
            - [ ] GFM task list 3-2
            - [ ] GFM task list 3-3
        - [ ] GFM task list 4
            - [ ] GFM task list 4-1
            - [ ] GFM task list 4-2
    
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

    + ### 03-图片缩放

        ![Minion](https://octodex.github.com/images/minion.png ":size=250")
        ![Stormtroopocat](https://octodex.github.com/images/stormtroopocat.jpg ":size=250 The Stormtroopocat")
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

        ?> [参考](https://gist.github.com/rxaviers/7360908)

        :100: :stuck_out_tongue_closed_eyes: :sweat_smile: :neckbeard: :imp: :star: :sweat_drops: :fire:
        <!-- 
            https://docsify.js.org/#/zh-cn/plugins?id=emoji
            :100:
            [参考]([/docs/devops/build/cmake?id=cmake-100)
            不需要解析emoji 
            https://github.com/docsifyjs/docsify/issues/742
        -->
    
    + ### 06-反斜杠转义

        ?> 反斜杠 转义 `` # https://github.com/docsifyjs/docsify/issues/1881
        <br>single backslash (status: opening)

    + ### 07-段落逃逸

        ?> 如果`end of the each li`需要跳出bullet,则需添加一空白行。需要注意空白行, 得预留一个跟外部顶格一样多的空格。比如`start of file text` 前面有四个空格,所以要添加有四个空格的空白行。
        
        * paragraph

            + inner paragraph

            start of file text

                1. li one
                2. li two

                    text of li two

            end of the each li

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

        ?> [参考](https://github.com/orgs/community/discussions/37498#discussioncomment-7152968)
        
        ```mermaid
        info
        ```

    + ### 10-面板布局

        <!-- panels:start -->
        <!-- div:title-panel -->
        ##### Hello World
        <!-- div:left-panel-40 -->
        ?> If you are on widescreen, checkout the *right* panel, *right* there →
        
        <!-- div:right-panel-60 -->
        ?> This is an example panel.
        <br>You can see it's usage in practice in the docs listed below:
        
        - [Fairlay API](https://fairlay.com/api)
        - [FLAP services](https://docs.flap.cloud/#/create_new_service?id=special-files)
    
    <small>please contact me if you use docsify-example-panels. i would like to display it here too.</small>
        <!-- panels:end -->

    + ### 11-文字样式

        ?> [offical site](https://github.com/fzankl/docsify-plugin-flexible-alerts)

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

        ?> 扩展样式1

        !> 扩展样式2

    + ### 12-折叠展开

        <details>
            <summary>This is a super cool title</summary><!-- Good place for a CTA (Call to Action) -->
            <!-- leave an empty line *️⃣  -->
            <p>This is a super cool paragraph</p>
            <small>This is a super cool small paragraph</small>
            <b>Veni Vidi Vici</b>
        </details>
        <!-- leave an empty line *️⃣  -->

* ## Reference

    - ### syntax
        + https://pandao.github.io/editor.md/
        + https://github.com/pandao/editor.md
        + https://marked.js.org/
    
    - ### content 
        + [404页面](https://imageranger.com/tips/how_to_resize_images/)
        + [代码行号](https://sa-token.cc/doc.html#/)
        + [更换谷歌字体](https://u.sb/css-cdn/)