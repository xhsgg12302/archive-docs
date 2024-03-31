* ## Intro(GunPG)

    > [!NOTE]
    Key 管理是 GPG 的核心功能，GPG Key 不能简单的理解为非对称加密的 Private Key / Public Key / Key Pair。GPG Key 由如下信息组成：
    <br><br>Key ID: 该 GPG Key 的唯一标识，值为主公钥的指纹，支持多种格式(Fingerprint, Long key ID, Short key ID)，更多参见：What is a OpenPGP/GnuPG key ID?。
    <br>UID: 1 个或多个，每个 UID 由 name、email、comment 组成，email 和 comment 可以为空。
    <br>Expire: 过期时间，可以为永久。
    <br>多个具有不同用途的非对称加密算法中的 Key 的集合。

    > [!WARNING|style:flat|label:key 分类参见下表]
    <br>主秘钥和主公钥（Primary Key）、子秘钥和子公钥（Sub Key）都是成对出现的，其用途也是一致的。
    <br>每一对都包含一个 key id 属性（为 public key 的指纹），其中主密钥/主公钥的 key id 就是当前 GPG Key 的 Key ID。
    | 类型   | 全名          | 缩写 | 用途 (Usage) | 说明                                                              |
    | ------ | ------------- | ---- | ------------ | ----------------------------------------------------------------- |
    | 主公钥 | Public Key    | pub  | SC           | 每个 GPG Key 有且只有一个 主公钥，可以选择一种或多种 Usage        |
    | 子公钥 | Public Subkey | sub  | S/A/E        | 每个 GPG Key 可以有多个子公钥，每个子公钥可以选择一种或多种 Usage |
    | 主私钥 | Secret Key    | sec  | SC           | 每个 GPG Key 有且只有一个 主私钥，可以选择一种或多种 Usage        |
    | 子私钥 | Secret Subkey | ssb  | S/A/E        | 每个 GOG Key 可以有多个子私钥，每个子私钥可以选择一种或多种 Usage |

    > [!WARNING|style:flat|label:用途参见下表]
    | 缩写 | 全名           | 用途                                                 |
    | ---- | -------------- | ---------------------------------------------------- |
    | C    | Certificating  | 管理证书，如添加/删除/吊销子密钥/UID，修改过期时间。 |
    | S    | Signing        | 签名，如文件数字签名、邮件签名、Git 提交。           |
    | A    | Authenticating | 身份验证，如登录。                                   |
    | E    | Encrypting     | 加密，如文件和文本。                                 |

    + ### 安装

        ```shell
        # 1. 源码安装
        # https://www.gnupg.org/download/index.en.html
        ./configure
        make && make install

        # 2. debian/ubuntu
        sudo apt-get install gnupg

        # 3. Fedora/centos
        yum install gnupg

        # 4. macosx
        brew install gnupg
        ```

    + ### 使用

        - #### operation
        
            ```shell
            # 1. 查看公匙和私匙
            gpg -k/-K [name]

            # 2. 生成公私匙
            gpg --gen-key

            # 3. 输出密匙
            gpg --armor -o xx.txt --export [name|userID]
            gpg --armor -o xx.txt --export-secret-keys [name|userID]

            # 4. 查看公匙指纹
            gpg --fingerprint [name|userID]

            # 5. 上传公匙
            gpg --keyserver hkp://www.example.com --send-keys [name|userID]
            gpg --keyserver hkp://www.example.com --recv-keys [name|userID]

            # 6. 导入公匙
            gpg --import [file]
            ```

        - #### openvpn 样例

            ```shell
            -- 导入openvpn公匙
            wget -O security-openvpn-net.asc https://keys.openpgp.org/vks/v1/by-fingerprint/F554A3687412CFFEBDEFE0A312F5F7B42F2B01E7
            gpg --import security-openvpn-net.asc

            -- 下载签名文件
            wget -c https://swupdate.openvpn.org/community/releases/openvpn-2.5.5.tar.gz.asc

            -- 下载源文件
            wget -c https://swupdate.openvpn.org/community/releases/openvpn-2.5.5.tar.gz

            -- 验证
            gpg --verify openvpn-2.5.5.tar.gz.asc openvpn-2.5.5.tar.gz
            ```

        - #### github 验证

            ```shell
            # https://docs.github.com/en/authentication/managing-commit-signature-verification/checking-for-existing-gpg-keys
            ```

    + ### 公用服务器

        * http://keyserver.ubuntu.com/
        * https://keys.openpgp.org/
        * http://pgp.mit.edu/

* ## Reference
    + [GPG入门教程 | ruanyifeng ](http://www.ruanyifeng.com/blog/2013/07/gpg.html)
    + [openvpn | GnuPG Public Key](https://openvpn.net/community-resources/sig/ 'hello')
    + [python3.10.6 gpg](https://www.python.org/downloads/release/python-3106/)
    + https://www.rectcircle.cn/posts/understand-and-use-gpg/
    + https://www.zhihu.com/question/60520344/answer/531709554
    + 
    + [gpg: signing failed: Inappropriate ioctl for device](https://github.com/keybase/keybase-issues/issues/2798)