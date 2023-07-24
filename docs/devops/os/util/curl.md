## SYNTAX
```shell
# 1、直接访问默认是get
curl http://www.example.com 
# 2、-i,--include 打印响应头
curl http://www.example.com -i 
# 3、-I,--head 大写的只会打印响应头信息
curl http://www.example.com -I
# 3、-v,--verbose 详细信息
curl http://www.example.com -v
# 4、-d,--date 请求数据 配合-X(请求方式)
curl http://www.example.com -X post -d "username=xhsgg&pwd=123456"
# 5、-b,--cookie 附带cookie信息
curl http://www.example.com -b "JSESSIONID=************;time=***"
# 6、-H,--header 请求头消息
curl http://www.example.com -H "Content-Type:application/json"
# 7、-F,--form 表单的内容，可以上传文件
curl http://www.example.com -F "file=@_path_"

# 使用curl 代替 telnet
curl telnet://wtfu.site:30000 --connect-timeout 1
```

## 实例
* ### ajax提交new from
    ![](../../../.images/../../.images/os/util/first.png ':size=100%')
    ```shell
    curl http://localhost:8080//admin/cardRule/update.html -H "Content-Type=multipart/form-data" \
        -F "action=UploadVMKImagePath" -F "id=915427308658102272" \
        -F "cardTypeId=892847836675313664" -F "carParkId=789" \
        -F 'applicableCkName="厂库A;厂库B"' -F 'applicableObjectId=undefined' -F 'amount=123.01' \
        -b "JSESSIONID=D2F46A76147799F513EA5E25F0C6BBF6" -v
    ```

* ### 请求打码服务器
    ![](../../../.images/../../.images/os/util/second.png ':size=100%')
    ```shell
    # 需要 注意  --data-urlencode 配合 @ 读取文件的话key=value 中的等号也会encode。
    # 所以换一种方式通过cat 读取。另外如果需要结合在当前命令中运行shell  需要双引号先计算。
    curl -X POST http://139.155.77.112:14306/verify/base64/  --data-urlencode "imageFile=`cat data2.txt`"
    ```
    ```shell
    # # data2.txt
    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
    ```

