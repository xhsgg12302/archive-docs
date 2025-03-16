* ## Intro(HTTP)

    + ### 分块传输编码

        > [?] Response Header 中存在 `Transfer-Encoding: chunked`的话，就是分块传输，数据需要按照 [格式](https://zh.wikipedia.org/wiki/分块传输编码#格式) 解码。[[参考](https://en.wikipedia.org/wiki/Chunked_transfer_encoding)]
        <br><br> 应用场景：使用 socket 发送 http/https 请求的时候会返回数据报文，需要自己解析。比如：
        <br>1. 模拟[`vless客户端发送 wss + https`](https://github.com/12302-bak/idea-test-project/blob/v2.0.0-BAK/_4_springmvc/src/main/java/site/wtfu/framework/utils/TLSTest.java) 请求: 先与vless服务器创建一个通过tls加密的websocket隧道连接。
        <br>2. 发送`command(ipaddr, port)` + `握手消息`到websocket服务器，并与`目标服务器`建立连接并且发送Client Hello[一般情况下是这个]，后续让`BC库`完成握手。
        <br>3. 读取 **tls**`[BC.TlsClientProtocol]`解密后的报文，此时需要注意可能存在分块传输如下图。

        ![](/.images/devops/network/http/http-response-chunked-encoding-01.png ':size=70%')

    + ### 响应头大小写

        > [?] (参考[rfc | 7540](https://www.rfc-editor.org/rfc/rfc7540#section-8.1.2)) 在 http2 中，**响应头的 key 必须为小写**，其他的视为畸形的，但是在 http1.1 中大小写不敏感。
        <br><br>使用 curl 验证即可，一般浏览器应该会自动转化，比如 chrome 即使小写也会转化成首字母大写。
        <br>下列命令查看响应头区别：`curl -I --http1.1 https://wtfu.site`，`curl -I --http2 https://wtfu.site`

* ## Reference
    + https://en.wikipedia.org/wiki/Chunked_transfer_encoding
    + https://www.runoob.com/http/http-tutorial.html