* ## Intro(HTTP)

    + ### 分块传输编码

        ?> Response Header 中存在 `Transfer-Encoding: chunked`的话，就是分块传输，数据需要按照[格式](https://zh.wikipedia.org/wiki/分块传输编码#格式)解码。[[参考](https://en.wikipedia.org/wiki/Chunked_transfer_encoding)].
        <br><br> 应用场景：使用 socket 发送 http/https 请求的时候会返回数据报文，需要自己解析。比如：
        <br>1. 模拟[`vless客户端发送 wss + https`](https://github.com/12302-bak/idea-test-project/blob/learning/_4_springmvc/src/main/java/site/wtfu/framework/utils/TLSTest.java) 请求: 先与vless服务器创建一个通过tls加密的websocket隧道连接。
        <br>2. 发送`command(ipaddr, port)` + `握手消息`到websocket服务器，并与`目标服务器`建立连接并且发送Client Hello[一般情况下是这个]，后续让`BC库`完成握手。
        <br>3. 读取 **tls**`[BC.TlsClientProtocol]`解密后的报文，此时需要注意可能存在分块传输如下图。

        ![](/.images/devops/network/http/http-response-chunked-encoding-01.png ':size=70%')

* ## Reference
    + https://en.wikipedia.org/wiki/Chunked_transfer_encoding