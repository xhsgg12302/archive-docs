* ## Intro(HTTP)

    + ### 分块传输编码

        ?> Response Header 中存在 `Transfer-Encoding: chunked`的话，就是分块传输，数据需要按照[格式](https://zh.wikipedia.org/wiki/分块传输编码#格式)解码。
        <br> [参考](https://en.wikipedia.org/wiki/Chunked_transfer_encoding)

        ![](/.images/devops/network/http/http-response-chunked-encoding-01.png ':size=70%')

* ## Reference
    + https://en.wikipedia.org/wiki/Chunked_transfer_encoding