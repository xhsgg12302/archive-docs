* ## Intro(GnuPG)

    ![](/.images/devops/os/util/gpg-intro-01.png 'https://cran.r-project.org/web/packages/gpg/vignettes/intro.html :size=70% :align=center')

    > [!TIP|style:flat]
    Key 管理是 GPG 的核心功能，GPG Key 不能简单的理解为非对称加密的 Private Key / Public Key / Key Pair。GPG Key 由如下信息组成：
    <br><br>Key ID: 该 GPG Key 的唯一标识，值为主公钥的指纹，支持多种格式(Fingerprint, Long key ID, Short key ID)，更多参见：What is a OpenPGP/GnuPG key ID?。
    <br>UID: 1 个或多个，每个 UID 由 name、email、comment 组成，email 和 comment 可以为空。
    <br>Expire: 过期时间，可以为永久。
    <br>多个具有不同用途的非对称加密算法中的 Key 的集合。

    > [!WARNING|label:key 分类参见下表]
    主秘钥和主公钥（Primary Key）、子秘钥和子公钥（Sub Key）都是成对出现的，其用途也是一致的。
    <br>每一对都包含一个 key id 属性（为 public key 的指纹），其中主密钥/主公钥的 key id 就是当前 GPG Key 的 Key ID。

    | 类型   | 全名          | 缩写 | 用途 (Usage) | 说明                                                              |
    | ------ | ------------- | ---- | ------------ | ----------------------------------------------------------------- |
    | 主公钥 | Public Key    | pub  | SC           | 每个 GPG Key 有且只有一个 主公钥，可以选择一种或多种 Usage        |
    | 子公钥 | Public Subkey | sub  | S/A/E        | 每个 GPG Key 可以有多个子公钥，每个子公钥可以选择一种或多种 Usage |
    | 主私钥 | Secret Key    | sec  | SC           | 每个 GPG Key 有且只有一个 主私钥，可以选择一种或多种 Usage        |
    | 子私钥 | Secret Subkey | ssb  | S/A/E        | 每个 GOG Key 可以有多个子私钥，每个子私钥可以选择一种或多种 Usage |

    > [!WARNING|label:用途参见下表]

    | 缩写 | 全名           | 用途                                                 |
    | ---- | -------------- | ---------------------------------------------------- |
    | C    | Certificating  | 管理证书，如添加/删除/吊销子密钥/UID，修改过期时间。 |
    | S    | Signing        | 签名，如文件数字签名、邮件签名、Git 提交。           |
    | A    | Authenticating | 身份验证，如登录。                                   |
    | E    | Encrypting     | 加密，如文件和文本。                                 |

    + ### 安装

        <!-- panels:start -->
        <!-- div:left-panel-60 -->
        <!-- tabs:start -->
        ##### **SourceCode**
        ?> # 1. 源码安装
        <br># https://www.gnupg.org/download/index.en.html
        <br>`./configure`
        <br>`make && make install`
        ##### **debian/ubuntu**
        `sudo apt-get install gnupg`
        ##### **Fedora/centos**
        `yum install gnupg`
        ##### **macosx**
        `brew install gnupg`
        <!-- tabs:end -->
        <!-- panels:end -->

    + ### 基本操作

        - #### 1. 查看公匙和私匙
        > [!TIP]
        `gpg -k/-K [--keyid-format {none|short|0xshort|long|0xlong}] [KEYID]`

        ![](/.images/devops/os/util/gpg-usage-1-01.png ':size=49%')
        ![](/.images/devops/os/util/gpg-usage-1-02.png ':size=49%')

        - #### 2. 生成与删除公私匙
        > [!TIP]
        `gpg [--generate-key | --full-generate-key]`
        <br><br>`gpg --delete-secret-keys <KEYID>` `gpg --delete-key <KEYID>`。

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ![](/.images/devops/os/util/gpg-usage-2-01.png ':size=99%')
        <hr>

        > [!ATTENTION|style:flat] 删除的时候得先删除私匙
        
        ![](/.images/devops/os/util/gpg-usage-2-03.png ':size=99%')
        <!-- div:right-panel-50 -->
        ![](/.images/devops/os/util/gpg-usage-2-02.png ':size=99%')
        <!-- panels:end -->

        - #### 3. 输出密匙
        > [!TIP]
        `gpg --armor [-o xx.txt] --export [KEYID]`
        <br>`gpg --armor [-o xx.txt] --export-secret-keys [KEYID]`

        ![](/.images/devops/os/util/gpg-usage-3-01.png ':size=47%')
        ![](/.images/devops/os/util/gpg-usage-3-02.png ':size=49%')

        - #### 4. 查看公匙指纹
        > [!TIP]
        `gpg --fingerprint [KEYID]`

        ![](/.images/devops/os/util/gpg-usage-4-01.png ':size=80%')

        - #### 5. 上传公匙
        > [!TIP]
        `gpg --keyserver hkp://keyserver.ubuntu.com --send-keys [KEYID]`
        <br>`gpg --keyserver hkps://keys.openpgp.org --recv-keys [KEYID]`
        <br><br>`--keyserver name` This option is deprecated - please use the --keyserver in ‘dirmngr.conf’ instead.

        ![](/.images/devops/os/util/gpg-usage-5-01.png ':size=80%')

        - #### 6. 导入公匙
        `gpg --import [file]`

        ![](/.images/devops/os/util/gpg-usage-6-01.png ':size=99%')

    + ### 加密签名

        > [!WARNING|style:flat|label:demo txt ]
        hello world;
        <br>最近怎么样，我正在学习gpg。

        - #### 加密
        > [!NOTE]
        `gpg --recipient 425B8CB8073AAC1EB005E4E648E1F1185160B400 --output demo.en.txt --encrypt demo.txt`

        ![](/.images/devops/os/util/gpg-en-01.png ':size=99%')

        - #### 解密
        > [!NOTE]
        `gpg --armor --decrypt demo.en.txt`

        ![](/.images/devops/os/util/gpg-de-01.png ':size=70%')

        - #### 签名
        > [!NOTE]
        `gpg --sign demo.txt`
        <br>生成 **demo.txt.gpg** 文件，这个文件默认采用二进制储存，如果想生成ASCII码的签名文件，可以使用`--clearsign`参数，生成 **demo.txt.asc** 文件
        <br>如果想生成单独的签名文件，与文件内容分开存放，可以使用`--detach-sign`参数。生成一个单独的签名文件 **demo.txt.sig** 。该文件是二进制形式的，
        <br>如果想采用ASCII码形式，要加上armor参数。`gpg --armor --detach-sign demo.txt`

        ![](/.images/devops/os/util/gpg-sign-01.png ':size=90%')

        - #### 验签
        > [!NOTE]
        `gpg --verify demo.txt.asc`
        <br>出现: **WARNING: not a detached signature; file 'demo.txt' was NOT verified!** 
        <br>是因为附近有'demo.txt'文件造成的。可以理解为这不是一个单独的签名文件。如果同级目录存在'demo.txt'，不对其进行hash验签。
        <br>可以重命名'demo.txt'或者使用`--detach-sign`来消除警告。[参考](https://sites.google.com/view/chewkeanho/guides/gnupg/verify#h.p_M_4fYCj3j-bZ)

        ![](/.images/devops/os/util/gpg-verify-01.png ':size=90%')

        - #### 签名➕加密
        > [!NOTE]
        `gpg --local-user [发信者KEYID] --recipient [接收者KEYID] --armor --sign --encrypt demo.txt`
        <br>local-user参数指定用发信者的私钥签名，recipient参数指定用接收者的公钥加密，armor参数表示采用ASCII码形式显示，
        <br>sign参数表示需要签名，encrypt参数表示指定源文件。

        ![](/.images/devops/os/util/gpg-en-sign-01.png ':size=90%')

        - #### 解密➕验签
        > [!NOTE]
        `gpg --decrypt demo.txt.asc`
        <br>将上述步骤生成的`demo.txt.asc`进行解密验签名。

        > [!ATTENTION|style:flat]
        需要注意的是：不能单独验证，可能是因为先加密的原因，需要解密后才可以看见签名，发现签名后顺带验证签名了。

        ![](/.images/devops/os/util/gpg-de-verify-01.png ':size=90%')

    + ### 其他样例

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
        * [更多参考wikipedia](https://en.wikipedia.org/wiki/Key_server_(cryptographic)#Keyserver_examples)

    + ### GnuPG套件

        > [!NOTE]
        <br> gpg有很多的辅助程序，比如`gpg-agent`,`dirmngr`。
        <br> 查看keygrip `gpg --with-keygrip -K 425B8CB8073AAC1EB005E4E648E1F1185160B400`
        <br> 预置密码 `"$(gpgconf --list-dirs libexecdir)"/gpg-preset-passphrase --preset <keygrip>`，需要在gpg-agent中开启`allow-preset-passphrase`选项。
        <br> 查看agent `gpg-connect-agent 'keyinfo --list' /bye`
        <br> 重启agent `gpg-connect-agent reloadagent /bye`

        ![](/.images/devops/os/util/gpg-suite-01.png ':size=60%')

* ## Reference

    + https://wiki.archlinuxcn.org/wiki/GnuPG
    + https://unix.stackexchange.com/questions/512173/gpg-gpg-preset-passphrase-installation
    + https://unix.stackexchange.com/questions/614737/how-to-cache-gpg-key-passphrase-with-gpg-agent-and-keychain-on-debian-10
    + https://superuser.com/questions/1539189/gpg-preset-passphrase-caching-passphrase-failed-not-supported
    + 
    + [GPG入门教程 | ruanyifeng ](http://www.ruanyifeng.com/blog/2013/07/gpg.html)
    + https://www.hacksanity.com/kb/gnupg-part-4-encrypt-sign-files/
    + https://loganmarchione.com/2015/12/a-brief-introduction-to-gpg/
    + [openvpn | GnuPG Public Key](https://openvpn.net/community-resources/sig/ 'hello')
    + [python3.10.6 gpg](https://www.python.org/downloads/release/python-3106/)
    + https://www.rectcircle.cn/posts/understand-and-use-gpg/
    + https://www.zhihu.com/question/60520344/answer/531709554
    + https://github.com/rvm/rvm/issues/4215
    + 
    + [gpg: signing failed: Inappropriate ioctl for device](https://github.com/keybase/keybase-issues/issues/2798)