* ### curl -k 忽略证书授信问题
    - #### 测试
        1. 首先设置burpsuite证书不受信任。
           
           ![](../../../.images/../../.images/os/util/third.png ':size=70%')
        2. curl 验证不受信证书展示：（终止链接）
            ```shell
            curl -v -x http://127.0.0.1:7070 https://www.baidu.com 
            
            * Rebuilt URL to: https://www.baidu.com/
            *   Trying 127.0.0.1...
            * TCP_NODELAY set
            * Connected to 127.0.0.1 (127.0.0.1) port 7070 (#0)
            * Establish HTTP proxy tunnel to www.baidu.com:443
            > CONNECT www.baidu.com:443 HTTP/1.1
            > Host: www.baidu.com:443
            > User-Agent: curl/7.54.0
            > Proxy-Connection: Keep-Alive
            >
            < HTTP/1.0 200 Connection established
            <
            * Proxy replied OK to CONNECT request
            * ALPN, offering h2
            * ALPN, offering http/1.1
            * Cipher selection: ALL:!EXPORT:!EXPORT40:!EXPORT56:!aNULL:!LOW:!RC4:@STRENGTH
            * successfully set certificate verify locations:
            *   CAfile: /etc/ssl/cert.pem
            CApath: none
            * TLSv1.2 (OUT), TLS handshake, Client hello (1):
            * TLSv1.2 (IN), TLS handshake, Server hello (2):
            * TLSv1.2 (IN), TLS handshake, Certificate (11):
            * TLSv1.2 (OUT), TLS alert, Server hello (2):
            * SSL certificate problem: self signed certificate in certificate chain
            * stopped the pause stream!
            * Closing connection 0
            curl: (60) SSL certificate problem: self signed certificate in certificate chain
            More details here: https://curl.haxx.se/docs/sslcerts.html

            curl performs SSL certificate verification by default, using a "bundle"
            of Certificate Authority (CA) public keys (CA certs). If the default
            bundle file isn't adequate, you can specify an alternate file
            using the --cacert option.
            If this HTTPS server uses a certificate signed by a CA represented in
            the bundle, the certificate verification probably failed due to a
            problem with the certificate (it might be expired, or the name might
            not match the domain name in the URL).
            If you'd like to turn off curl's verification of the certificate, use
            the -k (or --insecure) option.
            HTTPS-proxy has similar options --proxy-cacert and --proxy-insecure.
            ```  
        3. curl 使用 -k 忽略不受信问题，继续使用不受信任证书访问。
            ```shell
            curl -v -k -x http://127.0.0.1:7070 https://www.baidu.com

            * Rebuilt URL to: https://www.baidu.com/
            *   Trying 127.0.0.1...
            * TCP_NODELAY set
            * Connected to 127.0.0.1 (127.0.0.1) port 7070 (#0)
            * Establish HTTP proxy tunnel to www.baidu.com:443
            > CONNECT www.baidu.com:443 HTTP/1.1
            > Host: www.baidu.com:443
            > User-Agent: curl/7.54.0
            > Proxy-Connection: Keep-Alive
            >
            < HTTP/1.0 200 Connection established
            <
            * Proxy replied OK to CONNECT request
            * ALPN, offering h2
            * ALPN, offering http/1.1
            * Cipher selection: ALL:!EXPORT:!EXPORT40:!EXPORT56:!aNULL:!LOW:!RC4:@STRENGTH
            * successfully set certificate verify locations:
            *   CAfile: /etc/ssl/cert.pem
            CApath: none
            * TLSv1.2 (OUT), TLS handshake, Client hello (1):
            * TLSv1.2 (IN), TLS handshake, Server hello (2):
            * TLSv1.2 (IN), TLS handshake, Certificate (11):
            * TLSv1.2 (IN), TLS handshake, Server key exchange (12):
            * TLSv1.2 (IN), TLS handshake, Server finished (14):
            * TLSv1.2 (OUT), TLS handshake, Client key exchange (16):
            * TLSv1.2 (OUT), TLS change cipher, Client hello (1):
            * TLSv1.2 (OUT), TLS handshake, Finished (20):
            * TLSv1.2 (IN), TLS change cipher, Client hello (1):
            * TLSv1.2 (IN), TLS handshake, Finished (20):
            * SSL connection using TLSv1.2 / ECDHE-RSA-AES256-GCM-SHA384
            * ALPN, server did not agree to a protocol
            * Server certificate:
            *  subject: C=PortSwigger; O=PortSwigger; OU=PortSwigger CA; CN=www.baidu.com
            *  start date: Oct 30 11:25:13 2014 GMT
            *  expire date: Oct 30 11:25:13 2040 GMT
            *  issuer: C=PortSwigger; ST=PortSwigger; L=PortSwigger; O=PortSwigger; OU=PortSwigger CA; CN=PortSwigger CA
            *  SSL certificate verify result: self signed certificate in certificate chain (19), continuing anyway.
            > GET / HTTP/1.1
            > Host: www.baidu.com
            > User-Agent: curl/7.54.0
            > Accept: */*
            >
            < HTTP/1.1 200 OK
            < Accept-Ranges: bytes
            < Cache-Control: private, no-cache, no-store, proxy-revalidate, no-transform
            < Content-Length: 2443
            < Content-Type: text/html
            < Date: Thu, 13 Oct 2022 12:31:27 GMT
            < Etag: "588603eb-98b"
            < Last-Modified: Mon, 23 Jan 2017 13:23:55 GMT
            < Pragma: no-cache
            < Server: bfe/1.0.8.18
            < Set-Cookie: BDORZ=27315; max-age=86400; domain=.baidu.com; path=/
            < Connection: close
            <
            <!DOCTYPE html>
            <!--STATUS OK--><html> <head><meta http-equiv=content-type content=text/html;charset=utf-8><meta http-equiv=X-UA-Compatible content=IE=Edge><meta content=always name=referrer><link rel=stylesheet type=text/css href=https://ss1.bdstatic.com/5eN1bjq8AAUYm2zgoY3K/r/www/cache/bdorz/baidu.min.css><title>百度一下，你就知道</title></head> <body link=#0000cc> <div id=wrapper> <div id=head> <div class=head_wrapper> <div class=s_form> <div class=s_form_wrapper> <div id=lg> <img hidefocus=true src=//www.baidu.com/img/bd_logo1.png width=270 height=129> </div> <form id=form name=f action=//www.baidu.com/s class=fm> <input type=hidden name=bdorz_come value=1> <input type=hidden name=ie value=utf-8> <input type=hidden name=f value=8> <input type=hidden name=rsv_bp value=1> <input type=hidden name=rsv_idx value=1> <input type=hidden name=tn value=baidu><span class="bg s_ipt_wr"><input id=kw name=wd class=s_ipt value maxlength=255 autocomplete=off autofocus=autofocus></span><span class="bg s_btn_wr"><input type=submit id=su value=百度一下 class="bg s_btn" autofocus></span> </form> </div> </div> <div id=u1> <a href=http://news.baidu.com name=tj_trnews class=mnav>新闻</a> <a href=https://www.hao123.com name=tj_trhao123 class=mnav>hao123</a> <a href=http://map.baidu.com name=tj_trmap class=mnav>地图</a> <a href=http://v.baidu.com name=tj_trvideo class=mnav>视频</a> <a href=http://tieba.baidu.com name=tj_trtieba class=mnav>贴吧</a> <noscript> <a href=http://www.baidu.com/bdorz/login.gif?login&amp;tpl=mn&amp;u=http%3A%2F%2Fwww.baidu.com%2f%3fbdorz_come%3d1 name=tj_login class=lb>登录</a> </noscript> <script>document.write('<a href="http://www.baidu.com/bdorz/login.gif?login&tpl=mn&u='+ encodeURIComponent(window.location.href+ (window.location.search === "" ? "?" : "&")+ "bdorz_come=1")+ '" name="tj_login" class="lb">登录</a>');
                            </script> <a href=//www.baidu.com/more/ name=tj_briicon class=bri style="display: block;">更多产品</a> </div> </div> </div> <div id=ftCon> <div id=ftConw> <p id=lh> <a href=http://home.baidu.com>关于百度</a> <a href=http://ir.baidu.com>About Baidu</a> </p> <p id=cp>&copy;2017&nbsp;Baidu&nbsp;<a href=http://www.baidu.com/duty/>使用百度前必读</a>&nbsp; <a href=http://jianyi.baidu.com/ class=cp-feedback>意见反馈</a>&nbsp;京ICP证030173号&nbsp; <img src=//www.baidu.com/img/gs.gif> </p> </div> </div> </div> </body> </html>
            * Closing connection 0
            * TLSv1.2 (OUT), TLS alert, Client hello (1):
            ```

    - #### 引用
        1. [数字证书概述](https://blog.csdn.net/qq_40279192/article/details/107925082)
        2. [看懂HTTPS、证书机构（CA）、证书、数字签名、私钥、公钥](https://yuhongjun.github.io/tech/2020/11/30/%E7%9C%8B%E6%87%82HTTPS-%E8%AF%81%E4%B9%A6%E6%9C%BA%E6%9E%84-CA-%E8%AF%81%E4%B9%A6-%E6%95%B0%E5%AD%97%E7%AD%BE%E5%90%8D-%E7%A7%81%E9%92%A5-%E5%85%AC%E9%92%A5.html#%E8%BA%AB%E4%BB%BD%E8%AF%86%E5%88%AB%E6%95%B0%E5%AD%97%E8%AF%81%E4%B9%A6)
        3. [HTTPS 客户端验证 服务端证书流程](http://t.zoukankan.com/kabi-p-6200434.html)
    - #### 总结
    !> 服务器证书中包含：明文信息（若干），一段CA签名（用来验证当前证书的有效性）。例如：PortSwigger的CA公匙信息在cacert.der这个证书中。如果可以解密CA签名，则表示就是PortSwigger这个证书机构签发的。也进一步说明明文信息中的服务器公匙没问题. 

* ## Reference
    * [curl 的用法指南](http://www.ruanyifeng.com/blog/2019/09/curl-reference.html)