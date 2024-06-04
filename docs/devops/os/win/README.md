* ## Intro(windows)

    + ### window激活

        > [!NOTE|label:window各种版本激活]
        <br>1). 首先，查看系统的激活状态：点击桌面左下角的“Windows”按钮，从扩展面板中依次点击“设置”-“更新和安全”，并切换到“激活”选项卡，在此就可以查看到当前系统的激活状态。
        <br>2). 以管理员身份”运行cmd。输出以下命令：`slmgr.vbs /upk`复制以上命令，按回车进行确定。此时弹出窗口显未“已成功卸载了产品密钥”。
        <br>3). 接着输入以下命令：`slmgr /ipk <KMS key>` 弹出窗口提示：“成功的安装了产品密钥”。`KMS key`参见下标,例如对于**Windows 10 Pro专业版**，使用`slmgr /ipk W269N-WFGWX-YVC9B-4J6C9-T83GX`。
        <br>4). 继续输入以下命令：`slmgr /skms zh.us.to` 弹出窗口提示：“密钥管理服务计算机名成功的设置为zh.us.to”。
        <br>5). 接下来输入以下命令：`slmgr /ato` 此时将弹出窗口提示：“成功的激活了产品”。
        <br>6). 最后就可以看到当前系统的激活状态了。

        | OS | KMS key |
        | -  | -       |
        |  Windows 10 Pro专业版  | `W269N-WFGWX-YVC9B-4J6C9-T83GX` |
        |  Windows 11 Pro专业版  | `W269N-WFGWX-YVC9B-4J6C9-T83GX` |
        |  Windows 7  Enterprise企业版  | `33PXH-7Y6KF-2VJC9-XBBR8-HVTHH` [参考](https://gist.github.com/jerodg/502bd80a715347662e79af526c98f187#windows-7) |

        <!-- panels:start -->
        <!-- div:left-panel-54 -->
        ![](/.images/devops/os/windows/windows-active-01.png ':size=100%')
        <hr>

        ![](/.images/devops/os/windows/windows-active-03.png ':size=100%')
        <hr>

        ![](/.images/devops/os/windows/windows-active-04.png ':size=100%')

        <!-- div:right-panel-46 -->
        ![](/.images/devops/os/windows/windows-active-02.png ':size=100%')
        <!-- panels:end -->

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### **Index**
        <!-- div:left-panel-50 -->
        > [?] **备用激活码**
        ```shell
        # 1、专业版：
        W269N-WFGWX-YVC9B-4J6C9-T83GX
        MH37W-N47XK-V7XM9-C7227-GCQG9
        2X7P3-NGJTH-Q9TJF-8XDP9-T83GT
        J2WWN-Q4338-3GFW9-BWQVK-MG9TT
        NBQWQ-W9PTV-B4YWP-4K773-T6PKG
        236TW-X778T-8MV9F-937GT-QVKBB
        87VT2-FY2XW-F7K39-W3T8R-XMFGF
        KH2J9-PC326-T44D4-39H6V-TVPBY
        TFP9Y-VCY3P-VVH3T-8XXCC-MF4YK
        J783Y-JKQWR-677Q8-KCXTF-BHWGC
        C4M9W-WPRDG-QBB3F-VM9K8-KDQ9Y
        2VCGQ-BRVJ4-2HGJ2-K36X9-J66JG
        MGX79-TPQB9-KQ248-KXR2V-DHRTD
        FJHWT-KDGHY-K2384-93CT7-323RC

        # 2、神Key(适用各版本)：
        KH2J9-PC326-T44D4-39H6V-TVPBY
        TFP9Y-VCY3P-VVH3T-8XXCC-MF4YK
        236TW-X778T-8MV9F-937GT-QVKBB
        87VT2-FY2XW-F7K39-W3T8R-XMFGF
        6K2KY-BFH24-PJW6W-9GK29-TMPWP
        RHTBY-VWY6D-QJRJ9-JGQ3X-Q2289

        # Windows 10 Pro(win10专业版激活密钥)
        TPYNC-4J6KF-4B4GP-2HD89-7XMP6
        2BXNW-6CGWX-9BXPV-YJ996-GMT6T
        NRTT2-86GJM-T969G-8BCBH-BDWXG
        XC88X-9N9QX-CDRVP-4XV22-RVV26
        TNM78-FJKXR-P26YV-GP8MB-JK8XG
        TR8NX-K7KPD-YTRW3-XTHKX-KQBP6
        VK7JG-NPHTM-C97JM-9MPGT-3V66T
        NPPR9-FWDCX-D2C8J-H872K-2YT43
        ```
        <!-- div:right-panel-50 -->
        > [?] **激活服务器列表**
        ```shell
        kms(server:1688)
        kms.03k.org
        kms.chinancce.com
        kms.lotro.cc
        cy2617.jios.org
        kms.shuax.com
        kms.luody.info
        kms.cangshui.net
        zh.us.to
        kms.library.hk
        xykz.f3322.org
        kms.binye.xyz
        kms.tttal.com
        kms.v0v.bid
        kms.moeclub.org
        amrice.top
        kms.digiboy.ir
        kms.lolico.moe
        kms8.MSGuides.com
        kms9.MSGuides.com
        ```
        <!-- panels:end -->
        
        - #### Reference
            * https://03k.org/kms.html
            * http://kms.cangshui.net/
            * https://v0v.bid/
            * 
            * [【win10专业版激活】【win8安装密匙】](https://note.youdao.com/s/LGyAI7ir)
            * [DOC| 镜像下载](/docs/devops/os/mac/parallels-desktop.md#windows镜像)
        

* ## Reference
    + [Placeholder]()