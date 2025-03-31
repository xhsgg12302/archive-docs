---
tags: ["self-hosted", "email", "article"]
---

* ## Intro(Maddy | 自建个人邮箱)

    > [?] [下载: maddy-0.8.1-x86_64-linux-musl.tar.zst ](https://github.com/foxcpp/maddy/releases/download/v0.8.1/maddy-0.8.1-x86_64-linux-musl.tar.zst)
    <br><br>证书操作可参考[DOCS | acme自动化管理证书](/devops/nginx/nginx.md#1使用-acmesh-自动化管理-ssltsl证书)
    <br>颁发证书：`acme.sh --issue --dns dns_tencent  -d mail.wtfu.site -d mta-sts.wtfu.site --keylength 2048`
    <br>创建目录：`mkdir -p /usr/local/nginx/conf/certificate/mail`
    <br>安装到 nginx 里面，稍后为 MTA-STS 提供验证。`acme.sh --install-cert -d mail.wtfu.site --fullchain-file /usr/local/nginx/conf/certificate/mail/full_chain.pem --key-file /usr/local/nginx/conf/certificate/mail/private.key --reloadcmd "/usr/local/nginx/sbin/nginx -s reload"`

    + ### 魔法前摇

        > [!ATTENTION|label:需要注意：|style:flat] `1.` 使用 maddy 发送非本域邮件的话，必须使用 25 端口转发，根据 [PR:767](https://github.com/foxcpp/maddy/pull/767) 中说明是 RFC 中的标准吧，配置文件中`target.remote`部分并没有可以配置可选端口的选项。所以，如果你的 VPS 厂商封锁 TCP 25端口的出口流量的话，你的自建邮箱服务器给其他邮件服务器（gmail，outlook，126之类的）就 gg 了。很不幸，此次使用的是阿里的机器，然后根据搜索说是可以 [解封](https://help.aliyun.com/document_detail/56130.html)，但是发现申请后没毛用，【安全管控｜25端口解封】处仍然是红色的**审核为通过**，此时距离发送邮件就遥不可及了。但是好在可以使用其他端口委托到 SMPT 中继服务器，继续满足我们对高潮的期待。

    + ### 邮箱服务器形态
    + ### 配置文件参悟
    + ### DNS记录

        <table>
            <thead>
                <tr> <th>用于</th> <th>主机记录</th> <th>记录类型</th> <th>记录值</th> </tr>
            </thead>
            <tbody>
                <tr> <td rowspan="3">基本记录</td> <td>mail.wtfu.site</td> <td>A</td> <td>47.94.20.18</td> </tr>
                <tr> <td>smtp.wtfu.site</td> <td>CNAME</td> <td>mail.wtfu.site</td> </tr>
                <tr> <td>wtfu.site</td> <td>MX</td> <td>mail.wtfu.site</td> </tr>
                <tr> <td><a href="#spf" rel="noopener" class="internal-link" data-src="#spf">SPF</a></td> <td>mail.wtfu.site</td> <td>TXT</td> <td>v=spf1 mx ~all</td> </tr>
                <tr> <td><a href="#dkim" rel="noopener" class="internal-link" data-src="#dkim">DKIM</a></td> <td>default._domainkey.wtfu.site</td> <td>TXT</td> <td>v=DKIM1; k=rsa; p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyDAs34 …</td> </tr>
                <tr> <td><a href="#dmarc" rel="noopener" class="internal-link" data-src="#dmarc">DMARC</a></td> <td>_dmarc.wtfu.site</td> <td>TXT</td> <td>v=DMARC1; p=quarantine; ruf=mailto:xhsgg12302@126.com</td> </tr>
                <tr> <td rowspan="3"><a href="#mta-sts" rel="noopener" class="internal-link" data-src="#mta-sts">MTA-STS</a></td> <td>_mta-sts.wtfu.site</td> <td>TXT</td> <td>v=STSv1; id=1</td> </tr> 
                <tr> <td>_smtp._tls.wtfu.site 【TLS-RPT】</td> <td>TXT</td> <td>v=TLSRPTv1;rua=mailto:xhsgg12302@126.com</td> </tr>
                <tr> <td>mta-sts.wtfu.site</td> <td>A</td> <td>47.94.20.18</td> </tr>
            </tbody>
        </table>
    + ### SMTP中继服务器使用
    + ### 邮箱评分
    + ### SMTP端口选择

        > [?] SSL/TLS和STARTTLS：
        <br>通常情况下：（SSL和TSL）放在一起都表示传输层安全加密，除了特别的协议版本指定之外，他们没有任何区别。一般工作在`465`端口，表示这个端口的流量必须加密。
        <br>另外一个是（STARTTLS）通常情况下在端口`587`发挥作用。表示可以支持 ssl/tls 加密，也可以不加密，以普通方式进行。在 RFC/3207 中有命令  [STARTTLS](https://www.rfc-editor.org/rfc/rfc3207#:~:text=The-,STARTTLS,-Command) 定义，稍后演示一下。
        <br>还有一个最为熟知的端口`25`，现在一般不允许在此处提交邮件，只针对中继服务（MTA）转发使用。（maddy 默认不让在这个端口提交邮件，但是不影响它使用 25 端口接收其他 MTA 发过来的邮件）
        <br><br>其他协议 **IMAP**，**POP3** 加密端口
        <br><span style='padding-left:1.2em'>IMAP 使用 `143` 端口，经过 SSL/TLS 加密的 IMAPS 协议使用 993 端口。
        <br><span style='padding-left:1.2em'>POP3 使用 `110` 端口，经过 SSL/TLS 加密的 POP3S 协议使用 995 端口。
        <br><br>参考：[which-smtp-port-understanding-ports-25-465-587](https://www.mailgun.com/blog/email/which-smtp-port-understanding-ports-25-465-587/)

        - #### SMTP各个端口通信过程

            <!-- tabs:start -->

            ##### **587**
            587 端口处要查看效果，使用 curl 工具并不可行，因为目前没有发送`STARTTLS`命令的选项。可以使用 telnet 命令替代，手工发送。
            <br><br>而且 126 SMTP 服务器对 587 端口进行强行加密，简直是个小菜鸡，不已它为测试对象。更换为 Mailgun（一个邮件服务商），当然使用 maddy 也是可以做到的，只不过为了演示效果需要修改 submission 中的配置 [insecure_auth](https://maddy.email/reference/endpoints/smtp/#:~:text=insecure_auth) 为 yes。

            <!-- tabs:start -->
            ###### **不用 STARTTLS 进行加密**
            <!-- panels:start -->
            可以看到不用进行加密就可以进行 **AUTH PLAIN** 命令认证，返回 334 继续。（但是菜鸡 126 就直接关闭 TCP 连接了）
            <!-- div:left-panel-50 -->
            ```shell {6,14}
            root@12302:/etc/maddy# telnet smtp.mailgun.org 587
            Trying 34.160.63.108...
            Connected to smtp.mailgun.org.
            Escape character is '^]'.
            220 Mailgun Influx ready
            ehlo 12302
            250-391ef55d98cd
            250-AUTH PLAIN LOGIN
            250-SIZE 52428800
            250-8BITMIME
            250-SMTPUTF8
            250-PIPELINING
            250 STARTTLS
            AUTH PLAIN
            334 Go ahead
            ^]
            telnet> quit
            Connection closed.
            root@12302:/etc/maddy#
            root@12302:/etc/maddy#
            root@12302:/etc/maddy#
            root@12302:/etc/maddy#
            ```
            <!-- div:right-panel-50 -->
            ```shell {6,17}
            root@12302:/etc/maddy# telnet smtp.wtfu.site 587
            Trying 47.94.20.18...
            Connected to mail.wtfu.site.
            Escape character is '^]'.
            220 mail.wtfu.site ESMTP Service Ready
            ehlo 12302
            250-Hello 12302
            250-PIPELINING
            250-8BITMIME
            250-ENHANCEDSTATUSCODES
            250-CHUNKING
            250-STARTTLS
            250-AUTH PLAIN
            250-SMTPUTF8
            250-SIZE 33554432
            250 LIMITS RCPTMAX=20000
            AUTH PLAIN
            334
            ^]
            telnet> quit
            Connection closed.
            root@12302:/etc/maddy#
            ```
            <!-- panels:end -->
            ###### **使用 STARTTLS 进行加密**
            <!-- panels:start -->
            使用 ssl/tls 的话，就需要在服务器介绍完自己后，发送 **STARTTLS** 指令开始建立加密连接了。
            <!-- div:left-panel-50 -->
            ```shell {6,14}
            root@12302:/etc/maddy# telnet smtp.mailgun.org 587
            Trying 34.149.236.64...
            Connected to smtp.mailgun.org.
            Escape character is '^]'.
            220 Mailgun Influx ready
            ehlo 12302
            250-635897b4693b
            250-AUTH PLAIN LOGIN
            250-SIZE 52428800
            250-8BITMIME
            250-SMTPUTF8
            250-PIPELINING
            250 STARTTLS
            STARTTLS
            220 Go ahead
            ^]
            telnet> quit
            Connection closed.
            root@12302:/etc/maddy#
            root@12302:/etc/maddy#
            root@12302:/etc/maddy#
            root@12302:/etc/maddy#
            ```
            <!-- div:right-panel-50 -->
            ```shell {6,17}
            root@12302:/etc/maddy# telnet smtp.wtfu.site 587
            Trying 47.94.20.18...
            Connected to mail.wtfu.site.
            Escape character is '^]'.
            220 mail.wtfu.site ESMTP Service Ready
            ehlo 12302
            250-Hello 12302
            250-PIPELINING
            250-8BITMIME
            250-ENHANCEDSTATUSCODES
            250-CHUNKING
            250-STARTTLS
            250-AUTH PLAIN
            250-SMTPUTF8
            250-SIZE 33554432
            250 LIMITS RCPTMAX=20000
            STARTTLS
            220 2.0.0 Ready to start TLS
            ^]
            telnet> quit
            Connection closed.
            root@12302:/etc/maddy#
            ```
            <!-- panels:end -->
            <!-- tabs:end -->
            
            ##### **25**
            **使用 curl 命令与 25 号端口通信**，如下以明文的方式进行。

            ```bash
            curl smtp://smtp.126.com -u 'xhsgg12302@126.com:xxxxxx' -v     
            *   Trying 123.126.97.180:25...
            * Connected to smtp.126.com (123.126.97.180) port 25 (#0)
            < 220 126.com Anti-spam GT for Coremail System (126com[20140526])
            > EHLO StevendeMacBook-Pro
            < 250-mail
            < 250-PIPELINING
            < 250-AUTH LOGIN PLAIN XOAUTH2
            < 250-AUTH=LOGIN PLAIN XOAUTH2
            < 250-coremail 1Uxr2xKj7kG0xkI17xGrU7I0s8FY2U3Uj8Cz28x1UUUUU7Ic2I0Y2UFXFzjVUCa0xDrUUUUj
            < 250-STARTTLS
            < 250-ID
            < 250 8BITMIME
            > AUTH PLAIN
            < 334 
            > base64..encode
            < 235 Authentication successful
            > HELP
            < 502 Error: command not implemented
            * Command failed: 502
            > QUIT
            < 221 Bye
            * Closing connection 0
            curl: (8) Command failed: 502
            ```

            ##### **465**
            **使用 curl 命令与 465 号端口通信**，如下打开 TCP 连接后立马开始 ssl/tls 握手简历通信，保证加密数据传输。

            ```bash
            curl smtps://smtp.126.com:465 -u 'xhsgg12302@126.com:xxxxxx' -v                     
            *   Trying 123.126.97.180:465...
            * Connected to smtp.126.com (123.126.97.180) port 465 (#0)
            *  CAfile: /etc/ssl/cert.pem
            *  CApath: none
            * [CONN-0-0][CF-SSL] (304) (OUT), TLS handshake, Client hello (1):
            * [CONN-0-0][CF-SSL] (304) (IN), TLS handshake, Server hello (2):
            * [CONN-0-0][CF-SSL] (304) (IN), TLS handshake, Unknown (8):
            * [CONN-0-0][CF-SSL] (304) (IN), TLS handshake, Certificate (11):
            * [CONN-0-0][CF-SSL] (304) (IN), TLS handshake, CERT verify (15):
            * [CONN-0-0][CF-SSL] (304) (IN), TLS handshake, Finished (20):
            * [CONN-0-0][CF-SSL] (304) (OUT), TLS handshake, Finished (20):
            * SSL connection using TLSv1.3 / AEAD-AES256-GCM-SHA384
            * Server certificate:
            *  subject: C=CN; ST=zhejiang; L=hangzhou; O=NetEase (Hangzhou) Network Co., Ltd; CN=*.126.com
            *  start date: Jan 10 00:00:00 2023 GMT
            *  expire date: Feb  7 23:59:59 2024 GMT
            *  subjectAltName: host "smtp.126.com" matched cert's "*.126.com"
            *  issuer: C=US; O=DigiCert Inc; CN=GeoTrust RSA CN CA G2
            *  SSL certificate verify ok.
            < 220 126.com Anti-spam GT for Coremail System (126com[20140526])
            > EHLO StevendeMacBook-Pro
            < 250-mail
            < 250-PIPELINING
            < 250-AUTH LOGIN PLAIN XOAUTH2
            < 250-AUTH=LOGIN PLAIN XOAUTH2
            < 250-coremail 1Uxr2xKj7kG0xkI17xGrU7I0s8FY2U3Uj8Cz28x1UUUUU7Ic2I0Y2UruCFffUCa0xDrUUUUj
            < 250-STARTTLS
            < 250-ID
            < 250 8BITMIME
            > AUTH PLAIN
            < 334 
            > base64..encode
            < 235 Authentication successful
            > HELP
            < 502 Error: command not implemented
            * Command failed: 502
            > QUIT
            < 221 Bye
            * Closing connection 0
            curl: (8) Command failed: 502
            ```
            <!-- tabs:end -->

* ## Extra

    + ### SPF

        > [!NOTE|label:下述内容来自撒谎不眨眼的AI，注意甄别] **SPF（Sender Policy Framework，发件人策略框架）** 是一种电子邮件认证机制，用于防止垃圾邮件发送者伪造合法域名发送邮件。它通过定义哪些邮件服务器被授权代表某个域名发送邮件来工作。
        <br><br>工作原理：
        <br>SPF 通过 DNS 记录来实现其功能。具体来说，域名所有者在 DNS 中发布一条 TXT 记录，该记录包含了允许代表该域名发送邮件的 IP 地址或主机名列表。当一封电子邮件到达接收方的邮件服务器时，接收方会执行以下步骤：
        <br><span style='padding-left:1.2em'>`1.`: 解析发件人的域名：从邮件的 Return-Path 或 MAIL FROM 头中提取发件人的域名。
        <br><span style='padding-left:1.2em'>`2.`: 查询 SPF 记录：基于该域名进行 DNS 查询，查找相应的 SPF TXT 记录。
        <br><span style='padding-left:1.2em'>`3.`: 验证发送者的IP地址：检查发送该邮件的服务器的 IP 地址是否包含在 SPF 记录中列出的授权服务器列表中。
        <br><span style='padding-left:1.2em'>`4.`: 根据结果采取行动：根据 SPF 验证的结果，接收方邮件服务器可以接受、拒绝或标记该邮件。
        <br><br>记录格式：
        <br>SPF 记录是一个 DNS TXT 记录，通常位于域名的根目录下。它的基本格式如下：
        <br>`v=spf1 [mechanisms] [qualifiers] -all`
        <br><span style='padding-left:1.2em'>`1.`: v=spf1：指定 SPF 版本号，目前版本为 spf1。
        <br><span style='padding-left:1.2em'>`2.`: [mechanisms]：一系列机制，定义了哪些服务器被授权发送邮件。有如下常用机制：
        <br><span style='padding-left:3.0em'>`2.1`: ip4 和 ip6：直接指定 IPv4 或 IPv6 地址。`v=spf1 ip4:192.168.0.1/24 ip6:2001:db8::/32 -all`
        <br><span style='padding-left:3.0em'>`2.2`: a：匹配 A 记录中的 IP 地址。`v=spf1 a -all`
        <br><span style='padding-left:3.0em'>`2.3`: mx：匹配 MX 记录中的 IP 地址。`v=spf1 mx -all`
        <br><span style='padding-left:3.0em'>`2.4`: include：引用另一个域名的 SPF 记录。`v=spf1 include:_spf.example.com -all`
        <br><span style='padding-left:3.0em'>`2.5`: all：匹配所有 IP 地址，通常作为 SPF 记录的最后一项。
        <br><span style='padding-left:1.2em'>`3.`: [qualifiers]：每个机制前可选的限定符，默认为 +（Pass），其他选项包括 -（Fail）、~（SoftFail）、?（Neutral）。
        <br><span style='padding-left:3.0em'>`3.1`: +：默认值，表示匹配成功（Pass）。
        <br><span style='padding-left:3.0em'>`3.2`: -：表示不匹配（Fail），接收方应拒绝邮件。
        <br><span style='padding-left:3.0em'>`3.3`: ~：表示软失败（SoftFail），接收方应将邮件标记为可疑但仍然接受。
        <br><span style='padding-left:3.0em'>`3.4`: ?：表示中立（Neutral），接收方不做特别处理。
        <br><span style='padding-left:1.2em'>`4.`: -all：表示不在上述机制中的任何 IP 地址都被视为非法。
        <br><br>SPF 配置示例:
        <br>假设你拥有一个域名 example.com，并且你的邮件服务器 IP 地址是 192.0.2.1，你还可以通过第三方邮件服务提供商发送邮件。你可以创建如下的 SPF 记录：
        <br>`v=spf1 ip4:192.0.2.1 include:_spf.thirdpartyemailprovider.com -all`
        <br>ip4:192.0.2.1：允许 IP 地址 192.0.2.1 发送邮件，include:_spf.thirdpartyemailprovider.com：允许使用第三方邮件服务提供商的服务器发送邮件，-all：拒绝所有未明确列出的服务器发送的邮件。

    + ### DKIM

        > [!NOTE|label:下述内容来自撒谎不眨眼的AI，注意甄别] **DKIM（DomainKeys Identified Mail，域名密钥识别邮件）** 是一种电子邮件安全协议，旨在确保电子邮件在传输过程中不被篡改，并验证发件人的真实性。它通过使用公钥加密技术为每封邮件添加数字签名来实现这一点。
        <br><br>工作原理：
        <br>DKIM 通过在邮件头中插入一个特殊的 DKIM-Signature 字段来工作。这个字段包含了使用私钥生成的数字签名，接收方可以通过查询 DNS 获取相应的公钥来验证该签名。具体步骤如下：
        <br><span style='padding-left:1.2em'>`1.`: 签名生成：发件方的邮件服务器使用私钥对选定的邮件部分（如从指定头字段到邮件正文的一部分）进行哈希运算，并用私钥加密生成数字签名。
        <br><span style='padding-left:1.2em'>`2.`: 签名插入：将生成的签名作为 DKIM-Signature 头部字段插入邮件中。
        <br><span style='padding-left:1.2em'>`3.`: DNS 记录发布：发件方在其 DNS 中发布一条 TXT 记录，包含用于验证签名的公钥。
        <br><span style='padding-left:1.2em'>`4.`: 签名验证：接收方的邮件服务器根据 DKIM-Signature 头中的信息获取相应的公钥，并使用该公钥验证签名的有效性。如果验证成功，则表明邮件未被篡改且确实来自声称的发件方。

        <!-- panels:start -->
        <!-- div:title-panel -->
        **DKIM 签名和 DNS 记录格式**
        <!-- div:left-panel-50 -->
        > [?] 签名头部字段 DKIM-Signature 头字段包含多个标签，每个标签由名称和值组成，以分号分隔。常见的标签包括：`v`：版本号，通常为 1。`a`：签名算法，如 rsa-sha256。`d`：发件人的域名。`s`：选择器（selector），用于区分不同的密钥。`h`：签名覆盖的头字段列表。`bh`：消息体的哈希值。`b`：实际的签名数据。
        ```shell [data-file:示例值]
        DKIM-Signature: v=1; a=rsa-sha256; d=example.com; s=brisbane;
            c=relaxed/simple; q=dns/txt; l=1234; t=1117574938; x=1118006938;
            h=from:to:subject:date;
            bh=yHBVXcELHZTTdDucHOTgZrTJ0vfoiicfY+OFxWQkKzI=;
            b=dzdVyOfAKCdLXdJOc9G2q8LoXSlEniSbav+yuU4zGeeruD00lszZVoG4ZHRNiYzR
        ```
        <!-- div:right-panel-50 -->
        > [?] DNS 记录格式：为了使接收方能够验证签名，你需要在 DNS 中发布一条 TXT 记录。记录的名称通常是 _domainkey.\<yourdomain\>，其中 \<yourdomain\> 是你的域名，而 selector 是你在签名时使用的标识符。`v=DKIM1`：DKIM 版本号。`k=rsa`：密钥类型，RSA 是最常见的类型。`p=`：公钥内容。

        ```shell [data-file:示例值]
        default._domainkey.example.com IN TXT "v=DKIM1; k=rsa; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC3QEKyU1fSma0axspqYK5iAj+54lsAg4qRRCnpKK68hawSd8zpsDz77ntPpAxykyXRrbF+Ch/4wvsrE2giJV4tO1uPHgY61EGds7B0Cz5tOYG3XCP7sbaRB1QIDAQAB"
        ```
        <!-- panels:end -->

    + ### DMARC

        > [!NOTE|label:下述内容来自撒谎不眨眼的AI，注意甄别] **DMARC（Domain-based Message Authentication, Reporting & Conformance，基于域名的消息认证、报告与一致性）** 是一种电子邮件验证协议，旨在防止电子邮件欺骗和网络钓鱼攻击。它通过整合 SPF（Sender Policy Framework）和 DKIM（DomainKeys Identified Mail）这两种现有的电子邮件认证技术，并提供报告功能来帮助域名所有者监控其域名的使用情况。
        <br><br>工作原理：
        <br>DMARC 的核心在于它定义了如何处理那些未能通过 SPF 或 DKIM 验证的邮件。此外，它还允许域名所有者请求接收方发送关于这些邮件的报告，从而更好地了解他们的域名是否被滥用。步骤解析:
        <br><span style='padding-left:1.2em'>`1.`: SPF 和 DKIM 验证：当一封邮件到达接收方的邮件服务器时，首先会进行 SPF 和 DKIM 验证。
        <br><span style='padding-left:3.0em'>`1.1`: 如果邮件通过了 SPF 验证（且 Return-Path 域名与发件人地址一致），则视为 SPF 通过。
        <br><span style='padding-left:3.0em'>`1.2`: 如果邮件通过了 DKIM 验证（签名有效且未篡改），则视为 DKIM 通过。
        <br><span style='padding-left:1.2em'>`2.`: DMARC 策略应用：接收方根据 DMARC 记录中的策略决定如何处理邮件。
        <br><span style='padding-left:3.0em'>`2.1`: **none**：不采取任何行动，仅用于收集报告。
        <br><span style='padding-left:3.0em'>`2.2`: **quarantine**：将邮件标记为可疑或放入垃圾邮件文件夹。
        <br><span style='padding-left:3.0em'>`2.3`: **reject**：直接拒绝邮件。
        <br><span style='padding-left:1.2em'>`3.`: 报告生成与发送：DMARC 支持两种类型的报告：
        <br><span style='padding-left:3.0em'>`3.1`: **Aggregate Reports（汇总报告）**：每日或每周发送给域名所有者的 XML 格式报告，包含关于通过或未通过 DMARC 检查的邮件统计信息。
        <br><span style='padding-left:3.0em'>`3.2`: **Forensic Reports（取证报告）**：即时发送给域名所有者的详细报告，包含具体未通过 DMARC 检查的邮件样本。
        <br><br>记录格式：
        <br>DMARC 记录是一个 DNS TXT 记录，通常位于 _dmarc.<yourdomain> 下。基本格式如下：
        <br>`_dmarc.example.com IN TXT "v=DMARC1; p=none; rua=mailto:dmarcreports@example.com"`
        <br><span style='padding-left:1.2em'>`1.`: v=DMARC1：指定 DMARC 版本号，目前版本为 DMARC1。
        <br><span style='padding-left:1.2em'>`2.`: p=[none|quarantine|reject]：定义 DMARC 策略，指示接收方应如何处理未通过 DMARC 检查的邮件。
        <br><span style='padding-left:3.0em'>`2.1`: none：监测模式，不对邮件做任何处理。
        <br><span style='padding-left:3.0em'>`2.2`: quarantine：将邮件标记为可疑。
        <br><span style='padding-left:3.0em'>`2.3`: reject：拒绝邮件。
        <br><span style='padding-left:1.2em'>`3.`: rua=mailto:address@example.com：指定接收汇总报告的邮箱地址。
        <br><span style='padding-left:1.2em'>`4.`: ruf=mailto:address@example.com：指定接收取证报告的邮箱地址（可选）。
        <br><span style='padding-left:1.2em'>`5.`: fo：控制何时发送取证报告（可选）。
        <br><span style='padding-left:3.0em'>`5.1`: 0：仅在全部检查失败时发送。
        <br><span style='padding-left:3.0em'>`5.2`: 1：在任意一项检查失败时发送。
        <br><span style='padding-left:3.0em'>`5.3`: d：在 DKIM 失败时发送。
        <br><span style='padding-left:3.0em'>`5.4`: s：在 SPF 失败时发送。
        <br><span style='padding-left:1.2em'>`6.`: adkim：指定 DKIM 对齐方式（可选）。r：宽松对齐（默认）。s：严格对齐。
        <br><span style='padding-left:1.2em'>`7.`: aspf：指定 SPF 对齐方式（可选）。r：宽松对齐（默认）。s：严格对齐。
        <br><br>DMARC 配置示例:
        <br>假设你拥有一个域名 example.com，并且希望开始监控但不立即阻止任何邮件，你可以创建如下的 DMARC 记录：
        <br>`_dmarc.example.com IN TXT "v=DMARC1; p=none; rua=mailto:dmarcreports@example.com; ruf=mailto:dmarcforensics@example.com; fo=1"`

    + ### MTA-STS

        > [!NOTE|label:下述内容来自撒谎不眨眼的AI，注意甄别] **MTA-STS（Mail Transfer Agent Strict Transport Security）** 是一种用于增强邮件传输安全性的机制。它确保邮件在 SMTP 服务器之间的传输使用加密连接（TLS），从而防止中间人攻击和其他形式的窃听或篡改。MTA-STS 的主要目标是：强制要求 SMTP 服务器之间通过 TLS 加密进行通信，以及提供一种方式让邮件发送方知道接收方支持 TLS，并且可以强制执行这种策略。
        <br><br>工作原理：
        <br>MTA-STS 主要依赖于两个关键组件：
        <br><span style='padding-left:1.2em'>`1.`: DNS TXT 记录：发布一个 DNS TXT 记录，声明该域支持 MTA-STS。这个记录通常包含指向 MTA-STS 策略文件的 URL。
        <br><span style='padding-left:1.2em'>`2.`: MTA-STS 策略文件：位于 HTTPS 服务器上的策略文件，定义了该域的 MTA-STS 策略。比如：https://mta-sts.example.org/.well-known/mta-sts.txt。
        <br><br>步骤解析：
        <br><span style='padding-left:1.2em'>`1.`: 查询 DNS：当发件方准备发送邮件时，会首先查询收件方域名的 DNS TXT 记录，查找 _mta-sts 子域是否存在。
        <br><span style='padding-left:1.2em'>`2.`: 获取策略文件：如果存在相应的 TXT 记录，则根据其中的信息下载并解析 MTA-STS 策略文件。
        <br><span style='padding-left:1.2em'>`3.`: 验证 TLS 连接：根据策略文件中的规则，尝试建立到收件方 SMTP 服务器的 TLS 加密连接。如果连接失败或不符合策略要求，则可能拒绝发送邮件或发出警告。
        <br><span style='padding-left:1.2em'>`4.`: 缓存策略：为了提高效率，发件方会缓存策略文件一段时间（由策略文件中的 max_age 参数决定）。在这段时间内，不需要每次都重新查询和下载策略文件。
        <br><br>**TLS-RPT（TLS Reporting）**
        <br>TLS-RPT 是一种机制，允许域所有者接收有关其 MTA-STS 配置执行情况的报告。这些报告包含了发送服务器尝试与您的 MX 服务器建立 TLS 连接时遇到的问题，比如连接失败、证书验证失败等。这对于调试和维护 MTA-STS 非常有用。

    + ### TLS-RPT

* ## Glossary

    + ### mailbox

        > [?] 邮件内容保存的格式，一般有两种`mbox`,`maildir`。

    + ### MUA

        > [?] Mail User Agent (MUA)： A mail client application used by an end user to access a mail server to read, compose, and send email messages. Common MUAs include Microsoft Outlook and Mozilla Thunderbird.
        <br><br>一种邮件客户端应用程序，最终用户使用它来访问邮件服务器，以阅读、撰写和发送电子邮件消息。常见的mua包括Microsoft Outlook和Mozilla Thunderbird，Foxmail。

    + ### MSA

        > [?] A message submission agent (MSA), or mail submission agent, is a computer program or software agent that receives electronic mail messages from a mail user agent (MUA) and cooperates with a mail transfer agent (MTA) for delivery of the mail. It uses ESMTP, a variant of the Simple Mail Transfer Protocol (SMTP), as specified in RFC 6409.[1]
        <br> Many MTAs perform the function of an MSA as well, but there are also programs that are specially designed as MSAs without full MTA functionality.[2] Historically, in Internet mail, both MTA and MSA functions use port number 25, but the official port for MSAs is 587.[1] The MTA accepts a user's incoming mail, while the MSA accepts a user's outgoing mail.
        <br><br>消息提交代理（MSA）或邮件提交代理是一种计算机程序或软件代理，它接收来自邮件用户代理（MUA）的电子邮件消息，并与邮件传输代理（MTA）合作交付邮件。它使用 ESMTP，简单邮件传输协议（SMTP）的一种变体，在RFC 6409中指定[1]
        <br>许多 MTA 也执行 MSA 的功能，但也有专门设计为 MSA 的程序，没有完整的 MTA 功能。从历史上看，在Internet邮件中，MTA和MSA功能都使用端口号25，但 MSA 的官方端口是587.[1] MTA 接收用户的接收邮件，MSA 接收用户的发送邮件。
        <br><br>`运行 MSA 程序的计算机也叫 发送邮件服务器`
        
    + ### MTA

        > [?] Mail Transfer Agent (MTA)：A program running on a mail server that receives messages from mail user agents or other MTAs and either forwards them to another MTA or, if the recipient is on the MTA, delivers the message to the local delivery agent (LDA) for delivery to the recipient. Common MTAs include Microsoft Exchange and sendmail.
        <br><br>在邮件服务器上运行的程序，该程序接收来自邮件用户代理或其他 MTA 的消息，并将其转发给另一个 MTA，或者，如果收件人在 MTA 上，则将消息传递给本地传递代理（LDA），由 LDA 传递给收件人。常见的 mta 包括 Microsoft Exchange 和 sendmail。
    
    + ### MDA / LDA

        > [?] Mail Delivery Agent (MDA)：A message delivery agent (MDA), or mail delivery agent, is a computer software component that is responsible for the delivery of e-mail messages to a local recipient's mailbox.[1] It is also called a local delivery agent (LDA). Within the Internet mail architecture, local message delivery is achieved through a process of handling messages from the message transfer agent, and storing mail into the recipient's environment (typically a mailbox).
        <br><br>消息传递代理（MDA）或邮件传递代理是一个计算机软件组件，负责将电子邮件消息传递到本地收件人的邮箱它也被称为本地配送代理（LDA）。在Internet邮件体系结构中，本地消息传递是通过处理来自消息传输代理的消息并将邮件存储到收件人的环境（通常是邮箱）的过程来实现的。
        <br><br>~实际上的 MDA 是挂载 MTA 下面的一个小程序，最主要的功能是：分析由 MTA 所收到的邮件表头，来决定这封邮件的去向。是本域的话则把邮件放到本域用户对应的 mailbox 中，不是的话，则通过 smtp 转发到对应的 MTA上去。~

    + ### MRA

        > [?] A mail retrieval agent (MRA) is a computer application that retrieves or fetches e-mail from a remote mail server and works with a mail delivery agent to deliver mail to a local or remote email mailbox.[1] MRA is an automated agent that works on behalf of the user agent checks for the new incoming mail.[2][3] MRAs may be external applications by themselves or be built into bigger applications like a mail user agent. Significant examples of standalone MRAs include fetchmail and getmail.[4][user-generated source]
        <br><br>The concept of an MRA is not standardized in email architecture. Although they operate like mail transfer agents, MRAs are technically clients when they retrieve and submit messages.
        <br><br>邮件检索代理（MRA）是一种计算机应用程序，它从远程邮件服务器检索或提取电子邮件，并与 MDA 一起将邮件发送到本地或远程电子邮件邮箱 MRA 是一个自动代理，它代表用户代理检查新传入的邮件。MRA 本身可以是外部应用程序，也可以内置到更大的应用程序（如邮件用户代理）中。独立 MRA 的重要示例包括 fetchmail 和 getmail。[4][用户生成的源
        <br><br>MRA 的概念在电子邮件架构中没有标准化。尽管它们的操作类似于邮件传输代理，但从技术上讲，MRA 在检索和提交消息时是客户机。


* ## Reference
    + DOCS
        - https://maddy.email/
        - https://csrc.nist.gov/glossary/term/mail_user_agent
        - https://en.wikipedia.org/wiki/Message_delivery_agent
        - https://www.mailgun.com/blog/email/which-smtp-port-understanding-ports-25-465-587/
        - https://peirs.net/2021/08/email-security-and-traceability/
        - https://github.com/foxcpp/maddy/pull/767
        - https://www.rfc-editor.org/rfc/rfc4409
    + 邮箱评分
        - https://www.mail-tester.com/
        - https://www.mail-tester.com/test-diwnyljwx
    + 中继SMPT服务商
        - https://www.smtp2go.com/
    + 测试SMPT服务器
        - https://smtpserver.com/smtptest