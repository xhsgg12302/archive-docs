+ ## adb 添加系统证书方法。
    - ### 一，cer格式证书文件计算步骤
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

    - ### 二， pem格式证书计算步骤
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
    
    - ### 三，der格式证书
    ```shell
    挂上代理后，访问https://burp下载证书，即 cacert.der.
    将证书转换为移动设备用户凭据文件
    openssl x509 -inform DER -in cacert.der -out cacert.pem
    # 回显hash
    openssl x509 -inform PEM -subject_hash_old -in cacert.pem |head -1
    # 重命名
    mv cacert.pem hashvalue.0
    ```

    - ### 四，上传至手机/system分区
    ```shell
    # 利用magisk挂载系统分区
    # 如果没权限，可以利用 /sdcard 进行中转
    adb push 9a5ba575.0 /data/adb/modules/hy/system/etc/security/cacerts/
    chmod 644 9a5ba575.0
    ```

    - ### 五，利用openssl 自定义burpsuite--> android (NET::ERR_CERT_VALIDITY_TOO_LONG)
    ```shell
    mkdir certificates && cd certificatessudo 
    apt-get install openssl
    cp /usr/lib/ssl/openssl.cnf ./

    openssl req -x509 -days 730 -nodes -newkey rsa:2048 -outform der -keyout server.key -out ca.der -extensions v3_ca -config openssl.cnf
    openssl rsa -in server.key -inform pem -out server.key.der -outform der
    openssl pkcs8 -topk8 -in server.key.der -inform der -out server.key.pkcs8.der -outform der -nocrypt

    # Go to the proxy settings page and choose 
    # “Import / Export CA Certificate” -> “Import” -> “Certificate and private key in DER format”. 
    # The correct files to choose are `ca.der` and server.key.pkcs8.der:
    ```

+ ## OPENSSL 使用样例

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

## Reference
* [Using a custom root CA with Burp for inspecting Android N traffic](https://blog.nviso.eu/2018/01/31/using-a-custom-root-ca-with-burp-for-inspecting-android-n-traffic/)
* [Android 8.1 导入Burp、Fiddler证书](http://t.zoukankan.com/cijian9000-p-13431754.html)
* [安卓手机使用adb添加系统证书方法](https://zhuanlan.zhihu.com/p/473750804)
* [OpenSSL SAN 证书](https://blog.csdn.net/baishitongtian/article/details/119544269)