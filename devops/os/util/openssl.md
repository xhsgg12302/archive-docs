* ## Intro(OPENSSL)

    + ### 证书类型

        > [?] 暂时记录一个 x509 ([rfc](https://www.rfc-editor.org/rfc/rfc5280.html)，[wiki](https://zh.wikipedia.org/wiki/X.509#证书组成结构))。这种格式一般广泛应用于Web浏览器和服务器之间的SSL/TLS加密通信。
        <br>对于这种证书的编码格式有 **der**, **pem(privvaccy-enhanced mail)** 等。且可以认为 pem 是 der 二进制的一种 base64编码。可以如下验证：
        <br><br>`1.` 下载网站证书并写入到文件：（不是直接下载，而是在 https 握手过程中会携带 [证书数据](/devops/network/tls-ssl/README.md#hp-certificate-实例分析)，openssl 会在这个过程中打印出来，然后我们用工具提取即可）
        <br><span style='padding-left:1.2em'>`1.1.` `openssl s_client -connect wtfu.site:443 > pubkey.pem` (运行可能会卡住，CTRL+c 终止就行)
        <br><span style='padding-left:4.2em'>查看 pubkey.pem 内容：删除`-----BEGIN CERTIFICATE-----`和`-----END CERTIFICATE-----`之外多余的内容。
        <br><span style='padding-left:1.2em'>`1.2.` 或者一步到位：`openssl s_client -connect wtfu.site:443 </dev/null 2>/dev/null | sed -n '/BEGIN CERTIFICATE/,/END CERTIFICATE/p' > pubkey.pem`
        <br><br>`2.` 转换：pem -> der，将除了第一行和最后一行的内容进行 base64 解码，然后写入文件`sed '1d;$d' pubkey.pem | base64 -d > pubkey.der`
        <br>`3.` 提取 pem 证书中的公匙并显示相关信息：`openssl x509 -in pubkey.pem -pubkey -noout | openssl ec -pubin -text -noout`
        <br>`4.` 提取 der &nbsp;&nbsp;证书中的公匙并显示相关信息：`openssl x509 -in pubkey.der -pubkey -noout | openssl ec -pubin -text -noout`
        <br><br>![](/.images/devops/os/util/openssl-cer-format-01.png ':size=100%')
        <br><br>或者也可以通过 [asn1在线解析](https://asn1js.eu/) 查看 pubkey.der 文件是否正确（DER 本质上是 [ASN.1](https://zh.wikipedia.org/wiki/ASN.1) 结构类型的）。
        <br><br>另外可以计算 sha-256 指纹(对应 chrome 浏览器证书基本信息中的值)，包括 证书和公匙
        <br>证书：`cat pubkey.der | shasum -a 256`，或者 `grep -v ^- pubkey.pem | base64 -d | shasum -a 256`。
        <br>公匙：`openssl x509 -in <pubkey.der | pubkey.pem> -pubkey -noout | grep -v ^- | base64 -d | shasum -a 256`。

        > [!CAUTION] 上述服务器`wtfu.site`当前密匙交换算法采用的是`ECDSA`，而不是 [RSA](/doc/advance/crypto/rsa.md)，读取的时候需要按照`ECDSA`的方式解析，所以后面使用了`ec`。
        
        - ###### Referene
            * https://asn1js.eu/ [ASN.1 JavaScript decoder]
            * https://stackoverflow.com/questions/22743415/what-are-the-differences-between-pem-cer-and-der

    + ### 证书指纹

        > [!TIP] chrome 浏览器查看的指纹是 sha256 算法的，腾讯云证书控制台详情显示的是 sha1 的。
        <br><br>![](/.images/devops/os/util/openssl-cer-finger-01.png ':size=100%')
        <br><br>由上面信息也可以猜测到：<span style='color:blue'>**证书的指纹** 是指对当前证书所有二进制内容的算出的信息摘要</span>。一般有 sha1，sha256 等。验证如下：获取到证书的二进制内容，然后进行哈希算出摘要。
        <br>`openssl s_client -connect static.wtfu.site:443 </dev/null 2>/dev/null | sed -n '/BEGIN CERTIFICATE/,/END CERTIFICATE/p' | grep -v ^- | base64 -d | sha1`

    + ### OPENSSL读取ssh-keygen生成公匙信息

        > [?] 简单记录一下使用 **openssl** 读取由 **ssh-keygen** 生成 [RSA](/doc/advance/crypto/rsa.md) 类型的公匙信息，当然私匙也是可以的。
        <br><br>准备公私匙数据：`ssh-keygen -t rsa -b 1024 -C 12302@wtfu.site`，填写生成文件路径 */path/to/ca/ssh*，就不加密码段（passphrase）了。
        <br>比如生成的公匙叫`ssh.pub`，私匙叫`ssh`。
        
        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        > [!NOTE|label:解析 RSA 公匙信息] `1.` 直接使用 *-e* 选项导出 PEM 格式的公匙，然后去除第一，最后一行，转码后交给 openssl rsa 解析就行：`ssh-keygen -f ssh.pub -e -m PEM | sed '1d;$d' | base64 -d | openssl rsa -pubin -modulus -text --noout`。
        <br><br>且此处的公匙文件可以用私匙文件代替，因为当前生成的私匙里包括生成公匙需要的信息。
        <br><br>
        <br><br>![](/.images/devops/os/util/openssl-ssh-key-01.png ':size=100%')
        <!-- div:right-panel-50 -->
        > [!NOTE|label:解析 RSA 私匙信息] `1.` 记得备份原来的私匙文件：`cp ssh ssh_bak`，因为后续操作会将转换后内容的对私匙进行覆写，暂时没有找到其他好办法。
        <br>`2.` 判断起始和结束标记，是否是 `RSA` 类型的，是的进行下一步，如果是 `OPENSSH` 则进行 openssh 转换，如果是其他的，我也不知道了。
        <br>**openssh 转 rsa**:`ssh-keygen -f ssh -m PEM -p`，一路回车，查看 ssh 文件开始结束标记已经变成 RSA 了。
        <br>`3.` 查看私匙信息：`openssl rsa -in ssh -text -noout | head -n 15`
        <br><br>![](/.images/devops/os/util/openssl-ssh-key-02.png ':size=100%')
        <!-- panels:end -->

    + ### OPENSSL提取公私匙信息

        ```shell
        # 生成 RSA 2048 bit 私匙
        openssl genpkey -algorithm RSA -out private_key.pem -pkeyopt rsa_keygen_bits:2048

        # 从私匙中提取公匙
        openssl rsa -pubout -in private_key.pem -out public_key.pem

        # 公匙格式转换 pem -> der
        openssl rsa -pubin -outform DER -in openssl.pem -out openssl.der

        # 查看公匙中的指数和模数( -pubin 指定输入文件是公匙，默认当私匙处理)
        openssl rsa -pubin -modulus -text -noout -in public_key.pem
        # 查看私匙中的指数和模数
        openssl rsa -modulus -text -noout -in private_key.pem

        # 查看 TLS/SSL 证书中公匙的相关信息（使用 RSA 算法）
        openssl s_client -connect <mark class='under blue'>static.wtfu.site</mark>:443 </dev/null 2>/dev/null | sed -n '/BEGIN CERTIFICATE/,/END CERTIFICATE/p' | openssl x509  -pubkey -noout | <mark class='under deeppink'>openssl rsa -pubin -modulus -text -noout</mark>

        # 查看 TLS/SSL 证书中公匙的相关信息（使用 ECDSA 算法）
        openssl s_client -connect        <mark class='under blue'>wtfu.site</mark>:443 </dev/null 2>/dev/null | sed -n '/BEGIN CERTIFICATE/,/END CERTIFICATE/p' | openssl x509  -pubkey -noout | <mark class='under deeppink'>openssl ec -pubin -text -noout</mark>
        ```

    + ### OPENSSL操作示例

        ```shell
        # 证书请求
        openssl req -x509 -days 730 -nodes -newkey rsa:2048 -outform der -keyout server.key -out ca.der -extensions v3_ca -config openssl.cnf

        # 从x509格式证书ca.der中获取公匙 -noout 表示不输出当前证书段内容
        openssl x509 -inform der -noout -pubkey -in ca.der

        # 从私匙中提取公匙并输出
        openssl rsa -pubout -in server.key

        # 验证公私匙是否匹配(mac暂不通)
        # diff -eq <(openssl x509 -inform der -noout -pubkey -in ca.der) <(openssl rsa -pubout -in server.key)
        # 验证通过的
        diff  <(openssl x509 -inform der -noout -text -in www.wtfu.site_bundle.der) <(openssl x509  -inform pem -noout -text -in www.wtfu.site_bundle.pem)

        # 查看crt内容
        openssl x509  -noout -text -in www.wtfu.site_bundle.crt
        # 查看pem内容
        openssl x509  -inform pem -noout -text -in www.wtfu.site_bundle.pem
        # pem转der
        openssl x509 -outform der [-inform pem] -in www.wtfu.site_bundle.pem -out www.wtfu.site_bundle.der
        # 查看der内容
        openssl x509 -inform der -noout -text -in www.wtfu.site_bundle.der
        # 查看csr内容
        openssl req -noout -text [-verify] -in www.wtfu.site.csr
        # 验证私匙是否有效
        openssl rsa -check -in www.wtfu.site.key
        # 验证私匙，证书，请求是否匹配
        openssl rsa -noout -modulus -in www.wtfu.site.key | openssl md5
        openssl x509 -noout -modulus -in www.wtfu.site_bundle.crt | openssl md5
        openssl req -noout -modulus -in www.wtfu.site.csr| openssl md5
        ```
    + ### ADB 添加系统证书方法

        - #### 一，cer格式证书文件计算步骤
        ```shell
        # 计算hash值；
        # .cer格式证书
        openssl x509 -inform DER -subject_hash_old -in 证书文件.cer
        导出至桌面名称成为计算出的hashvalue.0格式
        # cer格式
        openssl x509 -inform DER -text -in cer文件 > 计算结果.0
        # 修改.0证书
        # 将-----BEGIN CERTIFICATE-----移动至开头
        ```

        - #### 二， pem格式证书计算步骤
        ```shell
        # 计算hash值
        # //.pem格式证书
        openssl x509 -inform PEM -subject_hash_old -in  证书文件.pem
        # 保存
        # //pem格式
        openssl x509 -inform PEM -text -in pem证书文件 > hash值.0
        # 保存
        # 将-----BEGIN CERTIFICATE-----移动至开头
        ```
        
        - #### 三，der格式证书
        ```shell
        挂上代理后，访问https://burp下载证书，即 cacert.der.
        将证书转换为移动设备用户凭据文件
        openssl x509 -inform DER -in cacert.der -out cacert.pem
        # 回显hash
        openssl x509 -inform PEM -subject_hash_old -in cacert.pem |head -1
        # 重命名
        mv cacert.pem hashvalue.0
        ```

        - #### 四，上传至手机/system分区
        ```shell
        # 利用magisk挂载系统分区
        # 如果没权限，可以利用 /sdcard 进行中转
        adb push 9a5ba575.0 /data/adb/modules/hy/system/etc/security/cacerts/
        chmod 644 9a5ba575.0
        ```

    - ### 自签证书

        > [?] 使用 **openssl** 自签证书给 burpsuite 代理使用，并且制作 android10 系统证书，捕获手机流量(手机抓包)。
        <br>解决 burpsuite--> android (NET::ERR_CERT_VALIDITY_TOO_LONG)
        <br><br>证书摘要:
        <br>![](/.images/devops/os/util/openssl-req-01.png ':size=60%')

        > [!CAUTION] 需要注意使用的`openssl.cnf`配置文件，最终使用了`/usr/local/etc/openssl@3/openssl.cnf`。不知道是版本不对，还是修改过里面的东西，导致出现如下问题：
        <br>burpsuite 端 ： **Received fatal alert: decrypt_error**
        <br>curl 端 ：**SSL routines__colon__CRYPTO_internal:block type is not 01**、**SSL routines__colon__CONNECT_CR_KEY_EXCH:bad signature**

        ```shell
        mkdir certificates && cd certificatessudo 
        apt-get install openssl
        cp /usr/lib/ssl/openssl.cnf ./

        # pwd: cp /usr/local/etc/openssl@3/openssl.cnf ./
        # pwd: ~/Desktop/_scratch/ca/certificates

        setopt +o nomatch && rm -rf *.0 ca.der server.key server.key.der server.key.pkcs8.der && setopt -o nomatch && \
        openssl req -x509 -days 730 -nodes -newkey rsa:2048 -outform der -keyout server.key -out ca.der -extensions v3_ca -config openssl.update.cnf -batch && \
        openssl rsa -in server.key -inform pem -out server.key.der -outform der && \
        openssl pkcs8 -topk8 -in server.key.der -inform der -out server.key.pkcs8.der -outform der -nocrypt

        # Go to the proxy settings page and choose 
        # “Import / Export CA Certificate” -> “Import” -> “Certificate and private key in DER format”. 
        # The correct files to choose are `ca.der` and server.key.pkcs8.der:

        openssl x509 -inform DER -in ca.der -out ca.pem && \
        mv ca.pem "$(openssl x509 -inform PEM -subject_hash_old -in ca.pem | head -1).0"
        # openssl x509 -inform PEM -text -in *.0
        # openssl x509 -noout -fingerprint -sha1 -in ca.der
        # openssl x509 -noout -fingerprint -sha256 -in ca.der

        # https://topjohnwu.github.io/Magisk/guides.html
        adb push *.0 /sdcard/ && adb shell
        su
        mv /sdcard/*.0 /data/adb/modules/hy/system/etc/security/cacerts/ && \
        chown -R root:root /data/adb/modules/hy/system/etc/security/cacerts/ && \
        chmod 644 /data/adb/modules/hy/system/etc/security/cacerts/* && \
        ls -hal /data/adb/modules/hy/system/etc/security/cacerts/
        ```

    + ### 伪随机字节

        > [?] [openssl-rand](https://docs.openssl.org/master/man1/openssl-rand/): openssl 库可以生成位随机字节，并通过 base64 编码或这 hex 十六进制的形式打印输出，而且长度可控。基本使用如下：
        <br><br>`openssl rand -hex 32`
        <br>`openssl rand -base64 16`

    + ### 记录
        ```shell
        # 查看证书详情
        curl -v -k -q https://wtfu.site

        # 使用openssl 查看证书
        openssl s_client -connect wtfu.site:443 > cer.pem
        # 删除多余的信息，然后查看证书内容
        openssl x509 -in cer.pem -text -noout

        # 添加代理，并且写入 master secret
        openssl s_client -keylogfile ~/Desktop/tls_secrets  -proxy 127.0.0.1:7890 -connect music.163.com:443
        ```

* ## Reference

    + [Using a custom root CA with Burp for inspecting Android N traffic](https://blog.nviso.eu/2018/01/31/using-a-custom-root-ca-with-burp-for-inspecting-android-n-traffic/)
    + [Android 8.1 导入Burp、Fiddler证书](http://t.zoukankan.com/cijian9000-p-13431754.html)
    + [安卓手机使用adb添加系统证书方法](https://zhuanlan.zhihu.com/p/473750804)
    + [OpenSSL SAN 证书](https://blog.csdn.net/baishitongtian/article/details/119544269)