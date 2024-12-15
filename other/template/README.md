---
# layout: review
tags: [template, demo]
---

<!-- [toc] -->
<!-- varibale -->
[12302]:http://mvn.wtfu.site
[我的主页]:https://wtfu.site
[dojocat]: https://octodex.github.com/images/dojocat.jpg ":size=400 The Dojocat"

<h1 style="text-align:center; font-size: 50px">以下语法仅针对项目环境生效</h1>

* ## Example

    + ### 01-嵌入html

        - #### 1).普通嵌入

            `<font style="font-size:1.3em;font-weight:bold;"> 目录 </font>`<font style="font-size:1.3em;font-weight:bold;"> 目录 </font>

        - #### 2).段落中的块元素展开折叠

            > [!] 截选自 [tls-ssl: Handshake protocol 实例分析](/devops/network/tls-ssl.md#handshake-protocol-实例分析)，点击末尾的<span style='font-size:120%;font-weight: bold;'>◐</span>查看效果
            <br><br>**TLS record**：
            <br><span style="padding-left:1em">`16`：Content type，(表示握手包)；
            <br><span style="padding-left:1em">`03 01`：Legacy version，(TLS 1.0)；
            <br><span style="padding-left:1em">`00 fd`：Length，(253)；
            <br><span style="padding-left:1em">`01 00 00 f9 03 ...... 0b 00 02 01 00`：Protocol message(s)，因为类型 **16** 是一个握手包，所以按照握手包协议进一步解析。
                <input type="checkbox" class="span toggle"><span class='content'>
                    <br><br><span style="padding-left:1em">Handshake protocol：</span>
                    <br><span style="padding-left:2em">`01`：Message type，(ClientHello)；</span>
                    <br><span style="padding-left:2em">`00 00 f9`：Handshake message data length，(249)；</span>
                    <br><span style="padding-left:2em">`03 03`：Version，(TLS 1.2 [0x0303])；</span>
                    <br><span style="padding-left:2em">`3a 67 f5 2b 44 ...... 58 28 b3 18 b4`：Random；
                        <input type="checkbox" class="span toggle"><span class='content'>
                            <br><span style="padding-left:3em">`3a 67 f5 2b`：GMT Unix Time，十进制值为 979891499；[在线转换](https://unixtime.org/) 为我们时区的值: **Fri Jan 19 2001 16:04:59 GMT+0800 (中国标准时间)** </span>
                            <br><span style="padding-left:3em">`44 11 e2 49 8e 32 6e 79 d2 ac 45 98 e8 1b 1c 74 94 3d 0d 2c 22 be 71 58 28 b3 18 b4`：Random Bytes；</span>
                        </span>
                    </span>
                    <br><span style="padding-left:2em">`20`：Session ID length，(32)；</span>
                    <br><span style="padding-left:2em">`83 ce 5a f0 a0 a9 a0 a8 1e b2 b5 42 fc 2d db 66 03 2a c0 60 4b 4e 04 e1 c4 1b 12 b2 5f d5 f1 a1`：Session ID；</span>
                    <br><span style="padding-left:2em">`00 1e`：Cipher Suites Length，(30)；</span>
                    <br><span style="padding-left:2em">`13 03 13 01 cc a9 c0 2b c0 23 c0 09 cc a8 c0 2f c0 27 c0 13 cc aa 00 9e 00 67 00 33 00 ff`：Cipher Suites，(两个字节为一组，总共 15 个)；
                        <input type="checkbox" class="span toggle"><span class='content'>
                            <br><span style="padding-left:3em">`13 03`：Cipher Suite: TLS_CHACHA20_POLY1305_SHA256 (0x1303)</span>
                            <br><span style="padding-left:3em">`13 01`：Cipher Suite: TLS_AES_128_GCM_SHA256 (0x1301)</span>
                            <br><span style="padding-left:3em">`cc a9`：Cipher Suite: TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256 (0xcca9)</span>
                            <br><span style="padding-left:3em">`c0 2b`：Cipher Suite: TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 (0xc02b)</span>
                            <br><span style="padding-left:3em">`c0 23`：Cipher Suite: TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 (0xc023)</span>
                            <br><span style="padding-left:3em">`c0 09`：Cipher Suite: TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA (0xc009)</span>
                            <br><span style="padding-left:3em">`cc a8`：Cipher Suite: TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256 (0xcca8)</span>
                            <br><span style="padding-left:3em">`c0 2f`：Cipher Suite: TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (0xc02f)</span>
                            <br><span style="padding-left:3em">`c0 27`：Cipher Suite: TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (0xc027)</span>
                            <br><span style="padding-left:3em">`c0 13`：Cipher Suite: TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (0xc013)</span>
                            <br><span style="padding-left:3em">`cc aa`：Cipher Suite: TLS_DHE_RSA_WITH_CHACHA20_POLY1305_SHA256 (0xccaa)</span>
                            <br><span style="padding-left:3em">`00 9e`：Cipher Suite: TLS_DHE_RSA_WITH_AES_128_GCM_SHA256 (0x009e)</span>
                            <br><span style="padding-left:3em">`00 67`：Cipher Suite: TLS_DHE_RSA_WITH_AES_128_CBC_SHA256 (0x0067)</span>
                            <br><span style="padding-left:3em">`00 33`：Cipher Suite: TLS_DHE_RSA_WITH_AES_128_CBC_SHA (0x0033)</span>
                            <br><span style="padding-left:3em">`00 ff`：Cipher Suite: TLS_EMPTY_RENEGOTIATION_INFO_SCSV (0x00ff)</span>
                        </span>
                    </span>
                    <br><span style="padding-left:2em">`01`：Compression Methods Length，(1)；</span>
                    <br><span style="padding-left:2em">`00`：Compression Methods，(Compression Method: null (0))；</span>
                    <br><span style="padding-left:2em">`00 92`：Extensions Length，(146)；</span>
                    <br><span style="padding-left:2em">`00 17 00 00 00 ...... 0b 00 02 01 00`：Extensions；
                        <input type="checkbox" class="span toggle"><span class='content'>
                            <br><span style="padding-left:3em">`00 17 00 00`：[Type: extended_master_secret (23) Length: 0] </span>
                            <br><span style="padding-left:3em">`00 16 00 00`：[Type: encrypt_then_mac (22) Length: 0] </span>
                            <br><span style="padding-left:3em">`00 2b 00 05 04 03 04 03 03`：[Type: supported_versions (43) Length: 5 Supported Versions length: 4，TLS 1.3 (0x0304)，TLS 1.2 (0x0303)] </span>
                            <br><span style="padding-left:3em">`00 0a 00 10 00 0e 00 1d 00 1e 00 17 00 18 01 00 01 01 01 02`：[Type: supported_groups (10) Length: 16]
                                <input type="checkbox" class="span toggle"><span class='content'>
                                    <br><span style="padding-left:4em">`00 0a`：[Type: supported_groups (10)] </span>
                                    <br><span style="padding-left:4em">`00 10`：[Length: 16] </span>
                                    <br><span style="padding-left:4em">`00 0e`：[Supported Groups List Length: 14] </span>
                                    <br><span style="padding-left:4em">`00 1d`：[Supported Group: x25519 (0x001d)] </span>
                                    <br><span style="padding-left:4em">`00 1e`：[Supported Group: x448 (0x001e)] </span>
                                    <br><span style="padding-left:4em">`00 17`：[Supported Group: secp256r1 (0x0017)] </span>
                                    <br><span style="padding-left:4em">`00 18`：[Supported Group: secp384r1 (0x0018)] </span>
                                    <br><span style="padding-left:4em">`01 00`：[Supported Group: ffdhe2048 (0x0100)] </span>
                                    <br><span style="padding-left:4em">`01 01`：[Supported Group: ffdhe3072 (0x0101)] </span>
                                    <br><span style="padding-left:4em">`01 02`：[Supported Group: ffdhe4096 (0x0102)] </span>
                                </span>
                            </span>
                            <br><span style="padding-left:3em">`00 33 00 26 00 24 00 1d 00 20 54 38 8f 34 90 97 4d ab 70 78 09 dd 48 2c 2d 70 bd f6 82 ea c1 7d 2d 29 31 3b 3d 64 27 69 93 22`：[Type: key_share (51) Length: 38]
                                <input type="checkbox" class="span toggle"><span class='content'>
                                    <br><span style="padding-left:4em">`00 33`：[Type: key_share (51)] </span>
                                    <br><span style="padding-left:4em">`00 26`：[Length: 38] </span>
                                    <br><span style="padding-left:4em">`00 24`：[Client Key Share Length: 36] </span>
                                    <br><span style="padding-left:5em">`00 1d`：[Group: x25519 (29)] </span>
                                    <br><span style="padding-left:5em">`00 20`：[Key Exchange Length: 32] </span>
                                    <br><span style="padding-left:5em">`54 38 8f 34 90 97 4d ab 70 78 09 dd 48 2c 2d 70 bd f6 82 ea c1 7d 2d 29 31 3b 3d 64 27 69 93 22`：[Key Exchange] </span>
                                </span>
                            </span>
                            <br><span style="padding-left:3em">`00 05 00 05 01 00 00 00 00`：[Type: status_request (5) Length: 5 Certificate Status Type: OCSP (1) Responder ID list Length: 0 Request Extensions Length: 0 ] </span>
                            <br><span style="padding-left:3em">`00 0d 00 30 00 2e 08 07 08 08 04 03 05 03 06 03 08 04 08 05 08 06 08 09 08 0a 08 0b 04 01 05 01 06 01 04 02 05 02 06 02 03 03 03 01 03 02 02 03 02 01 02 02`：[Type: signature_algorithms (13) Length: 48]
                                <input type="checkbox" class="span toggle"><span class='content'>
                                    <br><span style="padding-left:4em">`00 0d`：[Type: signature_algorithms (13)] </span>
                                    <br><span style="padding-left:4em">`00 30`：[Length: 48] </span>
                                    <br><span style="padding-left:4em">`00 2e`：[Signature Hash Algorithms Length: 46]
                                        <input type="checkbox" class="span toggle"><span class='content'>
                                            <br><span style="padding-left:5em">`08 07`：[Signature Algorithm: ed25519 (0x0807)] </span>
                                            <br><span style="padding-left:5em">`08 08`：[Signature Algorithm: ed448 (0x0808)] </span>
                                            <br><span style="padding-left:5em">`04 03`：[Signature Algorithm: ecdsa_secp256r1_sha256 (0x0403)] </span>
                                            <br><span style="padding-left:5em">`05 03`：[Signature Algorithm: ecdsa_secp384r1_sha384 (0x0503)] </span>
                                            <br><span style="padding-left:5em">`06 03`：[Signature Algorithm: ecdsa_secp521r1_sha512 (0x0603)] </span>
                                            <br><span style="padding-left:5em">`08 04`：[Signature Algorithm: rsa_pss_rsae_sha256 (0x0804)] </span>
                                            <br><span style="padding-left:5em">`08 05`：[Signature Algorithm: rsa_pss_rsae_sha384 (0x0805)] </span>
                                            <br><span style="padding-left:5em">`08 06`：[Signature Algorithm: rsa_pss_rsae_sha512 (0x0806)] </span>
                                            <br><span style="padding-left:5em">`08 09`：[Signature Algorithm: rsa_pss_pss_sha256 (0x0809)] </span>
                                            <br><span style="padding-left:5em">`08 0a`：[Signature Algorithm: rsa_pss_pss_sha384 (0x080a)] </span>
                                            <br><span style="padding-left:5em">`08 0b`：[Signature Algorithm: rsa_pss_pss_sha512 (0x080b)] </span>
                                            <br><span style="padding-left:5em">`04 01`：[Signature Algorithm: rsa_pkcs1_sha256 (0x0401)] </span>
                                            <br><span style="padding-left:5em">`05 01`：[Signature Algorithm: rsa_pkcs1_sha384 (0x0501)] </span>
                                            <br><span style="padding-left:5em">`06 01`：[Signature Algorithm: rsa_pkcs1_sha512 (0x0601)] </span>
                                            <br><span style="padding-left:5em">`04 02`：[Signature Algorithm: SHA256 DSA (0x0402)] </span>
                                            <br><span style="padding-left:5em">`05 02`：[Signature Algorithm: SHA384 DSA (0x0502)] </span>
                                            <br><span style="padding-left:5em">`06 02`：[Signature Algorithm: SHA512 DSA (0x0602)] </span>
                                            <br><span style="padding-left:5em">`03 03`：[Signature Algorithm: SHA224 ECDSA (0x0303)] </span>
                                            <br><span style="padding-left:5em">`03 01`：[Signature Algorithm: SHA224 RSA (0x0301)] </span>
                                            <br><span style="padding-left:5em">`03 02`：[Signature Algorithm: SHA224 DSA (0x0302)] </span>
                                            <br><span style="padding-left:5em">`02 03`：[Signature Algorithm: ecdsa_sha1 (0x0203)] </span>
                                            <br><span style="padding-left:5em">`02 01`：[Signature Algorithm: rsa_pkcs1_sha1 (0x0201)] </span>
                                            <br><span style="padding-left:5em">`02 02`：[Signature Algorithm: SHA1 DSA (0x0202)] </span>
                                        </span>
                                    </span>
                                </span>
                            </span>
                            <br><span style="padding-left:3em">`00 0b 00 02 01 00`：[Type: ec_point_formats (11) Length: 2 EC point formats Length: 1 EC point format: uncompressed (0)]
                        </span>
                    </span>
                </span>
            </span>

    + ### 02-markdown语法
    
        - #### 1). 普通文本

            title: "post blog title"
            date: 2020-05-23

        ---

        - #### 2). 下面表格的形式展示各种小文本块

            |  上标：X<sub>2</sub> | 下标：O<sup>2</sup> | `文本块2` | ![favicon.ico](/.images/_media/national-emblem.png "h") |
            | - | :- | :-: | -: |
            | **粗体1** | __加粗2__ | _斜体1_  | *斜体2* |
            | ~~删除线1~~ | <s>删除线2</s> | ___斜体加粗1___ | ***斜体加粗2***  | ~~_删除斜体线_~~ |

        <hr>

        - #### 3). 代码块

            ```java {5,14-15}
            package site.wtfu.framework;

            /**
             *
            * Copyright <a href="<mark>https://wtfu.site</mark>">...</a> Inc. All Rights Reserved.
            *
            * @date <mark class="under green">2021/11/28</mark>
            *                          @since 1.0
            *                          <mark class="clever">@author 12302</mark>
            *
            */
            public class <mark class="leaf">HelloWorld {</mark>
                public static void <mark class="box red">main(String[] args){</mark>
                    String temp = "hello";
                    temp += "world";
                    <mark class="under blue">System.out.println(temp);</mark>
                    System.out.println();
                }
            }
            ```
        
        ---
        
        - #### 4). 任务列表

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

        - #### 5). 锚点链接

            页外链接[mysql安装](/doc/framework/mysql/install.md#源码安装)
            <br>页内锚点[05-emoji表示](#05-emoji表示)

        ---

        - #### 6). 引用变量

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
        5. __A tunable worker thread pool__
        6. __Main method execute(Runnable r)__
        7. __Controls for:__
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
        <br>[应用示例](/algo/tree/03-01-AVL-tree.md)

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

        [样例1](/doc/advance/jvm/tools/hsdb.md#reference) 
        [样例2](/doc/advance/jvm/tools/jvisualvm.md#reference) 

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
        + [特殊符号](https://tw.piliapp.com/symbol/)

---

**以下是github 语法**[参见](https://docs.github.com/zh/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax#alerts),好像不支持缩进书写，部分兼容本文档（docsify | hugo）语法

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