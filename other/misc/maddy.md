---
tags: ["self-hosted", "email", "article"]
---

* ## Intro(Maddy | 自建邮箱)

    > [?] [下载: maddy-0.8.1-x86_64-linux-musl.tar.zst ](https://github.com/foxcpp/maddy/releases/download/v0.8.1/maddy-0.8.1-x86_64-linux-musl.tar.zst)

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

* ## Reference
    + https://maddy.email/