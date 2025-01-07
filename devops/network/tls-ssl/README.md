* ## Intro(TLS/SSL)

    > [?] placeholder

* ## 协议规范(TLS-RECORD)

    > [?] 参考 [wiki: TLS_record](https://en.wikipedia.org/wiki/Transport_Layer_Security#TLS_record)
    <br><br>实例分析用到的 [已经记录过的数据](./tls-record-console-output.2ed.md)，一个是来自 [bctls-debug-jdk15to18](https://github.com/bcgit/bc-java/tree/1.78.1) 结合 [idea bug(Evaluate and log)](https://www.jetbrains.com/help/idea/using-breakpoints.html#log) 输出，另外一个是 wireshark 

    > [!ATTENTION|style:flat] 第一次整理好相关东西后，发现当时没有记录解密相关的东西，然后不得不重新进行第二版。所以下面实例分析中用到的数据是第二个版本。

    <!-- panels:start -->
    <!-- div:left-panel-50 -->
    > [?] TLS-Record 基本结构

    图片引用自：[7: TLS record format ](https://www.researchgate.net/figure/TLS-record-format_fig7_321347130)

    ![](/.images/devops/network/tls-ssl/tls-record-format-01.png ':size=100%')
    <!-- div:right-panel-50 -->
    > [?] 建立会话基本流程

    ![](/.images/devops/network/tls-ssl/tls-full-handshake-01.png ':size=100%')
    <!-- panels:end -->

    <!-- panels:start -->
    <hr>

    <!-- div:left-panel-40 -->
    **TLS_record 通用格式如下表**：

    <table class="wikitable" style="width:95%;text-align:center">
        <caption>TLS record format, general </caption>
        <tbody>
            <tr>
            <th scope="col">Offset </th>
            <th scope="col" style="width:22%">Byte+0 </th>
            <th scope="col" style="width:22%">Byte+1 </th>
            <th scope="col" style="width:22%">Byte+2 </th>
            <th scope="col" style="width:22%">Byte+3 </th>
            </tr>
            <tr>
            <th scope="row">Byte <br>0 </th>
            <td style="background:#dfd">Content type </td>
            <td colspan="3data-sort-value=&quot;&quot;" style="background: var(--background-color-interactive, #ececec); color: var(--color-base, inherit); vertical-align: middle; text-align: center;" class="table-na">— </td>
            </tr>
            <tr>
            <th scope="row" rowspan="2">Bytes <br>1–4 </th>
            <td colspan="2" style="background:#fdd">Legacy version </td>
            <td colspan="2" style="background:#fdd">Length </td>
            </tr>
            <tr style="background:#fdd">
            <td>
                <i>(Major)</i>
            </td>
            <td>
                <i>(Minor)</i>
            </td>
            <td>
                <i>(bits 15–8)</i>
            </td>
            <td>
                <i>(bits 7–0)</i>
            </td>
            </tr>
            <tr>
            <th scope="row">Bytes <br>5–( <i>m</i>−1) </th>
            <td colspan="4">Protocol message(s) </td>
            </tr>
            <tr>
            <th scope="row">Bytes <br>
                <i>m</i>–( <i>p</i>−1)
            </th>
            <td colspan="4" style="background:#fbb">
                <a href="https://en.wikipedia.org/wiki/Message_authentication_code" target="_blank" rel="noopener" title="Message authentication code">MAC</a> (optional)
            </td>
            </tr>
            <tr>
            <th scope="row">Bytes <br>
                <i>p</i>–( <i>q</i>−1)
            </th>
            <td colspan="4" style="background:#fbb">Padding (block ciphers only) </td>
            </tr>
        </tbody>
    </table>

    <!-- div:left-panel-25 -->
    `Content type` 如下表：

    | Hex| Dec| Type |
    | - | - | - |
    | 0x14 | 20 | [ChangeCipherSpec](#changecipherspec-protocol) |
    | 0x15 | 21 | [Alert](#alert-protocol) |
    | 0x16 | 22 | [Handshake](#handshake-protocol) |
    | 0x17 | 23 | [Application](#application-protocol) |
    | 0x18 | 24 | Heartbeat |

    <!-- div:right-panel-35 -->
    `Legacy version` 表如下：
    
    | Major version | Minor version | version type |
    | - | - | - |
    |   |   | ~SSL 1.0~ Unpublished |
    |   |   | [SSL 2.0 / rfc6176 ](https://www.rfc-editor.org/rfc/rfc6176.html) |
    | 3 | 0 | [SSL 3.0 / rfc7568 ](https://www.rfc-editor.org/rfc/rfc7568.html) |
    | 3 | 1 | [TLS 1.0 / rfc2246 ](https://www.rfc-editor.org/rfc/rfc2246.html) |
    | 3 | 2 | [TLS 1.1 / rfc4346 ](https://www.rfc-editor.org/rfc/rfc4346.html) |
    | 3 | 3 | [TLS 1.2 / rfc5246 ](https://www.rfc-editor.org/rfc/rfc5246.html) |
    | 3 | 4 | [TLS 1.3 / rfc8446 ](https://www.rfc-editor.org/rfc/rfc8446.html) |
    <!-- panels:end -->

    `Length` ：表示 **protocol message(s)**，**MAC** 和 **padding** 的长度，不超过 2<sup>14</sup> 个字节(ob100000000000000 = 16384 = 16KB)。

    `Protocol message(s)` ：一个或多个消息在这个协议字段里面定义，这个属性或许被加密依赖于当前连接的状态。

    `MAC and padding` ：在所有密码算法和参数都进行了协商和握手，并且发送一个 **CipherStateChange** 记录（见下文）来确认这些参数生效之前，不能在TLS记录的末尾出现 **MAC** 或者 **padding** 字段。

    + ### Handshake protocol

        > [!TIP] 在建立TLS会话期间交换的大多数消息都基于此记录，除非发生错误或警告，并且需要通过 [Alert 协议](#alert-protocol) 记录发出信号，或者会话的加密方式被其他记录修改（比如：[ChangeCipherSpec 协议](#changecipherspec-protocol)）。

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        <table class="wikitable" style="width:95%;text-align:center">
        <caption>TLS record format for handshake protocol </caption>
        <tbody>
            <tr>
            <th scope="col">Offset </th>
            <th scope="col" style="width:22%">Byte+0 </th>
            <th scope="col" style="width:22%">Byte+1 </th>
            <th scope="col" style="width:22%">Byte+2 </th>
            <th scope="col" style="width:22%">Byte+3 </th>
            </tr>
            <tr>
            <th scope="row">Byte <br>0 </th>
            <td style="background:#dfd">22 </td>
            <td colspan="3data-sort-value=&quot;&quot;" style="background: var(--background-color-interactive, #ececec); color: var(--color-base, inherit); vertical-align: middle; text-align: center;" class="table-na">— </td>
            </tr>
            <tr>
            <th scope="row" rowspan="2">Bytes <br>1–4 </th>
            <td colspan="2" style="background:#fdd">Legacy version </td>
            <td colspan="2" style="background:#fdd">Length </td>
            </tr>
            <tr style="background:#fdd">
            <td>
                <i>(Major)</i>
            </td>
            <td>
                <i>(Minor)</i>
            </td>
            <td>
                <i>(bits 15–8)</i>
            </td>
            <td>
                <i>(bits 7–0)</i>
            </td>
            </tr>
            <tr>
            <th scope="row" rowspan="2">Bytes <br>5–8 </th>
            <td rowspan="2">Message type </td>
            <td colspan="3">Handshake message data length </td>
            </tr>
            <tr style="font-size:90%;line-height:1.2">
            <td>
                <i>(bits 23–16)</i>
            </td>
            <td>
                <i>(bits 15–8)</i>
            </td>
            <td>
                <i>(bits 7–0)</i>
            </td>
            </tr>
            <tr>
            <th scope="row">Bytes <br>9–( <i>n</i>−1) </th>
            <td colspan="4">Handshake message data </td>
            </tr>
            <tr>
            <th scope="row" rowspan="2">Bytes <br>
                <i>n</i>–( <i>n</i>+3)
            </th>
            <td rowspan="2" style="background:#fdd">Message type </td>
            <td colspan="3" style="background:#fdd">Handshake message data length </td>
            </tr>
            <tr style="background:#fdd">
            <td>
                <i>(bits 23–16)</i>
            </td>
            <td>
                <i>(bits 15–8)</i>
            </td>
            <td>
                <i>(bits 7–0)</i>
            </td>
            </tr>
            <tr>
            <th scope="row">Bytes <br>( <i>n</i>+4)– </th>
            <td colspan="4" style="background:#fdd">Handshake message data </td>
            </tr>
        </tbody>
        </table>

        <!-- div:right-panel-50 -->
        `Message type`：如下表

        | code | description                        | delimiter | code | description        |
        | ---- | ---------------------------------- | --------- | ---- | ------------------ |
        | 0    | HelloRequest                       |           | 12   | ServerKeyExchange  |
        | 1    | ClientHello                        |           | 13   | CertificateRequest |
        | 2    | ServerHello                        |           | 14   | ServerHelloDone    |
        | 4    | NewSessionTicket                   |           | 15   | CertificateVerify  |
        | 8    | EncryptedExtensions (TLS 1.3 only) |           | 16   | ClientKeyExchange  |
        | 11   | Certificate                        |           | 20   | Finished           |

        `Length`：这个使用 3 个字节表示握手数据的长度，不包括 header。<span style='color:red'>注意：多个握手包可以包括在一个记录中。</span>
        <!-- panels:end -->

        - #### HP-ClientHello 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`16`：Content type，(表示握手包)；
            <br><span style="padding-left:1em">`03 01`：Legacy version，(TLS 1.0)；
            <br><span style="padding-left:1em">`00 fd`：Length，(253)；
            <br><span style="padding-left:1em">`01 00 00 f9 03 ...... 0b 00 02 01 00`：Protocol message(s)，因为类型 **0x16** 是一个握手包，所以按照握手包协议进一步解析。
                <input type="checkbox" checked class="span toggle"><span class='content'>
                    <br><br><span style="padding-left:1em">Handshake protocol：</span>
                    <br><span style="padding-left:2em">`01`：Message type，(ClientHello)；</span>
                    <br><span style="padding-left:2em">`00 00 f9`：Handshake message data length，(249)；</span>
                    <br><span style="padding-left:2em">`03 03`：Version，(TLS 1.2 [0x0303])；</span>
                    <br><span style="padding-left:2em">`e3 e5 0a 0c c0 ...... 05 34 d6 4b c0`：Random；
                        <input type="checkbox" class="span toggle"><span class='content'>
                            <br><span style="padding-left:3em">`e3 e5 0a 0c`：GMT Unix Time，十进制值为 3823438348；[在线转换](https://unixtime.org/) 为我们时区的值: **Wed Feb 28 2091 02:12:28 GMT+0800 (中国标准时间)** </span>
                            <br><span style="padding-left:3em">`c0 f1 f7 49 4c 14 89 45 9c 28 05 39 fa b4 e9 ce 08 77 f0 94 1f 44 4e 05 34 d6 4b c0`：Random Bytes；</span>
                        </span>
                    </span>
                    <br><span style="padding-left:2em">`20`：Session ID length，(32)；</span>
                    <br><span style="padding-left:2em">`e0 76 16 70 7f ac e1 53 5f 39 77 f3 98 b3 ff 2a 19 b6 76 08 3e 23 15 d2 10 78 c5 a8 c1 2a 65 3a`：Session ID；</span>
                    <br><span style="padding-left:2em">`00 1e`：Cipher Suites Length，(30)；</span>
                    <br><span style="padding-left:2em">`13 03 13 01 cc a9 c0 2b c0 23 c0 09 cc a8 c0 2f c0 27 c0 13 cc aa 00 9e 00 67 00 33 00 ff`：Cipher Suites，(两个字节为一组，总共 15 个)；
                        <input type="checkbox" class="span toggle"><span class='content'>
                            <br><span style="padding-left:3em">`13 03`：Cipher Suite: TLS_CHACHA20_POLY1305_SHA256 (0x1303)</span>
                            <br><span style="padding-left:3em">`13 01`：Cipher Suite: TLS_AES_128_GCM_SHA256 (0x1301)</span>
                            <br><span style="padding-left:3em">`cc a9`：Cipher Suite: TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256 (0xcca9)</span>
                            <br><span style="padding-left:3em">`c0 2b`：Cipher Suite: TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 (0xc02b)</span>
                            <br><span style="padding-left:3em">`c0 23`：Cipher Suite: TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 (0xc023)</span>
                            <br><span style="padding-left:3em">`c0 09`：Cipher Suite: TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA (0xc009)</span>
                            <br><span style="padding-left:3em">`cc a8`：Cipher Suite: TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256 (0xcca8)</span>
                            <br><span style="padding-left:3em">`c0 2f`：Cipher Suite: TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (0xc02f)</span>
                            <br><span style="padding-left:3em">`c0 27`：Cipher Suite: TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (0xc027)</span>
                            <br><span style="padding-left:3em">`c0 13`：Cipher Suite: TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (0xc013)</span>
                            <br><span style="padding-left:3em">`cc aa`：Cipher Suite: TLS_DHE_RSA_WITH_CHACHA20_POLY1305_SHA256 (0xccaa)</span>
                            <br><span style="padding-left:3em">`00 9e`：Cipher Suite: TLS_DHE_RSA_WITH_AES_128_GCM_SHA256 (0x009e)</span>
                            <br><span style="padding-left:3em">`00 67`：Cipher Suite: TLS_DHE_RSA_WITH_AES_128_CBC_SHA256 (0x0067)</span>
                            <br><span style="padding-left:3em">`00 33`：Cipher Suite: TLS_DHE_RSA_WITH_AES_128_CBC_SHA (0x0033)</span>
                            <br><span style="padding-left:3em">`00 ff`：Cipher Suite: TLS_EMPTY_RENEGOTIATION_INFO_SCSV (0x00ff)</span>
                        </span>
                    </span>
                    <br><span style="padding-left:2em">`01`：Compression Methods Length，(1)；</span>
                    <br><span style="padding-left:2em">`00`：Compression Methods，(Compression Method: null (0))；</span>
                    <br><span style="padding-left:2em">`00 92`：Extensions Length，(146)；</span>
                    <br><span style="padding-left:2em">`00 17 00 00 00 ...... 0b 00 02 01 00`：Extensions；
                        <input type="checkbox" class="span toggle"><span class='content'>
                            <br><span style="padding-left:3em">`00 17 00 00`：[Type: extended_master_secret (23) Length: 0] </span>
                            <br><span style="padding-left:3em">`00 16 00 00`：[Type: encrypt_then_mac (22) Length: 0] </span>
                            <br><span style="padding-left:3em">`00 2b 00 05 04 03 04 03 03`：[Type: supported_versions (43) Length: 5 Supported Versions length: 4，TLS 1.3 (0x0304)，TLS 1.2 (0x0303)] </span>
                            <br><span style="padding-left:3em">`00 0a 00 10 00 0e 00 1d 00 1e 00 17 00 18 01 00 01 01 01 02`：[Type: supported_groups (10) Length: 16]
                                <input type="checkbox" class="span toggle"><span class='content'>
                                    <br><span style="padding-left:4em">`00 0a`：[Type: supported_groups (10)] </span>
                                    <br><span style="padding-left:4em">`00 10`：[Length: 16] </span>
                                    <br><span style="padding-left:4em">`00 0e`：[Supported Groups List Length: 14] </span>
                                    <br><span style="padding-left:4em">`00 1d`：[Supported Group: x25519 (0x001d)] </span>
                                    <br><span style="padding-left:4em">`00 1e`：[Supported Group: x448 (0x001e)] </span>
                                    <br><span style="padding-left:4em">`00 17`：[Supported Group: secp256r1 (0x0017)] </span>
                                    <br><span style="padding-left:4em">`00 18`：[Supported Group: secp384r1 (0x0018)] </span>
                                    <br><span style="padding-left:4em">`01 00`：[Supported Group: ffdhe2048 (0x0100)] </span>
                                    <br><span style="padding-left:4em">`01 01`：[Supported Group: ffdhe3072 (0x0101)] </span>
                                    <br><span style="padding-left:4em">`01 02`：[Supported Group: ffdhe4096 (0x0102)] </span>
                                </span>
                            </span>
                            <br><span style="padding-left:3em">`00 33 00 26 00 24 00 1d 00 20 20 87 73 67 9a 1e 19 0a 9c 9e 04 b8 1d 49 60 6f a1 2e 6c 12 f0 f6 19 03 24 99 0c 77 11 ec 04 60`：[Type: key_share (51) Length: 38]
                                <input type="checkbox" class="span toggle"><span class='content'>
                                    <br><span style="padding-left:4em">`00 33`：[Type: key_share (51)] </span>
                                    <br><span style="padding-left:4em">`00 26`：[Length: 38] </span>
                                    <br><span style="padding-left:4em">`00 24`：[Client Key Share Length: 36] </span>
                                    <br><span style="padding-left:5em">`00 1d`：[Group: x25519 (29)] </span>
                                    <br><span style="padding-left:5em">`00 20`：[Key Exchange Length: 32] </span>
                                    <br><span style="padding-left:5em">`20 87 73 67 9a 1e 19 0a 9c 9e 04 b8 1d 49 60 6f a1 2e 6c 12 f0 f6 19 03 24 99 0c 77 11 ec 04 60`：[Key Exchange] </span>
                                </span>
                            </span>
                            <br><span style="padding-left:3em">`00 05 00 05 01 00 00 00 00`：[Type: status_request (5) Length: 5 Certificate Status Type: OCSP (1) Responder ID list Length: 0 Request Extensions Length: 0 ] </span>
                            <br><span style="padding-left:3em">`00 0d 00 30 00 2e 08 07 08 08 04 03 05 03 06 03 08 04 08 05 08 06 08 09 08 0a 08 0b 04 01 05 01 06 01 04 02 05 02 06 02 03 03 03 01 03 02 02 03 02 01 02 02`：[Type: signature_algorithms (13) Length: 48]
                                <input type="checkbox" class="span toggle"><span class='content'>
                                    <br><span style="padding-left:4em">`00 0d`：[Type: signature_algorithms (13)] </span>
                                    <br><span style="padding-left:4em">`00 30`：[Length: 48] </span>
                                    <br><span style="padding-left:4em">`00 2e`：[Signature Hash Algorithms Length: 46]
                                        <input type="checkbox" class="span toggle"><span class='content'>
                                            <br><span style="padding-left:5em">`08 07`：[Signature Algorithm: ed25519 (0x0807)] </span>
                                            <br><span style="padding-left:5em">`08 08`：[Signature Algorithm: ed448 (0x0808)] </span>
                                            <br><span style="padding-left:5em">`04 03`：[Signature Algorithm: ecdsa_secp256r1_sha256 (0x0403)] </span>
                                            <br><span style="padding-left:5em">`05 03`：[Signature Algorithm: ecdsa_secp384r1_sha384 (0x0503)] </span>
                                            <br><span style="padding-left:5em">`06 03`：[Signature Algorithm: ecdsa_secp521r1_sha512 (0x0603)] </span>
                                            <br><span style="padding-left:5em">`08 04`：[Signature Algorithm: rsa_pss_rsae_sha256 (0x0804)] </span>
                                            <br><span style="padding-left:5em">`08 05`：[Signature Algorithm: rsa_pss_rsae_sha384 (0x0805)] </span>
                                            <br><span style="padding-left:5em">`08 06`：[Signature Algorithm: rsa_pss_rsae_sha512 (0x0806)] </span>
                                            <br><span style="padding-left:5em">`08 09`：[Signature Algorithm: rsa_pss_pss_sha256 (0x0809)] </span>
                                            <br><span style="padding-left:5em">`08 0a`：[Signature Algorithm: rsa_pss_pss_sha384 (0x080a)] </span>
                                            <br><span style="padding-left:5em">`08 0b`：[Signature Algorithm: rsa_pss_pss_sha512 (0x080b)] </span>
                                            <br><span style="padding-left:5em">`04 01`：[Signature Algorithm: rsa_pkcs1_sha256 (0x0401)] </span>
                                            <br><span style="padding-left:5em">`05 01`：[Signature Algorithm: rsa_pkcs1_sha384 (0x0501)] </span>
                                            <br><span style="padding-left:5em">`06 01`：[Signature Algorithm: rsa_pkcs1_sha512 (0x0601)] </span>
                                            <br><span style="padding-left:5em">`04 02`：[Signature Algorithm: SHA256 DSA (0x0402)] </span>
                                            <br><span style="padding-left:5em">`05 02`：[Signature Algorithm: SHA384 DSA (0x0502)] </span>
                                            <br><span style="padding-left:5em">`06 02`：[Signature Algorithm: SHA512 DSA (0x0602)] </span>
                                            <br><span style="padding-left:5em">`03 03`：[Signature Algorithm: SHA224 ECDSA (0x0303)] </span>
                                            <br><span style="padding-left:5em">`03 01`：[Signature Algorithm: SHA224 RSA (0x0301)] </span>
                                            <br><span style="padding-left:5em">`03 02`：[Signature Algorithm: SHA224 DSA (0x0302)] </span>
                                            <br><span style="padding-left:5em">`02 03`：[Signature Algorithm: ecdsa_sha1 (0x0203)] </span>
                                            <br><span style="padding-left:5em">`02 01`：[Signature Algorithm: rsa_pkcs1_sha1 (0x0201)] </span>
                                            <br><span style="padding-left:5em">`02 02`：[Signature Algorithm: SHA1 DSA (0x0202)] </span>
                                        </span>
                                    </span>
                                </span>
                            </span>
                            <br><span style="padding-left:3em">`00 0b 00 02 01 00`：[Type: ec_point_formats (11) Length: 2 EC point formats Length: 1 EC point format: uncompressed (0)]
                        </span>
                    </span>
                </span>
            </span>

            ```markup
            <mark class='under red'>16 03 01 00 fd</mark> <mark class='box green'>01 00 00 f9</mark> 03 03 <mark class='under blue'>e3 e5 0a 0c c0 
            f1 f7 49 4c 14 89 45 9c 28 05 39 fa b4 e9 ce 08 
            77 f0 94 1f 44 4e 05 34 d6 4b c0</mark> 20 <mark class='under deeppink'>e0 76 16 70 
            7f ac e1 53 5f 39 77 f3 98 b3 ff 2a 19 b6 76 08 
            3e 23 15 d2 10 78 c5 a8 c1 2a 65 3a</mark> 00 1e <mark class='under chartreuse'>13 03 
            13 01 cc a9 c0 2b c0 23 c0 09 cc a8 c0 2f c0 27 
            c0 13 cc aa 00 9e 00 67 00 33 00 ff</mark> 01 00 <mark class='box red'>00 92</mark>
            <mark class='under dodgerblue'>00 17 00 00</mark> <mark class='under chocolate'>00 16 00 00</mark> <mark class='under dodgerblue'>00 2b 00 05 04 03 04 03 
            03</mark> <mark class='under chocolate'>00 0a 00 10 00 0e 00 1d 00 1e 00 17 00 18 01 
            00 01 01 01 02</mark> <mark class='under dodgerblue'>00 33 00 26 00 24 00 1d 00 20 20 
            87 73 67 9a 1e 19 0a 9c 9e 04 b8 1d 49 60 6f a1 
            2e 6c 12 f0 f6 19 03 24 99 0c 77 11 ec 04 60</mark> <mark class='under chocolate'>00 
            05 00 05 01 00 00 00 00</mark> <mark class='under dodgerblue'>00 0d 00 30 00 2e 08 07 
            08 08 04 03 05 03 06 03 08 04 08 05 08 06 08 09 
            08 0a 08 0b 04 01 05 01 06 01 04 02 05 02 06 02 
            03 03 03 01 03 02 02 03 02 01 02 02</mark> <mark class='under chocolate'>00 0b 00 02 
            01 00</mark>
            ```

        - #### HP-ServerHello 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`16`：Content type，(表示握手包)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`00 59`：Length，(89)；
            <br><span style="padding-left:1em">`02 00 00 55 03 ...... 04 03 00 01 02`：Protocol message(s)，因为类型 **0x16** 是一个握手包，所以按照握手包协议进一步解析。
                <input type="checkbox" checked class="span toggle"><span class='content'>
                    <br><br><span style="padding-left:1em">Handshake protocol：</span>
                    <br><span style="padding-left:2em">`02`：Message type，(ServerHello)；</span>
                    <br><span style="padding-left:2em">`00 00 55`：Handshake message data length，(85)；</span>
                    <br><span style="padding-left:2em">`03 03`：Version，(TLS 1.2 [0x0303])；</span>
                    <br><span style="padding-left:2em">`52 f6 1e a4 6d ...... 8f 28 5b e9 fe`：Random；
                        <input type="checkbox" checked class="span toggle"><span class='content'>
                            <br><span style="padding-left:3em">`52 f6 1e a4`：GMT Unix Time，十进制值为 1391861412； [在线转换](https://unixtime.org) 为我们时区的值: **Sat Feb 08 2014 20:10:12 GMT+0800 (中国标准时间)**；</span>
                            <br><span style="padding-left:3em">`6d 79 2e 2b 1b 94 70 d7 eb 21 5d 42 ed 4b a3 44 88 45 f2 b8 c1 4b 63 8f 28 5b e9 fe`：Random Bytes；</span>
                        </span>
                    </span>
                    <br><span style="padding-left:2em">`20`：Session ID length，(32)；</span>
                    <br><span style="padding-left:2em">`0a 0e a0 a9 e9 88 4d 2d 07 c1 54 db 76 d3 9c e0 97 48 eb af 7e 54 ee 51 d1 bb 61 39 7a 80 53 d0`：Session ID；</span>
                    <br><span style="padding-left:2em">`c0 2b`：Cipher Suite: TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 (0xc02b)；</span>
                    <br><span style="padding-left:2em">`00`：Compression Method: null (0)；</span>
                    <br><span style="padding-left:2em">`00 0d`：Extensions Length，(13)；</span>
                    <br><span style="padding-left:2em">`ff 01 00 01 00 00 0b 00 04 03 00 01 02`：Extensions；</span>
                </span>
            </span>

            ```markup
            <mark class='under red'>16 03 03 00 59</mark> 02 00 00 55 03 03 <mark class='under blue'>52 f6 1e a4 6d 
            79 2e 2b 1b 94 70 d7 eb 21 5d 42 ed 4b a3 44 88 
            45 f2 b8 c1 4b 63 8f 28 5b e9 fe</mark> 20 <mark class='under deeppink'>0a 0e a0 a9 
            e9 88 4d 2d 07 c1 54 db 76 d3 9c e0 97 48 eb af 
            7e 54 ee 51 d1 bb 61 39 7a 80 53 d0</mark> <mark class='under chartreuse'>c0 2b</mark> 00 <mark class='box red'>00
            0d</mark> <mark class='under dodgerblue'>ff 01 00 01 00 00 0b 00 04 03 00 01 02</mark>
            ```

        - #### HP-Certificate 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`16`：Content type，(表示握手包)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`0b 77`：Length，(2935)；
            <br><span style="padding-left:1em">`0b 00 0b 73 00 ...... 78 90 6e bf f7`：Protocol message(s)，因为类型 **0x16** 是一个握手包，所以按照握手包协议进一步解析。
                <input type="checkbox" checked class="span toggle"><span class='content'>
                    <br><br><span style="padding-left:1em">Handshake protocol：</span>
                    <br><span style="padding-left:2em">`0b`：Message type，(Certificate)；</span>
                    <br><span style="padding-left:2em">`00 0b 73`：Handshake message data length，(2931)；</span>
                    <br><span style="padding-left:2em">`00 0b 70`：Certificates Length: 2928；</span>
                    <br><span style="padding-left:2em">`00 04 07 30 82 ...... 78 90 6e bf f7`：Certificates；
                        <input type="checkbox" checked class="span toggle"><span class='content'>
                            <br><span style="padding-left:3em">`00 04 07`：Certificate Length: (1031)；</span>
                            <br><span style="padding-left:3em">`30 82 04 03 30 ...... 65 83 a4 09 68`：Certificate；</span>
                            <br><span style="padding-left:3em">`00 03 89`：Certificate Length: (905)；</span>
                            <br><span style="padding-left:3em">`30 82 03 85 30 ...... 14 a1 a5 95 ab`：Certificate；</span>
                            <br><span style="padding-left:3em">`00 03 d7`：Certificate Length: (983)；</span>
                            <br><span style="padding-left:3em">`30 82 03 d3 30 ...... 78 90 6e bf f7`：Certificate；</span>
                        </span>
                    </span>
                </span>
            </span>

            ```markup [data-cc:350px]
            <mark class='under red'>16 03 03 0b 77</mark> 0b 00 0b 73 <mark class='box blue'>00 0b 70</mark> <mark class='box green'>00 04 07</mark> <mark class='under dodgerblue'>30 
            82 04 03 30 82 03 88 a0 03 02 01 02 02 10 4f c9 
            72 15 bf 60 1b 22 25 62 0a 35 37 a8 57 73 30 0a 
            06 08 2a 86 48 ce 3d 04 03 03 30 4b 31 0b 30 09 
            06 03 55 04 06 13 02 41 54 31 10 30 0e 06 03 55 
            04 0a 13 07 5a 65 72 6f 53 53 4c 31 2a 30 28 06 
            03 55 04 03 13 21 5a 65 72 6f 53 53 4c 20 45 43 
            43 20 44 6f 6d 61 69 6e 20 53 65 63 75 72 65 20 
            53 69 74 65 20 43 41 30 1e 17 0d 32 34 31 32 32 
            32 30 30 30 30 30 30 5a 17 0d 32 35 30 33 32 32 
            32 33 35 39 35 39 5a 30 14 31 12 30 10 06 03 55 
            04 03 13 09 77 74 66 75 2e 73 69 74 65 30 59 30 
            13 06 07 2a 86 48 ce 3d 02 01 06 08 2a 86 48 ce 
            3d 03 01 07 03 42 00 04 4a cd f6 13 8a a1 45 4b 
            a1 b2 29 2f 8c 31 06 2a 7f 03 7f e0 cd be 38 9b 
            c3 85 02 0e 08 13 fb 14 31 8c 52 9c 12 31 76 dd 
            be bf 92 7a 7d 66 66 79 df 2e 90 18 2b 58 62 67 
            51 57 82 5a 6c eb 11 56 a3 82 02 83 30 82 02 7f 
            30 1f 06 03 55 1d 23 04 18 30 16 80 14 0f 6b e6 
            4b ce 39 47 ae f6 7e 90 1e 79 f0 30 91 92 c8 5f 
            a3 30 1d 06 03 55 1d 0e 04 16 04 14 ee f5 a9 f7 
            35 a7 22 36 8c b5 58 f4 03 78 2e 98 bd e6 fe c2 
            30 0e 06 03 55 1d 0f 01 01 ff 04 04 03 02 07 80 
            30 0c 06 03 55 1d 13 01 01 ff 04 02 30 00 30 1d 
            06 03 55 1d 25 04 16 30 14 06 08 2b 06 01 05 05 
            07 03 01 06 08 2b 06 01 05 05 07 03 02 30 49 06 
            03 55 1d 20 04 42 30 40 30 34 06 0b 2b 06 01 04 
            01 b2 31 01 02 02 4e 30 25 30 23 06 08 2b 06 01 
            05 05 07 02 01 16 17 68 74 74 70 73 3a 2f 2f 73 
            65 63 74 69 67 6f 2e 63 6f 6d 2f 43 50 53 30 08 
            06 06 67 81 0c 01 02 01 30 81 88 06 08 2b 06 01 
            05 05 07 01 01 04 7c 30 7a 30 4b 06 08 2b 06 01 
            05 05 07 30 02 86 3f 68 74 74 70 3a 2f 2f 7a 65 
            72 6f 73 73 6c 2e 63 72 74 2e 73 65 63 74 69 67 
            6f 2e 63 6f 6d 2f 5a 65 72 6f 53 53 4c 45 43 43 
            44 6f 6d 61 69 6e 53 65 63 75 72 65 53 69 74 65 
            43 41 2e 63 72 74 30 2b 06 08 2b 06 01 05 05 07 
            30 01 86 1f 68 74 74 70 3a 2f 2f 7a 65 72 6f 73 
            73 6c 2e 6f 63 73 70 2e 73 65 63 74 69 67 6f 2e 
            63 6f 6d 30 82 01 05 06 0a 2b 06 01 04 01 d6 79 
            02 04 02 04 81 f6 04 81 f3 00 f1 00 77 00 cf 11 
            56 ee d5 2e 7c af f3 87 5b d9 69 2e 9b e9 1a 71 
            67 4a b0 17 ec ac 01 d2 5b 77 ce cc 3b 08 00 00 
            01 93 ec be 8a c5 00 00 04 03 00 48 30 46 02 21 
            00 ba 14 89 4c d4 a6 bf d5 8e 18 3b 10 63 54 35 
            3e 16 3a f1 b0 1d e3 49 20 a0 7f 36 e0 44 30 e7 
            e1 02 21 00 a8 dc 88 b6 51 32 6b 67 1e 60 d9 3a 
            8b 2e f1 f9 1c f1 ff 18 8d c5 3f f7 9b cc 4b d4 
            28 db 5c 13 00 76 00 cc fb 0f 6a 85 71 09 65 fe 
            95 9b 53 ce e9 b2 7c 22 e9 85 5c 0d 97 8d b6 a9 
            7e 54 c0 fe 4c 0d b0 00 00 01 93 ec be 8a 8f 00 
            00 04 03 00 47 30 45 02 20 1b ff 93 39 6b c9 87 
            b7 00 d6 45 63 66 92 b3 60 a0 4f 85 86 d5 1a 7a 
            4d 4d 40 30 d8 9e 7a 21 df 02 21 00 fa b6 ae 47 
            eb 9f c3 a7 f7 e1 87 3c 28 aa a3 47 88 81 af 36 
            ff 1d 16 3e f1 3a 6c 5f 90 cb e3 e1 30 21 06 03 
            55 1d 11 04 1a 30 18 82 09 77 74 66 75 2e 73 69 
            74 65 82 0b 2a 2e 77 74 66 75 2e 73 69 74 65 30 
            0a 06 08 2a 86 48 ce 3d 04 03 03 03 69 00 30 66 
            02 31 00 f9 31 c9 8c 2e 6b f5 ca d4 ad dd 19 87 
            27 1f d8 bd 32 ea 6c 99 6c 2e d6 c7 87 1c 02 f1 
            33 40 68 3a 1a 03 cd 65 10 ae 68 63 85 2e 30 80 
            22 da 47 02 31 00 b6 93 15 5e 96 75 ce 12 4e c0 
            c7 14 79 2b 0a 63 ed 00 42 3a a3 82 0a 68 29 d2 
            90 2a a8 59 c7 56 df 33 53 6f 59 0e 30 ec a0 81 
            42 65 83 a4 09 68</mark> <mark class='box green'>00 03 89</mark> <mark class='under chocolate'>30 82 03 85 30 82 03 
            0c a0 03 02 01 02 02 10 23 b7 6d e3 c1 bb 2b 1a 
            51 96 1e 08 ea b7 64 e8 30 0a 06 08 2a 86 48 ce 
            3d 04 03 03 30 81 88 31 0b 30 09 06 03 55 04 06 
            13 02 55 53 31 13 30 11 06 03 55 04 08 13 0a 4e 
            65 77 20 4a 65 72 73 65 79 31 14 30 12 06 03 55 
            04 07 13 0b 4a 65 72 73 65 79 20 43 69 74 79 31 
            1e 30 1c 06 03 55 04 0a 13 15 54 68 65 20 55 53 
            45 52 54 52 55 53 54 20 4e 65 74 77 6f 72 6b 31 
            2e 30 2c 06 03 55 04 03 13 25 55 53 45 52 54 72 
            75 73 74 20 45 43 43 20 43 65 72 74 69 66 69 63 
            61 74 69 6f 6e 20 41 75 74 68 6f 72 69 74 79 30 
            1e 17 0d 32 30 30 31 33 30 30 30 30 30 30 30 5a 
            17 0d 33 30 30 31 32 39 32 33 35 39 35 39 5a 30 
            4b 31 0b 30 09 06 03 55 04 06 13 02 41 54 31 10 
            30 0e 06 03 55 04 0a 13 07 5a 65 72 6f 53 53 4c 
            31 2a 30 28 06 03 55 04 03 13 21 5a 65 72 6f 53 
            53 4c 20 45 43 43 20 44 6f 6d 61 69 6e 20 53 65 
            63 75 72 65 20 53 69 74 65 20 43 41 30 76 30 10 
            06 07 2a 86 48 ce 3d 02 01 06 05 2b 81 04 00 22 
            03 62 00 04 36 41 61 17 2b 53 25 ed aa ca 94 e4 
            d6 da 48 57 ef 50 ba 84 64 82 d7 bb 05 1b d6 1f 
            06 24 f6 a5 33 9d 8c e7 f1 0b 55 68 63 82 30 10 
            5f 8d 65 ec aa a8 af 97 ca b5 86 ce 30 01 89 74 
            de e3 4e 5e 01 6e ee 26 7b cc 53 fa 23 a4 f7 44 
            1d 3e 4d 1e 5f 66 a6 ad 85 f6 f2 e3 bc 8e 09 98 
            80 24 8e 20 a3 82 01 75 30 82 01 71 30 1f 06 03 
            55 1d 23 04 18 30 16 80 14 3a e1 09 86 d4 cf 19 
            c2 96 76 74 49 76 dc e0 35 c6 63 63 9a 30 1d 06 
            03 55 1d 0e 04 16 04 14 0f 6b e6 4b ce 39 47 ae 
            f6 7e 90 1e 79 f0 30 91 92 c8 5f a3 30 0e 06 03 
            55 1d 0f 01 01 ff 04 04 03 02 01 86 30 12 06 03 
            55 1d 13 01 01 ff 04 08 30 06 01 01 ff 02 01 00 
            30 1d 06 03 55 1d 25 04 16 30 14 06 08 2b 06 01 
            05 05 07 03 01 06 08 2b 06 01 05 05 07 03 02 30 
            22 06 03 55 1d 20 04 1b 30 19 30 0d 06 0b 2b 06 
            01 04 01 b2 31 01 02 02 4e 30 08 06 06 67 81 0c 
            01 02 01 30 50 06 03 55 1d 1f 04 49 30 47 30 45 
            a0 43 a0 41 86 3f 68 74 74 70 3a 2f 2f 63 72 6c 
            2e 75 73 65 72 74 72 75 73 74 2e 63 6f 6d 2f 55 
            53 45 52 54 72 75 73 74 45 43 43 43 65 72 74 69 
            66 69 63 61 74 69 6f 6e 41 75 74 68 6f 72 69 74 
            79 2e 63 72 6c 30 76 06 08 2b 06 01 05 05 07 01 
            01 04 6a 30 68 30 3f 06 08 2b 06 01 05 05 07 30 
            02 86 33 68 74 74 70 3a 2f 2f 63 72 74 2e 75 73 
            65 72 74 72 75 73 74 2e 63 6f 6d 2f 55 53 45 52 
            54 72 75 73 74 45 43 43 41 64 64 54 72 75 73 74 
            43 41 2e 63 72 74 30 25 06 08 2b 06 01 05 05 07 
            30 01 86 19 68 74 74 70 3a 2f 2f 6f 63 73 70 2e 
            75 73 65 72 74 72 75 73 74 2e 63 6f 6d 30 0a 06 
            08 2a 86 48 ce 3d 04 03 03 03 67 00 30 64 02 30 
            24 70 54 0f 01 c9 40 dd c8 54 d9 6d 54 ca c8 08 
            ca 98 43 74 d8 3f f4 d7 a9 5f 6d f2 61 b9 70 0a 
            26 1b 63 30 a8 8b 31 9c bf 77 ec 67 b0 7f a5 88 
            02 30 25 ad ab a4 b0 ee 8d 52 e0 dd 0d 7c 9d df 
            7d 1d ae e2 5c 64 9c 74 f8 7e 63 e5 c1 4e 60 16 
            86 b0 a7 5e 19 6e ec 08 c6 91 d8 fb 03 14 a1 a5 
            95 ab</mark> <mark class='box green'>00 03 d7</mark> <mark class='under dodgerblue'>30 82 03 d3 30 82 02 bb a0 03 02 
            01 02 02 10 56 67 1d 04 ea 4f 99 4c 6f 10 81 47 
            59 d2 75 94 30 0d 06 09 2a 86 48 86 f7 0d 01 01 
            0c 05 00 30 7b 31 0b 30 09 06 03 55 04 06 13 02 
            47 42 31 1b 30 19 06 03 55 04 08 0c 12 47 72 65 
            61 74 65 72 20 4d 61 6e 63 68 65 73 74 65 72 31 
            10 30 0e 06 03 55 04 07 0c 07 53 61 6c 66 6f 72 
            64 31 1a 30 18 06 03 55 04 0a 0c 11 43 6f 6d 6f 
            64 6f 20 43 41 20 4c 69 6d 69 74 65 64 31 21 30 
            1f 06 03 55 04 03 0c 18 41 41 41 20 43 65 72 74 
            69 66 69 63 61 74 65 20 53 65 72 76 69 63 65 73 
            30 1e 17 0d 31 39 30 33 31 32 30 30 30 30 30 30 
            5a 17 0d 32 38 31 32 33 31 32 33 35 39 35 39 5a 
            30 81 88 31 0b 30 09 06 03 55 04 06 13 02 55 53 
            31 13 30 11 06 03 55 04 08 13 0a 4e 65 77 20 4a 
            65 72 73 65 79 31 14 30 12 06 03 55 04 07 13 0b 
            4a 65 72 73 65 79 20 43 69 74 79 31 1e 30 1c 06 
            03 55 04 0a 13 15 54 68 65 20 55 53 45 52 54 52 
            55 53 54 20 4e 65 74 77 6f 72 6b 31 2e 30 2c 06 
            03 55 04 03 13 25 55 53 45 52 54 72 75 73 74 20 
            45 43 43 20 43 65 72 74 69 66 69 63 61 74 69 6f 
            6e 20 41 75 74 68 6f 72 69 74 79 30 76 30 10 06 
            07 2a 86 48 ce 3d 02 01 06 05 2b 81 04 00 22 03 
            62 00 04 1a ac 54 5a a9 f9 68 23 e7 7a d5 24 6f 
            53 c6 5a d8 4b ab c6 d5 b6 d1 e6 73 71 ae dd 9c 
            d6 0c 61 fd db a0 89 03 b8 05 14 ec 57 ce ee 5d 
            3f e2 21 b3 ce f7 d4 8a 79 e0 a3 83 7e 2d 97 d0 
            61 c4 f1 99 dc 25 91 63 ab 7f 30 a3 b4 70 e2 c7 
            a1 33 9c f3 bf 2e 5c 53 b1 5f b3 7d 32 7f 8a 34 
            e3 79 79 a3 81 f2 30 81 ef 30 1f 06 03 55 1d 23 
            04 18 30 16 80 14 a0 11 0a 23 3e 96 f1 07 ec e2 
            af 29 ef 82 a5 7f d0 30 a4 b4 30 1d 06 03 55 1d 
            0e 04 16 04 14 3a e1 09 86 d4 cf 19 c2 96 76 74 
            49 76 dc e0 35 c6 63 63 9a 30 0e 06 03 55 1d 0f 
            01 01 ff 04 04 03 02 01 86 30 0f 06 03 55 1d 13 
            01 01 ff 04 05 30 03 01 01 ff 30 11 06 03 55 1d 
            20 04 0a 30 08 30 06 06 04 55 1d 20 00 30 43 06 
            03 55 1d 1f 04 3c 30 3a 30 38 a0 36 a0 34 86 32 
            68 74 74 70 3a 2f 2f 63 72 6c 2e 63 6f 6d 6f 64 
            6f 63 61 2e 63 6f 6d 2f 41 41 41 43 65 72 74 69 
            66 69 63 61 74 65 53 65 72 76 69 63 65 73 2e 63 
            72 6c 30 34 06 08 2b 06 01 05 05 07 01 01 04 28 
            30 26 30 24 06 08 2b 06 01 05 05 07 30 01 86 18 
            68 74 74 70 3a 2f 2f 6f 63 73 70 2e 63 6f 6d 6f 
            64 6f 63 61 2e 63 6f 6d 30 0d 06 09 2a 86 48 86 
            f7 0d 01 01 0c 05 00 03 82 01 01 00 19 ec eb 9d 
            89 2c 20 0b 04 80 1d 18 de 42 99 72 99 16 32 bd 
            0e 9c 75 5b 2c 15 e2 29 40 6d ee ff 72 db db ab 
            90 1f 8c 95 f2 8a 3d 08 72 42 89 50 07 e2 39 15 
            6c 01 87 d9 16 1a f5 c0 75 2b c5 e6 56 11 07 df 
            d8 98 bc 7c 9f 19 39 df 8b ca 00 64 73 bc 46 10 
            9b 93 23 8d be 16 c3 2e 08 82 9c 86 33 74 76 3b 
            28 4c 8d 03 42 85 b3 e2 b2 23 42 d5 1f 7a 75 6a 
            1a d1 7c aa 67 21 c4 33 3a 39 6d 53 c9 a2 ed 62 
            22 a8 bb e2 55 6c 99 6c 43 6b 91 97 d1 0c 0b 93 
            02 1d d2 bc 69 77 49 e6 1b 4d f7 bf 14 78 03 b0 
            a6 ba 0b b4 e1 85 7f 2f dc 42 3b ad 74 01 48 de 
            d6 6c e1 19 98 09 5e 0a b3 67 47 fe 1c e0 d5 c1 
            28 ef 4a 8b 44 31 26 04 37 8d 89 74 36 2e ef a5 
            22 0f 83 74 49 92 c7 f7 10 c2 0c 29 fb b7 bd ba 
            7f e3 5f d5 9f f2 a9 f4 74 d5 b8 e1 b3 b0 81 e4 
            e1 a5 63 a3 cc ea 04 78 90 6e bf f7
            ```

        - #### HP-ServerKeyExchange 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`16`：Content type，(表示握手包)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`00 94`：Length，(148)；
            <br><span style="padding-left:1em">`0c 00 00 8f 03 ...... d4 97 74 87 44`：Protocol message(s)，因为类型 **0x16** 是一个握手包，所以按照握手包协议进一步解析。
                <input type="checkbox" checked class="span toggle"><span class='content'>
                    <br><br><span style="padding-left:1em">Handshake protocol：</span>
                    <br><span style="padding-left:2em">`0c`：Message type，(ServerKeyExchange)；</span>
                    <br><span style="padding-left:2em">`00 00 90`：Handshake message data length，(144)；</span>
                    <br><span style="padding-left:2em">`03 00 17 41 04 ...... d4 97 74 87 44`：EC Diffie-Hellman Server Params；
                        <input type="checkbox" checked class="span toggle"><span class='content'>
                            <br><span style="padding-left:3em">`03`：Curve Type: named_curve (0x03)；</span>
                            <br><span style="padding-left:3em">`00 17`：Named Curve: secp256r1 (0x0017)；</span>
                            <br><span style="padding-left:3em">`41`：Pubkey Length: 65；</span>
                            <br><span style="padding-left:3em">`04 cf 1f 1d 3c ...... a3 9d 7e c1 a4`：Pubkey；</span>
                            <br><span style="padding-left:3em">`06 03`：Signature Algorithm: ecdsa_secp521r1_sha512 (0x0603)；</span>
                            <br><span style="padding-left:3em">`00 47`：Signature Length: 71；</span>
                            <br><span style="padding-left:3em">`30 45 02 21 00 ...... d4 97 74 87 44`：Signature；</span>
                        </span>
                    </span>
                </span>
            </span>

            ```markup
            <mark class='under red'>16 03 03 00 94</mark> 0c 00 00 90 <mark class='box green'>03 00 17 41</mark> <mark class='under dodgerblue'>04 cf 1f 
            1d 3c 13 27 b0 bf d5 0d f9 55 9e b2 c2 e5 f7 ad 
            ed db db 34 03 37 6e d2 e9 b1 ae 93 a0 b1 33 87 
            5d 9b 90 d9 71 ee df ea 19 d3 b9 da 74 bf 60 f3 
            39 4c 37 5d dc 75 82 68 fe a3 9d 7e c1 a4</mark> 06 03 
            00 47 <mark class='under chocolate'>30 45 02 21 00 de ed 7f a8 fd 3c d8 e0 d5 
            f2 e0 cf 22 eb a8 48 c6 a8 11 4a 65 30 7a 9e c6 
            8d fa 22 a2 a5 b0 e5 02 20 11 2d 77 5a 4b 27 a0 
            b6 0a 00 c0 e0 f5 ca 53 ba 57 66 40 76 79 26 c3 
            40 c0 fc 46 d4 97 74 87 44</mark>
            ```

        - #### HP-ServerHelloDone 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`16`：Content type，(表示握手包)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`00 04`：Length，(4)；
            <br><span style="padding-left:1em">`0e 00 00 00`：Protocol message(s)，因为类型 **0x16** 是一个握手包，所以按照握手包协议进一步解析。
                <input type="checkbox" checked class="span toggle"><span class='content'>
                    <br><br><span style="padding-left:1em">Handshake protocol：</span>
                    <br><span style="padding-left:2em">`0e`：Message type，(ServerHelloDone)；</span>
                    <br><span style="padding-left:2em">`00 00 00`：Handshake message data length，(0)；</span>
                </span>
            </span>

            ```markup
            <mark class='under red'>16 03 03 00 04</mark> 0e 00 00 00
            ```

        - #### HP-ClientKeyExchange 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`16`：Content type，(表示握手包)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`00 46`：Length，(70)；
            <br><span style="padding-left:1em">`10 00 00 42 41 ...... 40 ab 94 b1 ef`：Protocol message(s)，因为类型 **0x16** 是一个握手包，所以按照握手包协议进一步解析。
                <input type="checkbox" checked class="span toggle"><span class='content'>
                    <br><br><span style="padding-left:1em">Handshake protocol：</span>
                    <br><span style="padding-left:2em">`10`：Message type，(ClientKeyExchange)；</span>
                    <br><span style="padding-left:2em">`00 00 42`：Handshake message data length，(66)；</span>
                    <br><span style="padding-left:2em">`41 04 8e 74 54 ...... 40 ab 94 b1 ef`：EC Diffie-Hellman Client Params；
                        <input type="checkbox" checked class="span toggle"><span class='content'>
                            <br><span style="padding-left:3em">`41`：Pubkey Length: (65)；</span>
                            <br><span style="padding-left:3em">`04 8e 74 54 c4 ...... 40 ab 94 b1 ef`：Pubkey；</span>
                        </span>
                    </span>
                </span>
            </span>

            ```markup
            <mark class='under red'>16 03 03 00 46</mark> 10 00 00 42 <mark class='box green'>41</mark> <mark class='under dodgerblue'>04 8e 74 54 c4 02 
            9d a0 63 1d 72 d2 ee 25 74 2d 59 82 6f 1b 85 51 
            65 38 c8 e7 4a 3e af 4c bc fa 83 cc c5 6c f5 c2 
            93 1a 51 34 ac 2e 60 13 0d 17 23 35 d7 2e c2 01 
            2e 1e a5 47 f5 60 40 ab 94 b1 ef</mark>
            ```

        - #### HP-EncryptedMsg-Client 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`16`：Content type，(表示握手包)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`00 28`：Length，(40)；
            <br><span style="padding-left:1em">`00 00 00 00 00 ...... 6e 4e 12 56 8e`：Protocol message(s)，因为类型 **0x16** 是一个握手包，<span style='color: red'>但这个是在客户端 [CCS](#changecipherspec-protocol) 之后的，所以是加密过得，得解密之后才能看到消息内容。</span>
            <br><br>加密参见 [HP-Finished-Client 加密](#hp-finished-client-加密)

            ```markup
            <mark class='under red'>16 03 03 00 28</mark> <mark class='under dodgerblue'>00 00 00 00 00 00 00 00 04 c1 4c 
            a1 7a c3 ed ad 20 45 0a 2c 32 08 b2 db 6c 79 76 
            d8 5b 3d ae 76 ff 1e 28 6e 4e 12 56 8e</mark>
            ```

        - #### HP-EncryptedMsg-Server 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`16`：Content type，(表示握手包)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`00 28`：Length，(40)；
            <br><span style="padding-left:1em">`84 ef 8c ec 32 ...... 80 58 e6 6d a4`：Protocol message(s)，因为类型 **0x16** 是一个握手包，<span style='color: red'>但这个是在服务端 [CCS](#changecipherspec-protocol) 之后的，所以是加密过得，得解密之后才能看到消息内容。</span>
            <br><br>解密参见 [HP-Finished-Server 解密](#hp-finished-server-解密)

            ```markup
            <mark class='under red'>16 03 03 00 28</mark> <mark class='under dodgerblue'>84 ef 8c ec 32 b4 9f d9 a5 e6 04 
            70 54 90 69 44 14 84 ce ad d3 03 e0 eb 66 36 3e 
            01 28 88 6e d7 5c ed f6 80 58 e6 6d a4</mark>
            ```

    + ### ChangeCipherSpec protocol

        > [?] 参考: [rfc5246: Change Cipher Spec Protocol](https://www.rfc-editor.org/rfc/rfc5246.html#page-27)，[ChangeCipherSpec Protocol in SSL](https://www.slashroot.in/changecipherspec-protocol-ssl)
        <br>**ChangeCipherSpec** 消息在 SSL 中用于指示通信从未加密变为加密，从现在开始将使用协商的密钥和密码套件进行当前通信。
        <br>该消息由服务器和客户端发送，以通知对方"让我们开始使用我们刚刚协商的内容"。该消息仅有一个字节。
        <br>密钥交换和证书验证完成后，客户端立即向服务器发送此更改密码规范消息。服务器在收到密钥交换消息后，也会发回更改密码规范消息。
        <br><br>扩展资料：
        <br><span style='padding-left:2.7em'>[Why is change cipher spec an independent protocol content type and not part of Handshake Messages?](https://security.stackexchange.com/questions/24755/why-is-change-cipher-spec-an-independent-protocol-content-type-and-not-part-of-h)
        <br><span style='padding-left:2.7em'>[TLS中ChangeCipherSpec为什么是个单独的协议类型](https://suntus.github.io/2020/03/14/TLS中ChangeCipherSpec为什么是个单独的协议类型/)

        > [!ATTENTION] 如果服务器支持恢复旧的 SSL 会话（通过服务器 hello 消息中的“会话 ID”指示），并且客户端希望恢复旧的会话，则客户端会在“hello 消息”之后立即发送此更改密码规范消息。

        <table class="wikitable" style="width:95%;text-align:center">
            <caption>TLS record format for ChangeCipherSpec protocol </caption>
            <tbody>
                <tr>
                <th scope="col">Offset </th>
                <th scope="col" style="width:22%">Byte+0 </th>
                <th scope="col" style="width:22%">Byte+1 </th>
                <th scope="col" style="width:22%">Byte+2 </th>
                <th scope="col" style="width:22%">Byte+3 </th>
                </tr>
                <tr>
                <th scope="row">Byte <br>0 </th>
                <td style="background:#dfd">20 </td>
                <td colspan="3data-sort-value=&quot;&quot;" style="background: var(--background-color-interactive, #ececec); color: var(--color-base, inherit); vertical-align: middle; text-align: center;" class="table-na">— </td>
                </tr>
                <tr>
                <th scope="row" rowspan="2">Bytes <br>1–4 </th>
                <td colspan="2" style="background:#fdd">Legacy version </td>
                <td colspan="2" style="background:#fdd">Length </td>
                </tr>
                <tr style="background:#fdd">
                <td>
                    <i>(Major)</i>
                </td>
                <td>
                    <i>(Minor)</i>
                </td>
                <td>0 </td>
                <td>1 </td>
                </tr>
                <tr>
                <th>Byte <br>5 </th>
                <td>CCS protocol type </td>
                <td colspan="3data-sort-value=&quot;&quot;" style="background: var(--background-color-interactive, #ececec); color: var(--color-base, inherit); vertical-align: middle; text-align: center;" class="table-na">— </td>
                </tr>
            </tbody>
        </table>

        `CCS protocol type`: Currently only 1.

        - #### CCS 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`14`：Content Type: Change Cipher Spec (20);
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`00 01`：Length，(1)；
            <br><span style="padding-left:1em">`01`：Protocol message(s)，因为类型 **0x14** 是一个状态改变包，所以按照`ChangeCipherSpec`协议进一步解析。
                <input type="checkbox" checked class="span toggle"><span class='content'>
                    <br><br><span style="padding-left:1em">ChangeCipherSpec protocol：</span>
                    <br><span style="padding-left:2em">`01`：Change Cipher Spec Message；</span>
                </span>
            </span>

            ```markup
            <mark class='under red'>14 03 03 00 01</mark> 01
            ```

    + ### Application protocol

        > [?] placeholder

        <table class="wikitable" style="width:95%;text-align:center">
            <caption>TLS record format for application protocol </caption>
            <tbody>
                <tr>
                <th scope="col">Offset </th>
                <th scope="col" style="width:22%">Byte+0 </th>
                <th scope="col" style="width:22%">Byte+1 </th>
                <th scope="col" style="width:22%">Byte+2 </th>
                <th scope="col" style="width:22%">Byte+3 </th>
                </tr>
                <tr>
                <th scope="row">Byte <br>0 </th>
                <td style="background:#dfd">23 </td>
                <td colspan="3data-sort-value=&quot;&quot;" style="background: var(--background-color-interactive, #ececec); color: var(--color-base, inherit); vertical-align: middle; text-align: center;" class="table-na">— </td>
                </tr>
                <tr>
                <th scope="row" rowspan="2">Bytes <br>1–4 </th>
                <td colspan="2" style="background:#fdd">Legacy version </td>
                <td colspan="2" style="background:#fdd">Length </td>
                </tr>
                <tr style="background:#fdd">
                <td>
                    <i>(Major)</i>
                </td>
                <td>
                    <i>(Minor)</i>
                </td>
                <td>
                    <i>(bits 15–8)</i>
                </td>
                <td>
                    <i>(bits 7–0)</i>
                </td>
                </tr>
                <tr>
                <th>Bytes <br>5–( <i>m</i>−1) </th>
                <td colspan="4">Application data </td>
                </tr>
                <tr>
                <th>Bytes <br>
                    <i>m</i>–( <i>p</i>−1)
                </th>
                <td colspan="4" style="background:#fbb">
                    <a href="https://en.wikipedia.org/wiki/Message_authentication_code" target="_blank" rel="noopener" title="Message authentication code">MAC</a> (optional)
                </td>
                </tr>
                <tr>
                <th>Bytes <br>
                    <i>p</i>–( <i>q</i>−1)
                </th>
                <td colspan="4" style="background:#fbb">Padding (block ciphers only) </td>
                </tr>
            </tbody>
        </table>

        `Length`: Length of application data (excluding the protocol header and including the MAC and padding trailers)

        `MAC`: 32 bytes for the SHA-256-based HMAC, 20 bytes for the SHA-1-based HMAC, 16 bytes for the MD5-based HMAC.

        `Padding`: Variable length; last byte contains the padding length.

        - #### AP-EncryptedMsg-Client 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`17`：Content Type: Application Data，应用层数据 (23)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`00 fa`：Length，(250)；
            <br><span style="padding-left:1em">`00 00 00 00 00 ...... ab 32 d2 0d 12`：Protocol message(s)，因为类型 **0x17** 是一个应用层数据包，<span style='color: red'>但这个是在客户端 [CCS](#changecipherspec-protocol) 之后的，所以是加密过得，得解密之后才能看到消息内容。</span>

            ```markup
            <mark class='under red'>17 03 03 00 fa</mark> <mark class='under dodgerblue'>00 00 00 00 00 00 00 01 5b 2e c7 
            61 b1 bb 92 38 2c a9 d0 7e 65 2b 6f 7a 95 71 5d 
            28 bf f2 0a 14 d7 12 17 15 8f e5 ea 39 e9 aa 22 
            f4 47 b8 26 48 9b e8 4e 41 1f 16 3b e4 03 c5 b6 
            0b 9e 4d 9b a8 22 25 55 65 30 b7 52 a1 a3 d4 45 
            5b 66 87 ae 9d 7d c6 c2 2c c4 d1 a1 a1 06 ee 46 
            89 42 cd 52 27 9e 8b ca 57 d7 b7 74 38 5e cb 79 
            cc af 89 31 06 b9 f4 c6 37 dd d2 bd ba 52 e1 32 
            a5 2e c1 5f 64 42 97 f7 f2 f2 62 3e 04 e8 ed 69 
            e0 a2 23 1e 9a 7d 5b dc 0b 4f b9 fa 46 19 90 9e 
            05 eb 93 c7 57 b6 ee 50 bb 87 ba e7 bb 1f dc 27 
            b0 68 ff 50 9f fd 51 78 39 97 72 f1 c8 ce b7 78 
            c7 fa 51 d7 04 d7 98 cb 4d f2 1f 80 5c 0c a1 62 
            a9 b0 95 a4 7a 27 b1 0f 20 ab 34 30 6c 2a f4 be 
            53 14 d0 e7 b0 4c 6a bb 89 89 41 d3 db 59 06 ca 
            2a 37 ca 46 de 19 08 e9 14 a9 ab 32 d2 0d 12</mark>
            ```

        - #### AP-EncryptedMsg-Server 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`17`：Content Type: Application Data，应用层数据 (23)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`0a 04`：Length，(2564)；
            <br><span style="padding-left:1em">`84 ef 8c ec 32 ...... 05 98 5c f7 39`：Protocol message(s)，因为类型 **0x17** 是一个应用层数据包，<span style='color: red'>但这个是在服务端 [CCS](#changecipherspec-protocol) 之后的，所以是加密过得，得解密之后才能看到消息内容。</span>
            <br><br>解密参见 [AP-Server 解密](#ap-server-解密)

            ```markup [data-cc:400px]
            <mark class='under red'>17 03 03 0a 04</mark> <mark class='under dodgerblue'>84 ef 8c ec 32 b4 9f da 96 81 de 
            e1 95 e3 1c 13 dc cf 70 da d2 f9 30 c6 70 49 47 
            f1 78 eb b6 eb 7e ba c7 7b 22 3a 18 6b 2e 29 fe 
            f8 d6 46 62 21 89 d8 84 05 a5 29 28 23 bc a8 05 
            2b 0f 77 95 bb e2 ba 20 14 c5 5d 89 5d 11 c2 df 
            5d 46 f0 4d 38 5e 2c ca 1f 46 4f 05 4e 8d 6d f8 
            7a 0a 30 22 78 49 08 3f 40 bd e6 52 99 39 19 7d 
            59 8c 19 3c 9f b4 f5 0f 68 e7 51 00 47 71 4a 12 
            a2 97 35 8e e9 12 65 01 88 17 7e fd 85 c1 e6 f1 
            0f ca bf 0d 08 88 8c b9 d4 c4 5f 4b 18 bf ba 26 
            f6 15 84 92 df be e5 83 15 d7 3b 6c 92 d7 66 e5 
            a6 ad 42 77 0d eb 24 f2 21 fb 60 04 c4 a5 ab 8e 
            7b df 2f c9 74 a3 83 a5 29 bd b6 c3 e4 d7 d1 e9 
            b3 78 91 af c3 00 1a 0f 3a 61 19 ce 2a 9b 43 1e 
            d1 f7 02 6c 93 90 46 0a 8b 6c 96 37 56 6d dd 3d 
            3b a0 68 ad 30 e6 f4 3b 02 b5 05 73 8b a9 4b 3f 
            2e 0d 26 e2 f4 be ad 30 9a 52 83 73 65 b4 b2 e8 
            6b b2 0d 66 ba dd 58 8d 5c 2e 34 13 e0 52 0b 91 
            66 dd ed 30 2c b9 9e 7a 14 4b 52 06 8d 3f bd 01 
            72 dc b2 68 0d 83 64 c4 00 01 12 b4 34 4a c4 bd 
            15 eb e6 1c 62 ec 92 e5 ea 5b 6f d6 94 1f 89 6b 
            c7 37 82 ad 28 2f 33 ff ac 63 13 47 e8 81 fb 4e 
            6c ac 27 d5 f1 7c 0a 23 20 4d 15 77 7f 70 dd 4d 
            e4 f3 18 6c b6 42 00 4f 5c 97 11 9b 4a 5d 46 90 
            c4 53 d4 1c 57 94 b9 ba 06 2d 53 ab f9 b8 f7 bf 
            4f 60 4d 28 f7 6e e1 be 83 e1 36 2f 61 65 d3 31 
            67 90 1f e0 ae 84 92 e8 d5 26 74 fc 15 82 37 cf 
            33 ed 86 df 7c e7 12 a9 c1 48 b3 53 23 e5 55 0d 
            50 7f 2b 9a 65 dd eb 40 1c 2a 1f b0 fa 44 97 49 
            99 cb a9 f0 f6 cd 95 1e a8 f3 b9 4b 2e 6a 09 cd 
            03 90 ec 04 a9 b7 6d ba 20 fe 38 cc e5 a6 cc e6 
            b2 62 f3 fb 2f 00 ae 9d 59 cb 9a 31 2c 74 5e e0 
            35 47 58 d8 68 25 9d fd 32 51 9a 82 a4 66 32 df 
            6b fa 4f e7 40 a3 31 6f cd ee 23 e5 fe 14 41 06 
            e2 cb b9 3a c3 85 c7 fa d7 aa dd e1 3a b9 5c 46 
            0d 17 9b e2 21 5a 3c b6 8f 97 a2 a6 5d 4f 8e 4a 
            b0 3d 50 66 04 0f 9a 42 f0 42 bc 79 0f 0b 68 d6 
            a3 36 dd 36 f4 06 7d f9 51 69 18 5d 13 2f 6a 54 
            b5 8c f0 ee 0d 86 67 f5 85 54 51 30 06 48 5b f6 
            f9 e5 e1 d8 4a 45 f2 75 da 17 69 60 c2 c4 09 36 
            0d 76 9c 3e 30 0d ce ed 1c 55 aa d1 d0 c8 62 6f 
            18 41 cf f9 0c 49 df 19 15 a5 c5 25 57 5d f0 2f 
            14 86 e3 37 49 46 a3 01 5e c0 58 38 d6 20 70 4a 
            36 67 2c 5b d3 26 cf ed 46 12 d1 3e 83 a8 69 57 
            0e b0 8e f9 87 2e 69 dd fe e9 88 22 fb eb 74 0f 
            a3 4a 3e 39 f5 cb b2 42 32 b8 3d b0 8e 72 3a 87 
            7f 79 ec 51 24 7e 34 7c a5 e3 e7 22 b0 05 bd 62 
            f8 75 da 28 0a 55 16 78 f2 ed 06 e1 5d d8 6e 56 
            2b 88 78 93 2d fd 4d da 8e 59 eb b6 f4 12 86 d9 
            c3 f6 7b 85 56 05 01 5d 1d ac 35 1a 4b 41 91 b6 
            aa 79 00 90 a7 47 0c 1c 9e 82 b6 43 f2 30 29 19 
            1b b4 72 e2 61 b9 24 77 1e 53 47 57 7d cf e3 4e 
            97 ae f2 88 01 5e 7a 8d 55 1f 7a a3 06 a7 4f 0c 
            56 72 c9 5b b1 41 73 5a 38 c0 fb 16 a3 d8 f8 da 
            e3 fc a4 e3 91 4e ab 5a 94 1b 5a 02 69 d3 11 e9 
            06 e0 95 1a 21 bd 3c f5 ef ec e2 f3 ed be 5d b0 
            0d a7 49 7b 5c 66 4d e6 4d 2f 89 52 b9 d2 3c 2f 
            c1 ec 26 73 8d 52 c5 d7 20 49 c9 57 3f f8 f2 da 
            d5 46 13 a1 30 a1 24 60 27 cb 95 ab 7f 15 1c 6c
            9b 98 0c a8 80 ce 9b 17 06 3b 3d 6e c2 24 0b e3 
            29 36 b0 41 df 6b 94 97 8f e4 be e8 d8 5b fb 99 
            f8 01 e8 c8 9b fd 6e fd 67 74 74 02 27 9e dc c4 
            52 a8 18 dc 83 25 19 5e 34 5d e1 33 d9 55 cb 39 
            10 78 66 5a 88 65 44 de 11 3b 38 d2 94 42 26 4b 
            4e 64 c7 17 5a 62 f3 e8 83 46 37 15 01 94 f5 21 
            3e 81 03 17 05 7d fd ef 33 b6 2c 55 66 6a c2 36 
            a2 5b 7f 12 78 0a fb e1 a1 8f a1 7c 2a c9 19 cc 
            bc 39 a5 e5 27 b6 e2 1c 2f 8e af fc 71 17 09 1b 
            1d 26 e5 ac ff 27 09 d7 20 16 43 42 8e 9e 4c f5 
            bb 2e 4f 57 bc 41 79 a1 93 d5 ca f5 23 36 56 82 
            7d 02 b5 0b af ef 35 d1 5a d0 f3 8a 92 8b 83 55 
            8d 0e e4 77 78 45 5a 57 01 19 a5 e5 d3 46 1b 06 
            e8 4a 18 ce 63 6e e9 4d 90 19 a1 6a 3e 86 d2 60 
            4f 45 84 56 a5 fd f4 b4 ef 55 cf 6e d3 08 40 a6 
            08 c7 2d e1 fe bd 25 0c 13 0f 08 4a ff d9 3d 92 
            e5 db 9d 22 53 5d e2 8a c5 65 74 14 f2 ff 81 54 
            04 2b bd 49 ee 02 ed 5c 64 35 f3 bb aa 50 8d a6 
            a9 5b ae 4b 93 82 ce a5 87 c2 65 4c c3 ab 05 f0 
            3d 4d 75 b7 ad d4 68 de b2 94 88 07 ce 31 ac 20 
            0e 97 41 e1 d4 89 af b9 e5 92 3c ce 56 e3 f5 c6 
            c8 f5 4f b7 94 db cc 30 f0 30 f1 49 4a 15 03 6d 
            bc 64 85 6e 95 82 14 c1 5b 6c d7 43 81 f3 da b0 
            e1 6b a5 e3 59 5d 4e 41 38 7d d5 9a aa cb ef 12 
            8d 97 88 90 03 d3 92 91 90 96 7b 02 00 6a cc c3 
            01 f9 c1 fe 2c 8d a7 49 0b c3 7f 16 77 45 4b 9f 
            3d 5d f5 96 f6 94 3e f1 65 0b 33 90 f9 f0 6a ef 
            c9 2b 9c 8c 92 53 e2 c9 26 30 6e fc fa b7 df 70 
            38 ee b9 02 16 5e d5 69 97 25 a1 ae a4 19 9f f8 
            93 31 85 7e 2b 78 29 b7 a0 fb f3 b9 15 71 59 ca 
            e1 b5 6d 61 b9 df 8d 93 35 6d 25 ff ba 80 e1 b9 
            d0 32 b3 4a 08 7f 4f 1f 83 49 f3 61 59 59 18 7d 
            40 b2 f8 97 44 61 fa d9 a3 9f d5 d6 41 64 ee ef 
            22 3d f7 ba b6 47 a2 65 8f 50 7b d1 83 59 c0 51 
            0e 38 06 d5 03 17 d2 87 0c 38 bd 72 b3 1b 16 33 
            ca 41 91 d7 1f 73 bd 4a 88 d4 f7 58 1f 92 24 a5 
            80 51 ea 97 3c c9 67 3e 42 1d 70 4f 58 f2 e3 1b 
            49 e0 67 12 c4 35 37 3e 22 cc 3a 90 2d ea 23 4c 
            39 57 8f fb e8 6e 9c df bb c3 38 ae b5 e5 e5 ee 
            18 d0 b6 c5 c7 e0 a6 ad 5c 9e f7 59 ce 53 71 eb 
            fe 09 c2 3b 39 fc d2 8d f8 bb 5c 37 c9 61 e3 94 
            3a de 42 85 b7 7b ca e8 c9 06 29 f8 e1 41 12 d5 
            1a 63 24 75 66 ee cf a2 bf 22 ae 55 33 49 4c 43 
            fa a1 1c a1 5b cb fb 0a 4d 07 cd d8 c2 16 e1 35 
            23 af a8 96 c6 a7 8d fc f6 c3 1b 85 70 2e ae 56 
            d4 6a af 1a 96 eb cd ac 25 5d c4 a3 00 fd 1b 8c 
            29 75 75 68 eb d6 2d 15 45 98 90 75 31 24 ac ec 
            87 70 34 ee 23 eb 15 3c 54 dd 8f ff 34 74 81 ae 
            ea c6 19 45 6c 9a 88 0f 43 b0 f9 9c 4a 13 cc 6f 
            56 f1 f5 b7 c9 16 47 1d 54 f5 99 95 ba ac 5e c4 
            02 7e fd a2 66 76 92 fe 5b 06 63 cb b9 29 14 f2 
            2c 70 58 36 89 a8 84 af 60 aa 41 a0 21 84 d0 43 
            b7 1b f5 eb 3a 43 8f f9 c2 ec 6b 76 94 5b 7f e9 
            17 e7 7f 9d c8 19 28 c6 fe a1 7f 84 49 31 45 68 
            2f cc fc 0e a1 85 ef 79 e7 0f f5 65 b6 65 45 59 
            af 5b 3b 27 0f bf e6 5b 75 63 35 c5 03 e6 51 57 
            81 48 00 f9 b5 4f dd 0b 1e 41 45 ef 90 c4 4e 26 
            b4 15 c8 fc e2 60 be b9 a7 2f e5 73 06 0c d7 56 
            f2 e8 fc 70 64 e0 2e be 62 df a9 14 a1 ab 3d f8 
            dc 93 d8 32 77 d7 38 1d 6e ae ce 29 92 d9 90 9b 
            cb cd 6c 5c 57 ce 91 05 16 72 64 5b 6a 9a 54 3b 
            eb 42 8c b8 6e 6d ac 74 5c b0 c0 4c b6 f8 61 f1 
            a2 c5 aa b3 ed 6b e9 76 e1 9a c1 b8 70 43 d3 b9 
            8a 5e 0a c2 a6 f1 39 7d d9 fe 46 a0 17 56 f1 49 
            5d ed f9 2e 8e a1 56 4c fc a9 39 7c 95 a6 77 e2 
            e0 7c db ea a2 be ff 14 f7 04 ea 23 cf 6a 8a 02 
            18 bf 40 ce 2d de e4 b0 34 90 d4 c3 c3 15 bf 7b 
            e7 03 de f7 5a d8 8f 27 5c 5f b8 37 c1 f9 54 22 
            39 52 e7 e9 78 a3 df ce 98 96 7a a4 ea 8b 62 28 
            00 25 3b 2c ce 1a 68 ae 46 1d 6c a1 f2 9c 16 29 
            91 05 18 3e ab fb a3 d4 fd d0 ed e0 54 5e c1 13 
            65 2b 59 ee 4f 6f bc 37 75 ff ab 1b 63 5e 7c fa 
            f7 3b 91 46 ae 0c f5 5f f8 5f 43 5a 2c f4 82 b5 
            1d 10 ae 59 92 f9 fc 61 86 06 ec d1 43 18 c2 8e 
            be fd 9a 41 2f 81 58 d4 c9 8a 49 43 2a 81 f5 d9 
            92 e9 b2 dd 23 5d 94 fb 7b 29 b3 7b cc 49 57 8d 
            26 63 1d 15 9d f7 12 87 df 7d c4 4c 0d fd 29 81 
            79 0b e0 9a c0 0b 27 93 ca d1 02 d0 ba 37 e1 67 
            53 dd 6c 1d b5 bd 99 11 c5 1a 84 06 6a 08 82 c8 
            fc 2b 92 fb b7 c3 dc 4a 4a eb 29 04 b4 42 1c 8f 
            4d a2 39 41 a7 d3 d6 ad c0 71 f3 ba c3 ad 00 0b 
            a5 c7 60 6a 1a 5d 87 a3 80 5c 08 97 7a 04 4c 13 
            f5 6b 0c a4 90 78 fa 1d b6 d5 df 4c c3 27 a4 a9 
            ae a3 a8 db 37 cd 0e b0 ba a3 6f 7d 7f 40 b4 fc 
            c3 35 59 ef 4e b7 dc 27 32 79 0a 30 e4 a4 58 f2 
            47 d7 f8 38 24 d1 41 41 53 de c8 ff f2 65 ff 1a 
            f7 88 17 f3 59 84 4d 2d 5b ad 7d 3e d6 c2 c2 e7 
            5f 4e f5 d5 da 56 e5 1c a4 30 6a f7 60 5d 51 ff 
            4e ec 0d f7 60 5a 69 53 0e 63 d7 f8 af b7 e2 81 
            37 99 0a 4f 1f a5 9b fc ff 6a 11 dc 2b 1f f2 4e 
            06 e2 2d 53 b7 0f 65 9a 9c 6a 52 e1 7c 13 af 56 
            7b 50 f4 16 37 f2 67 12 a4 54 11 48 3a cd 9f bb 
            d6 4f eb da d4 1c b9 47 db 2c 7e 3b 85 e3 77 03 
            aa 12 e8 af 4f 5f df e5 da 5a c9 53 14 2c 86 ed 
            02 5e 9c e3 77 f6 bf 18 ad 25 51 47 75 73 72 85 
            c6 0a 45 bf 2a 14 2c d8 07 5d 9f e6 e4 e2 6f 2c 
            5c e0 84 4f 50 16 6c 23 75 87 63 d9 2f 43 22 ef 
            31 2a c1 f9 be ce c3 54 64 5d 3c d7 71 06 9d 5f 
            85 b0 c2 26 87 dc 3f 33 f9 e5 5e 6c d3 7b 07 cd 
            41 35 80 ab 5c 6a 97 ec a1 c6 60 23 6d 3d c4 c3 
            f2 ea b2 59 fd 5b 38 b8 9d aa 24 bf 32 b0 fd 3f 
            e6 c3 94 29 05 98 5c f7 39</mark>
            ```

    + ### Alert protocol

        > [?] 这种记录应该不会被发送在正常握手和数据交换的时候。然而，这种消息是可以在握手的任何时间发送的，直到会话结束。如果用于发送致命信号，则当前会话会在发送完这个 record 后立即关闭，所以这个记录也会携带关闭的原因。如果警告级别被标记为警告，远程可以关闭这次会话，在它认为对于自身需要不太可靠的情况下。这一端发送之前，另外一方也可以发送它的相关信号。

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        <table class="wikitable" style="width:95%;text-align:center">
            <caption>TLS record format for alert protocol </caption>
            <tbody>
                <tr>
                <th scope="col">Offset </th>
                <th scope="col" style="width:22%">Byte+0 </th>
                <th scope="col" style="width:22%">Byte+1 </th>
                <th scope="col" style="width:22%">Byte+2 </th>
                <th scope="col" style="width:22%">Byte+3 </th>
                </tr>
                <tr>
                <th scope="row">Byte <br>0 </th>
                <td style="background:#dfd">21 </td>
                <td colspan="3data-sort-value=&quot;&quot;" style="background: var(--background-color-interactive, #ececec); color: var(--color-base, inherit); vertical-align: middle; text-align: center;" class="table-na">— </td>
                </tr>
                <tr>
                <th scope="row" rowspan="2">Bytes <br>1–4 </th>
                <td colspan="2" style="background:#fdd">Legacy version </td>
                <td colspan="2" style="background:#fdd">Length </td>
                </tr>
                <tr style="background:#fdd">
                <td>
                    <i>(Major)</i>
                </td>
                <td>
                    <i>(Minor)</i>
                </td>
                <td>0 </td>
                <td>2 </td>
                </tr>
                <tr>
                <th>Bytes <br>5–6 </th>
                <td>Level </td>
                <td>Description </td>
                <td colspan="2data-sort-value=&quot;&quot;" style="background: var(--background-color-interactive, #ececec); color: var(--color-base, inherit); vertical-align: middle; text-align: center;" class="table-na">— </td>
                </tr>
                <tr>
                <th>Bytes <br>
                    <i>7</i>–( <i>p</i>−1)
                </th>
                <td colspan="4" style="background:#fbb">
                    <a href="https://en.wikipedia.org/wiki/Message_authentication_code" target="_blank" rel="noopener" title="Message authentication code">MAC</a> (optional)
                </td>
                </tr>
                <tr>
                <th>Bytes <br>
                    <i>p</i>–( <i>q</i>−1)
                </th>
                <td colspan="4" style="background:#fbb">Padding (block ciphers only) </td>
                </tr>
            </tbody>
        </table>

        <!-- div:right-panel-50 -->
        `Level`: 该字段标识警报级别. 如果级别是致命的，发送方应该立即关闭会话. 否则，接收方可以决定自身终止会话，发送自己的致命警报并在发送后立即关闭会话。Alert记录的使用是可选的，但是如果在会话关闭之前缺少Alert记录，会话可能会自动恢复（通过握手）。
        <br>在传输的应用程序终止后，会话的正常关闭最好至少使用Close notify Alert类型（带有简单的警告级别）发出警报，以防止这种新会话的自动恢复。在有效关闭安全会话的传输层之前显式地发出正常关闭的信号，对于防止或检测攻击是有用的（例如，如果安全传输的数据本质上没有安全数据接收者可能期望的预定长度或持续时间，则试图截断安全传输的数据）。

        <table class="wikitable" style="width:90%">
            <caption>Alert level types </caption>
            <tbody>
                <tr>
                <th scope="col">Code </th>
                <th scope="col">Level type </th>
                <th scope="col">Connection state </th>
                </tr>
                <tr>
                <th scope="row">1 </th>
                <td style="background:yellow;text-align:center">
                    <b>warning</b>
                </td>
                <td>connection or security may be unstable. </td>
                </tr>
                <tr>
                <th scope="row">2 </th>
                <td style="background:red;text-align:center">
                    <b>fatal</b>
                </td>
                <td>connection or security may be compromised, or an unrecoverable error has occurred. </td>
                </tr>
            </tbody>
        </table>

        `Description`: 该字段标识正在发送的警报类型。[具体描述参考 wiki:Alert description types](https://en.wikipedia.org/wiki/Transport_Layer_Security#Alert_protocol)
        <!-- panels:end -->

        - #### Alert-EncryptedMsg-Server 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`15`：Content Type: Alert (21)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`00 1a`：Length，(26)；
            <br><span style="padding-left:1em">`84 ef 8c ec 32 ...... a3 c8 ca 1f 77`：Protocol message(s)，因为类型 **0x15** 是一个 Alert 包，<span style='color: red'>但这个是在服务端 [CCS](#changecipherspec-protocol) 之后的，所以是加密过得，得解密之后才能看到消息内容。</span>
            <br><br>解密参见 [Alert-Server 解密](#alert-server-解密)

            ```markup
            <mark class='under red'>15 03 03 00 1a</mark> <mark class='under dodgerblue'>84 ef 8c ec 32 b4 9f db a9 11 e8 
            3f 18 d6 fe 11 34 13 20 11 ce a3 c8 ca 1f 77</mark>
            ```

        - #### Alert-EncryptedMsg-Client 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`15`：Content Type: Alert (21)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`00 1a`：Length，(26)；
            <br><span style="padding-left:1em">`00 00 00 00 00 ...... bb aa 34 65 a9`：Protocol message(s)，因为类型 **0x15** 是一个 Alert 包，<span style='color: red'>但这个是在客户端 [CCS](#changecipherspec-protocol) 之后的，所以是加密过得，得解密之后才能看到消息内容。</span>

            ```markup
            <mark class='under red'>15 03 03 00 1a</mark> <mark class='under dodgerblue'>00 00 00 00 00 00 00 02 3a f7 82 
            f2 00 2e 34 39 8a 72 77 2b c0 bb aa 34 65 a9</mark>
            ```

* ## TLS报文解密

    > [!] 需要注意这次的记录报文是将和服务器通讯过程中 **必要参数** 想办法获取到后，才可以实现的解密。另外也不需要懂太多加解密和 hash 算法相关的高深知识，大致了解即可。
    <br><br>用到的参数如下：
    <br>`1).`: 首先本次服务器选择的 Cipher Suite 为 `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 (0xc02b)`。
    <br>`2).`: 服务器`ServerKeyExchange`中 **EC Diffie-Hellman Server Params** 的公匙为`04 cf 1f 1d 3c ...... a3 9d 7e c1 a4`，具体参见[HP-ServerKeyExchange 实例分析](#hp-serverkeyexchange-实例分析)
    <br>`3).`: 客户端中用来生成公私匙对的关键参数：`41421376590410550347532242163556460089898812947798573278257512740784871055544`。
    <br>`4).`: 客户端随机数：`e3 e5 0a 0c c0 f1 f7 49 4c 14 89 45 9c 28 05 39 fa b4 e9 ce 08 77 f0 94 1f 44 4e 05 34 d6 4b c0`。
    <br>`5).`: 服务器随机数：`52 f6 1e a4 6d 79 2e 2b 1b 94 70 d7 eb 21 5d 42 ed 4b a3 44 88 45 f2 b8 c1 4b 63 8f 28 5b e9 fe`。
    <br><br>本次解密通过两种方式，一种是 代码解析，另外一个是 wireshak.

    > [!TIP] 完整代码参见:[github: DecryptDemo.java](https://github.com/12302-bak/idea-test-project/blob/spring-mvc/_4_springmvc/src/main/java/site/wtfu/framework/utils/DecryptDemo.java)
    <br> IDEA 控制台输出及 wireshark 抓包数据参见：[md: tls-record-console-output](./tls-record-console-output.2ed.md)。

    + ### 前置需求(material)
        
        > [?] 包括 Cipher 构造，以及相关工具方法

        ```java [data-file:TlsAEADCCipherContext.java,data-cc:400px]
        package site.wtfu.framework.utils;

        import lombok.AllArgsConstructor;
        import org.bouncycastle.crypto.AsymmetricCipherKeyPair;
        import org.bouncycastle.crypto.BlockCipher;
        import org.bouncycastle.crypto.Digest;
        import org.bouncycastle.crypto.engines.AESEngine;
        import org.bouncycastle.crypto.modes.AEADBlockCipher;
        import org.bouncycastle.crypto.modes.GCMBlockCipher;
        import org.bouncycastle.crypto.params.ECDomainParameters;
        import org.bouncycastle.crypto.params.ECPrivateKeyParameters;
        import org.bouncycastle.crypto.params.ECPublicKeyParameters;
        import org.bouncycastle.math.ec.ECPoint;
        import org.bouncycastle.math.ec.FixedPointCombMultiplier;
        import org.bouncycastle.tls.*;
        import org.bouncycastle.tls.crypto.CryptoHashAlgorithm;
        import org.bouncycastle.tls.crypto.TlsECConfig;
        import org.bouncycastle.tls.crypto.TlsSecret;
        import org.bouncycastle.tls.crypto.impl.TlsAEADCipherImpl;
        import org.bouncycastle.tls.crypto.impl.bc.BcTlsCrypto;
        import org.bouncycastle.tls.crypto.impl.bc.BcTlsECDomain;
        import org.junit.jupiter.api.Test;
        import site.wtfu.framework.utils.tls.ExBcTlsECDH;
        import site.wtfu.framework.utils.tls.OuterBcTlsAEADCCipherImpl;

        import java.io.IOException;
        import java.math.BigInteger;

        /**
         *
        * Copyright https://wtfu.site Inc. All Rights Reserved.
        *
        * @date 2024/12/26
        *                          @since  1.0
        *                          @author 12302
        *
        */
        public class TlsAEADCCipherContext {

            public TlsAEADCipher testCreateConstructor() throws IOException {
                /*
                * construct
                */
                BlockCipher blockCipher = AESEngine.newInstance();
                AEADBlockCipher aeadBlockCipher = GCMBlockCipher.newInstance(blockCipher);
                OuterBcTlsAEADCCipherImpl encryptCipher = new OuterBcTlsAEADCCipherImpl(aeadBlockCipher, true);
                OuterBcTlsAEADCCipherImpl decryptCipher = new OuterBcTlsAEADCCipherImpl(aeadBlockCipher, false);

                int nonceMode =  1; //NONCE_RFC5288;
                int fixed_iv_length = 4, record_iv_length = 8;
                int keySize = 16, macSize = 16;
                // ProtocolVersion negotiatedVersion = ProtocolVersion.TLSv12;
                byte[] decryptConnectionID = null;
                boolean decryptUseInnerPlaintext = false;

                byte[] decryptNonce = new byte[fixed_iv_length];
                byte[] encryptNonce = new byte[fixed_iv_length];
                boolean isServer = false;

                int keyBlockSize = (2 * keySize) + (2 * fixed_iv_length);
                byte[] peerPublicKey = hexStringToByteArray("04 cf 1f 1d 3c 13 27 b0 bf d5 0d f9 55 9e b2 c2 e5 f7 ad ed db db 34 03 37 6e d2 e9 b1 ae 93 a0 b1 33 87 5d 9b 90 d9 71 ee df ea 19 d3 b9 da 74 bf 60 f3 39 4c 37 5d dc 75 82 68 fe a3 9d 7e c1 a4 ");
                BigInteger big = new BigInteger("41421376590410550347532242163556460089898812947798573278257512740784871055544");
                String asciiLabel = "master secret";
                byte[] clientRandom = hexStringToByteArray("e3 e5 0a 0c c0 f1 f7 49 4c 14 89 45 9c 28 05 39 fa b4 e9 ce 08 77 f0 94 1f 44 4e 05 34 d6 4b c0 ");
                byte[] serverRandom = hexStringToByteArray("52 f6 1e a4 6d 79 2e 2b 1b 94 70 d7 eb 21 5d 42 ed 4b a3 44 88 45 f2 b8 c1 4b 63 8f 28 5b e9 fe ");

                TlsSecret masterSecret = testMasterSecret(peerPublicKey, big, asciiLabel, clientRandom, serverRandom);
                int cipherSuite = CipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256; //securityParameters.getCipherSuite();   TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 (0xc02b)
                int keyExchangeAlgorithm = KeyExchangeAlgorithm.ECDHE_ECDSA;
                int encryptionAlgorithm = EncryptionAlgorithm.AES_128_GCM; //getEncryptionAlgorithm(cipherSuite);
                int macAlgorithm = MACAlgorithm._null; // getMACAlgorithm(cipherSuite);
                byte[] seed = concat(serverRandom, clientRandom);
                byte[] keyBlock = masterSecret.deriveUsingPRF(PRFAlgorithm.tls_prf_sha256, ExporterLabel.key_expansion, seed, keyBlockSize).extract();

                int pos = 0;
                encryptCipher.setKey(keyBlock, pos, keySize);
                pos += keySize;
                decryptCipher.setKey(keyBlock, pos, keySize); pos += keySize;
                System.arraycopy(keyBlock, pos, encryptNonce, 0, fixed_iv_length);
                pos += fixed_iv_length;
                System.arraycopy(keyBlock, pos, decryptNonce, 0, fixed_iv_length); pos += fixed_iv_length;
                if (keyBlockSize != pos) {throw new RuntimeException("that's here");}
                return new TlsAEADCipher(masterSecret, encryptCipher, decryptCipher, encryptNonce, decryptNonce, fixed_iv_length, record_iv_length, macSize);
            }

            public static byte[] hexStringToByteArray(String s) {
                s = s.replaceAll("\\s", "");
                int len = s.length();
                byte[] data = new byte[len / 2];
                for (int i = 0; i < len; i += 2) {
                    data[i / 2] = (byte) ((Character.digit(s.charAt(i), 16) << 4) + Character.digit(s.charAt(i+1), 16));
                }
                return data;
            }

            static byte[] concat(byte[] a, byte[] b)
            {
                byte[] c = new byte[a.length + b.length];
                System.arraycopy(a, 0, c, 0, a.length);
                System.arraycopy(b, 0, c, a.length, b.length);
                return c;
            }

            static byte[] additionalData(long seqNo, short recordType, ProtocolVersion recordVersion,
                                        int ciphertextLength, int plaintextLength, byte[] connectionID){
                byte[] additional_data = new byte[13];
                TlsUtils.writeUint64(seqNo, additional_data, 0);
                TlsUtils.writeUint8(recordType, additional_data, 8);
                TlsUtils.writeVersion(recordVersion, additional_data, 9);
                TlsUtils.writeUint16(plaintextLength, additional_data, 11);
                return additional_data;
            }

            public static TlsSecret TestPreMaster(byte[] peerPublicKey, BigInteger big) throws IOException {
                TlsECConfig ecConfig = new TlsECConfig(0x0017);
                ECDomainParameters domainParameters = BcTlsECDomain.getDomainParameters(ecConfig);
                BcTlsECDomain domain = new BcTlsECDomain(new BcTlsCrypto(), new TlsECConfig(0x0017));

                // BcTlsECDH
                ExBcTlsECDH exBcTlsECDH = new ExBcTlsECDH(domain);
                ECPoint var5 = new FixedPointCombMultiplier().multiply(domainParameters.getG(), big);
                AsymmetricCipherKeyPair localKeyPair = new AsymmetricCipherKeyPair(new ECPublicKeyParameters(var5, domainParameters), new ECPrivateKeyParameters(big, domainParameters));

                // byte[] point = bcTlsECDH.generateEphemeral();
                exBcTlsECDH.setLocalKeyPair(localKeyPair);
                exBcTlsECDH.receivePeerValue(peerPublicKey);
                TlsSecret tlsSecret = exBcTlsECDH.calculateSecret();
                return tlsSecret;
            }

            /**
             * 通过预主密匙计算 masterSecret
            * @throws IOException
            */
            public static TlsSecret testMasterSecret(byte[] peerPublicKey, BigInteger big, String asciiLabel, byte[] clientRandom, byte[] serverRandom) throws IOException {

                //TlsKeyExchangeFactory factory = new DefaultTlsKeyExchangeFactory();
                //TlsKeyExchange keyExchange = factory.createECDHEKeyExchangeClient(KeyExchangeAlgorithm.ECDHE_ECDSA);
                //keyExchange.init(clientContext);

                //TlsSecret preMasterSecret = keyExchange.generatePreMasterSecret(); // 主要通过本地的私匙，和 远端的公匙 来生成一个预主密匙。
                //TlsSecret tlsSecret = agreement.calculateSecret();

                // extendedMasterSecret = false
                byte[] seed = concat(clientRandom, serverRandom);

                TlsSecret preMasterSecret = TestPreMaster(peerPublicKey, big);
                // TlsSecret masterSecret = TlsUtils.calculateMasterSecret(context, preMasterSecret);
                TlsSecret masterSecret = preMasterSecret.deriveUsingPRF(PRFAlgorithm.tls_prf_sha256, asciiLabel, seed, 48);
                return masterSecret;
            }

            public byte[] testHash2ed(){

                byte[] clientHello = hexStringToByteArray("01 00 00 f9 03 03 e3 e5 0a 0c c0 f1 f7 49 4c 14 89 45 9c 28 05 39 fa b4 e9 ce 08 77 f0 94 1f 44 4e 05 34 d6 4b c0 20 e0 76 16 70 7f ac e1 53 5f 39 77 f3 98 b3 ff 2a 19 b6 76 08 3e 23 15 d2 10 78 c5 a8 c1 2a 65 3a 00 1e 13 03 13 01 cc a9 c0 2b c0 23 c0 09 cc a8 c0 2f c0 27 c0 13 cc aa 00 9e 00 67 00 33 00 ff 01 00 00 92 00 17 00 00 00 16 00 00 00 2b 00 05 04 03 04 03 03 00 0a 00 10 00 0e 00 1d 00 1e 00 17 00 18 01 00 01 01 01 02 00 33 00 26 00 24 00 1d 00 20 20 87 73 67 9a 1e 19 0a 9c 9e 04 b8 1d 49 60 6f a1 2e 6c 12 f0 f6 19 03 24 99 0c 77 11 ec 04 60 00 05 00 05 01 00 00 00 00 00 0d 00 30 00 2e 08 07 08 08 04 03 05 03 06 03 08 04 08 05 08 06 08 09 08 0a 08 0b 04 01 05 01 06 01 04 02 05 02 06 02 03 03 03 01 03 02 02 03 02 01 02 02 00 0b 00 02 01 00");
                byte[] serverHello = hexStringToByteArray("02 00 00 55 03 03 52 f6 1e a4 6d 79 2e 2b 1b 94 70 d7 eb 21 5d 42 ed 4b a3 44 88 45 f2 b8 c1 4b 63 8f 28 5b e9 fe 20 0a 0e a0 a9 e9 88 4d 2d 07 c1 54 db 76 d3 9c e0 97 48 eb af 7e 54 ee 51 d1 bb 61 39 7a 80 53 d0 c0 2b 00 00 0d ff 01 00 01 00 00 0b 00 04 03 00 01 02");
                byte[] certificate = hexStringToByteArray("0b 00 0b 73 00 0b 70 00 04 07 30 82 04 03 30 82 03 88 a0 03 02 01 02 02 10 4f c9 72 15 bf 60 1b 22 25 62 0a 35 37 a8 57 73 30 0a 06 08 2a 86 48 ce 3d 04 03 03 30 4b 31 0b 30 09 06 03 55 04 06 13 02 41 54 31 10 30 0e 06 03 55 04 0a 13 07 5a 65 72 6f 53 53 4c 31 2a 30 28 06 03 55 04 03 13 21 5a 65 72 6f 53 53 4c 20 45 43 43 20 44 6f 6d 61 69 6e 20 53 65 63 75 72 65 20 53 69 74 65 20 43 41 30 1e 17 0d 32 34 31 32 32 32 30 30 30 30 30 30 5a 17 0d 32 35 30 33 32 32 32 33 35 39 35 39 5a 30 14 31 12 30 10 06 03 55 04 03 13 09 77 74 66 75 2e 73 69 74 65 30 59 30 13 06 07 2a 86 48 ce 3d 02 01 06 08 2a 86 48 ce 3d 03 01 07 03 42 00 04 4a cd f6 13 8a a1 45 4b a1 b2 29 2f 8c 31 06 2a 7f 03 7f e0 cd be 38 9b c3 85 02 0e 08 13 fb 14 31 8c 52 9c 12 31 76 dd be bf 92 7a 7d 66 66 79 df 2e 90 18 2b 58 62 67 51 57 82 5a 6c eb 11 56 a3 82 02 83 30 82 02 7f 30 1f 06 03 55 1d 23 04 18 30 16 80 14 0f 6b e6 4b ce 39 47 ae f6 7e 90 1e 79 f0 30 91 92 c8 5f a3 30 1d 06 03 55 1d 0e 04 16 04 14 ee f5 a9 f7 35 a7 22 36 8c b5 58 f4 03 78 2e 98 bd e6 fe c2 30 0e 06 03 55 1d 0f 01 01 ff 04 04 03 02 07 80 30 0c 06 03 55 1d 13 01 01 ff 04 02 30 00 30 1d 06 03 55 1d 25 04 16 30 14 06 08 2b 06 01 05 05 07 03 01 06 08 2b 06 01 05 05 07 03 02 30 49 06 03 55 1d 20 04 42 30 40 30 34 06 0b 2b 06 01 04 01 b2 31 01 02 02 4e 30 25 30 23 06 08 2b 06 01 05 05 07 02 01 16 17 68 74 74 70 73 3a 2f 2f 73 65 63 74 69 67 6f 2e 63 6f 6d 2f 43 50 53 30 08 06 06 67 81 0c 01 02 01 30 81 88 06 08 2b 06 01 05 05 07 01 01 04 7c 30 7a 30 4b 06 08 2b 06 01 05 05 07 30 02 86 3f 68 74 74 70 3a 2f 2f 7a 65 72 6f 73 73 6c 2e 63 72 74 2e 73 65 63 74 69 67 6f 2e 63 6f 6d 2f 5a 65 72 6f 53 53 4c 45 43 43 44 6f 6d 61 69 6e 53 65 63 75 72 65 53 69 74 65 43 41 2e 63 72 74 30 2b 06 08 2b 06 01 05 05 07 30 01 86 1f 68 74 74 70 3a 2f 2f 7a 65 72 6f 73 73 6c 2e 6f 63 73 70 2e 73 65 63 74 69 67 6f 2e 63 6f 6d 30 82 01 05 06 0a 2b 06 01 04 01 d6 79 02 04 02 04 81 f6 04 81 f3 00 f1 00 77 00 cf 11 56 ee d5 2e 7c af f3 87 5b d9 69 2e 9b e9 1a 71 67 4a b0 17 ec ac 01 d2 5b 77 ce cc 3b 08 00 00 01 93 ec be 8a c5 00 00 04 03 00 48 30 46 02 21 00 ba 14 89 4c d4 a6 bf d5 8e 18 3b 10 63 54 35 3e 16 3a f1 b0 1d e3 49 20 a0 7f 36 e0 44 30 e7 e1 02 21 00 a8 dc 88 b6 51 32 6b 67 1e 60 d9 3a 8b 2e f1 f9 1c f1 ff 18 8d c5 3f f7 9b cc 4b d4 28 db 5c 13 00 76 00 cc fb 0f 6a 85 71 09 65 fe 95 9b 53 ce e9 b2 7c 22 e9 85 5c 0d 97 8d b6 a9 7e 54 c0 fe 4c 0d b0 00 00 01 93 ec be 8a 8f 00 00 04 03 00 47 30 45 02 20 1b ff 93 39 6b c9 87 b7 00 d6 45 63 66 92 b3 60 a0 4f 85 86 d5 1a 7a 4d 4d 40 30 d8 9e 7a 21 df 02 21 00 fa b6 ae 47 eb 9f c3 a7 f7 e1 87 3c 28 aa a3 47 88 81 af 36 ff 1d 16 3e f1 3a 6c 5f 90 cb e3 e1 30 21 06 03 55 1d 11 04 1a 30 18 82 09 77 74 66 75 2e 73 69 74 65 82 0b 2a 2e 77 74 66 75 2e 73 69 74 65 30 0a 06 08 2a 86 48 ce 3d 04 03 03 03 69 00 30 66 02 31 00 f9 31 c9 8c 2e 6b f5 ca d4 ad dd 19 87 27 1f d8 bd 32 ea 6c 99 6c 2e d6 c7 87 1c 02 f1 33 40 68 3a 1a 03 cd 65 10 ae 68 63 85 2e 30 80 22 da 47 02 31 00 b6 93 15 5e 96 75 ce 12 4e c0 c7 14 79 2b 0a 63 ed 00 42 3a a3 82 0a 68 29 d2 90 2a a8 59 c7 56 df 33 53 6f 59 0e 30 ec a0 81 42 65 83 a4 09 68 00 03 89 30 82 03 85 30 82 03 0c a0 03 02 01 02 02 10 23 b7 6d e3 c1 bb 2b 1a 51 96 1e 08 ea b7 64 e8 30 0a 06 08 2a 86 48 ce 3d 04 03 03 30 81 88 31 0b 30 09 06 03 55 04 06 13 02 55 53 31 13 30 11 06 03 55 04 08 13 0a 4e 65 77 20 4a 65 72 73 65 79 31 14 30 12 06 03 55 04 07 13 0b 4a 65 72 73 65 79 20 43 69 74 79 31 1e 30 1c 06 03 55 04 0a 13 15 54 68 65 20 55 53 45 52 54 52 55 53 54 20 4e 65 74 77 6f 72 6b 31 2e 30 2c 06 03 55 04 03 13 25 55 53 45 52 54 72 75 73 74 20 45 43 43 20 43 65 72 74 69 66 69 63 61 74 69 6f 6e 20 41 75 74 68 6f 72 69 74 79 30 1e 17 0d 32 30 30 31 33 30 30 30 30 30 30 30 5a 17 0d 33 30 30 31 32 39 32 33 35 39 35 39 5a 30 4b 31 0b 30 09 06 03 55 04 06 13 02 41 54 31 10 30 0e 06 03 55 04 0a 13 07 5a 65 72 6f 53 53 4c 31 2a 30 28 06 03 55 04 03 13 21 5a 65 72 6f 53 53 4c 20 45 43 43 20 44 6f 6d 61 69 6e 20 53 65 63 75 72 65 20 53 69 74 65 20 43 41 30 76 30 10 06 07 2a 86 48 ce 3d 02 01 06 05 2b 81 04 00 22 03 62 00 04 36 41 61 17 2b 53 25 ed aa ca 94 e4 d6 da 48 57 ef 50 ba 84 64 82 d7 bb 05 1b d6 1f 06 24 f6 a5 33 9d 8c e7 f1 0b 55 68 63 82 30 10 5f 8d 65 ec aa a8 af 97 ca b5 86 ce 30 01 89 74 de e3 4e 5e 01 6e ee 26 7b cc 53 fa 23 a4 f7 44 1d 3e 4d 1e 5f 66 a6 ad 85 f6 f2 e3 bc 8e 09 98 80 24 8e 20 a3 82 01 75 30 82 01 71 30 1f 06 03 55 1d 23 04 18 30 16 80 14 3a e1 09 86 d4 cf 19 c2 96 76 74 49 76 dc e0 35 c6 63 63 9a 30 1d 06 03 55 1d 0e 04 16 04 14 0f 6b e6 4b ce 39 47 ae f6 7e 90 1e 79 f0 30 91 92 c8 5f a3 30 0e 06 03 55 1d 0f 01 01 ff 04 04 03 02 01 86 30 12 06 03 55 1d 13 01 01 ff 04 08 30 06 01 01 ff 02 01 00 30 1d 06 03 55 1d 25 04 16 30 14 06 08 2b 06 01 05 05 07 03 01 06 08 2b 06 01 05 05 07 03 02 30 22 06 03 55 1d 20 04 1b 30 19 30 0d 06 0b 2b 06 01 04 01 b2 31 01 02 02 4e 30 08 06 06 67 81 0c 01 02 01 30 50 06 03 55 1d 1f 04 49 30 47 30 45 a0 43 a0 41 86 3f 68 74 74 70 3a 2f 2f 63 72 6c 2e 75 73 65 72 74 72 75 73 74 2e 63 6f 6d 2f 55 53 45 52 54 72 75 73 74 45 43 43 43 65 72 74 69 66 69 63 61 74 69 6f 6e 41 75 74 68 6f 72 69 74 79 2e 63 72 6c 30 76 06 08 2b 06 01 05 05 07 01 01 04 6a 30 68 30 3f 06 08 2b 06 01 05 05 07 30 02 86 33 68 74 74 70 3a 2f 2f 63 72 74 2e 75 73 65 72 74 72 75 73 74 2e 63 6f 6d 2f 55 53 45 52 54 72 75 73 74 45 43 43 41 64 64 54 72 75 73 74 43 41 2e 63 72 74 30 25 06 08 2b 06 01 05 05 07 30 01 86 19 68 74 74 70 3a 2f 2f 6f 63 73 70 2e 75 73 65 72 74 72 75 73 74 2e 63 6f 6d 30 0a 06 08 2a 86 48 ce 3d 04 03 03 03 67 00 30 64 02 30 24 70 54 0f 01 c9 40 dd c8 54 d9 6d 54 ca c8 08 ca 98 43 74 d8 3f f4 d7 a9 5f 6d f2 61 b9 70 0a 26 1b 63 30 a8 8b 31 9c bf 77 ec 67 b0 7f a5 88 02 30 25 ad ab a4 b0 ee 8d 52 e0 dd 0d 7c 9d df 7d 1d ae e2 5c 64 9c 74 f8 7e 63 e5 c1 4e 60 16 86 b0 a7 5e 19 6e ec 08 c6 91 d8 fb 03 14 a1 a5 95 ab 00 03 d7 30 82 03 d3 30 82 02 bb a0 03 02 01 02 02 10 56 67 1d 04 ea 4f 99 4c 6f 10 81 47 59 d2 75 94 30 0d 06 09 2a 86 48 86 f7 0d 01 01 0c 05 00 30 7b 31 0b 30 09 06 03 55 04 06 13 02 47 42 31 1b 30 19 06 03 55 04 08 0c 12 47 72 65 61 74 65 72 20 4d 61 6e 63 68 65 73 74 65 72 31 10 30 0e 06 03 55 04 07 0c 07 53 61 6c 66 6f 72 64 31 1a 30 18 06 03 55 04 0a 0c 11 43 6f 6d 6f 64 6f 20 43 41 20 4c 69 6d 69 74 65 64 31 21 30 1f 06 03 55 04 03 0c 18 41 41 41 20 43 65 72 74 69 66 69 63 61 74 65 20 53 65 72 76 69 63 65 73 30 1e 17 0d 31 39 30 33 31 32 30 30 30 30 30 30 5a 17 0d 32 38 31 32 33 31 32 33 35 39 35 39 5a 30 81 88 31 0b 30 09 06 03 55 04 06 13 02 55 53 31 13 30 11 06 03 55 04 08 13 0a 4e 65 77 20 4a 65 72 73 65 79 31 14 30 12 06 03 55 04 07 13 0b 4a 65 72 73 65 79 20 43 69 74 79 31 1e 30 1c 06 03 55 04 0a 13 15 54 68 65 20 55 53 45 52 54 52 55 53 54 20 4e 65 74 77 6f 72 6b 31 2e 30 2c 06 03 55 04 03 13 25 55 53 45 52 54 72 75 73 74 20 45 43 43 20 43 65 72 74 69 66 69 63 61 74 69 6f 6e 20 41 75 74 68 6f 72 69 74 79 30 76 30 10 06 07 2a 86 48 ce 3d 02 01 06 05 2b 81 04 00 22 03 62 00 04 1a ac 54 5a a9 f9 68 23 e7 7a d5 24 6f 53 c6 5a d8 4b ab c6 d5 b6 d1 e6 73 71 ae dd 9c d6 0c 61 fd db a0 89 03 b8 05 14 ec 57 ce ee 5d 3f e2 21 b3 ce f7 d4 8a 79 e0 a3 83 7e 2d 97 d0 61 c4 f1 99 dc 25 91 63 ab 7f 30 a3 b4 70 e2 c7 a1 33 9c f3 bf 2e 5c 53 b1 5f b3 7d 32 7f 8a 34 e3 79 79 a3 81 f2 30 81 ef 30 1f 06 03 55 1d 23 04 18 30 16 80 14 a0 11 0a 23 3e 96 f1 07 ec e2 af 29 ef 82 a5 7f d0 30 a4 b4 30 1d 06 03 55 1d 0e 04 16 04 14 3a e1 09 86 d4 cf 19 c2 96 76 74 49 76 dc e0 35 c6 63 63 9a 30 0e 06 03 55 1d 0f 01 01 ff 04 04 03 02 01 86 30 0f 06 03 55 1d 13 01 01 ff 04 05 30 03 01 01 ff 30 11 06 03 55 1d 20 04 0a 30 08 30 06 06 04 55 1d 20 00 30 43 06 03 55 1d 1f 04 3c 30 3a 30 38 a0 36 a0 34 86 32 68 74 74 70 3a 2f 2f 63 72 6c 2e 63 6f 6d 6f 64 6f 63 61 2e 63 6f 6d 2f 41 41 41 43 65 72 74 69 66 69 63 61 74 65 53 65 72 76 69 63 65 73 2e 63 72 6c 30 34 06 08 2b 06 01 05 05 07 01 01 04 28 30 26 30 24 06 08 2b 06 01 05 05 07 30 01 86 18 68 74 74 70 3a 2f 2f 6f 63 73 70 2e 63 6f 6d 6f 64 6f 63 61 2e 63 6f 6d 30 0d 06 09 2a 86 48 86 f7 0d 01 01 0c 05 00 03 82 01 01 00 19 ec eb 9d 89 2c 20 0b 04 80 1d 18 de 42 99 72 99 16 32 bd 0e 9c 75 5b 2c 15 e2 29 40 6d ee ff 72 db db ab 90 1f 8c 95 f2 8a 3d 08 72 42 89 50 07 e2 39 15 6c 01 87 d9 16 1a f5 c0 75 2b c5 e6 56 11 07 df d8 98 bc 7c 9f 19 39 df 8b ca 00 64 73 bc 46 10 9b 93 23 8d be 16 c3 2e 08 82 9c 86 33 74 76 3b 28 4c 8d 03 42 85 b3 e2 b2 23 42 d5 1f 7a 75 6a 1a d1 7c aa 67 21 c4 33 3a 39 6d 53 c9 a2 ed 62 22 a8 bb e2 55 6c 99 6c 43 6b 91 97 d1 0c 0b 93 02 1d d2 bc 69 77 49 e6 1b 4d f7 bf 14 78 03 b0 a6 ba 0b b4 e1 85 7f 2f dc 42 3b ad 74 01 48 de d6 6c e1 19 98 09 5e 0a b3 67 47 fe 1c e0 d5 c1 28 ef 4a 8b 44 31 26 04 37 8d 89 74 36 2e ef a5 22 0f 83 74 49 92 c7 f7 10 c2 0c 29 fb b7 bd ba 7f e3 5f d5 9f f2 a9 f4 74 d5 b8 e1 b3 b0 81 e4 e1 a5 63 a3 cc ea 04 78 90 6e bf f7");
                byte[] serverKeyExchange = hexStringToByteArray("0c 00 00 90 03 00 17 41 04 cf 1f 1d 3c 13 27 b0 bf d5 0d f9 55 9e b2 c2 e5 f7 ad ed db db 34 03 37 6e d2 e9 b1 ae 93 a0 b1 33 87 5d 9b 90 d9 71 ee df ea 19 d3 b9 da 74 bf 60 f3 39 4c 37 5d dc 75 82 68 fe a3 9d 7e c1 a4 06 03 00 47 30 45 02 21 00 de ed 7f a8 fd 3c d8 e0 d5 f2 e0 cf 22 eb a8 48 c6 a8 11 4a 65 30 7a 9e c6 8d fa 22 a2 a5 b0 e5 02 20 11 2d 77 5a 4b 27 a0 b6 0a 00 c0 e0 f5 ca 53 ba 57 66 40 76 79 26 c3 40 c0 fc 46 d4 97 74 87 44");
                byte[] serverDone = hexStringToByteArray("0e 00 00 00");
                byte[] clientKeyExChange = hexStringToByteArray("10 00 00 42 41 04 8e 74 54 c4 02 9d a0 63 1d 72 d2 ee 25 74 2d 59 82 6f 1b 85 51 65 38 c8 e7 4a 3e af 4c bc fa 83 cc c5 6c f5 c2 93 1a 51 34 ac 2e 60 13 0d 17 23 35 d7 2e c2 01 2e 1e a5 47 f5 60 40 ab 94 b1 ef");

                byte[] concat = concat(clientHello, serverHello);
                concat = concat(concat, certificate);
                concat = concat(concat, serverKeyExchange);
                concat = concat(concat, serverDone);
                concat = concat(concat, clientKeyExChange);

                BcTlsCrypto bcTlsCrypto = new BcTlsCrypto();
                Digest digest = bcTlsCrypto.createDigest(CryptoHashAlgorithm.sha256);
                digest.update(concat,0, concat.length);

                byte[] rv = new byte[digest.getDigestSize()];
                digest.doFinal(rv, 0);

                return rv;
            }

            @AllArgsConstructor
            static
            class TlsAEADCipher {
                private TlsSecret masterSecret;
                private TlsAEADCipherImpl encryptCipher;
                private TlsAEADCipherImpl decryptCipher;

                private byte[] encryptNonce;
                private byte[] decryptNonce;

                private int fixed_iv_length;
                private int record_iv_length;
                private int macSize;
            }
        }
        ```

    + ### 代码解密

        - #### HP-Finished-Server 解密

            > [?] 这个对应的报文是首次使用协商好的算法进行加密过的，其实对应 [HP-EncryptedMsg-Server 实例分析](#hp-encryptedmsg-server-实例分析) 中的加密报文，解密后就是服务端握手协议中的 Finished 报文。解密如下：

            ```java {41}
            @Test
            public void testDecodeCipherForFinished() throws IOException {

                TlsAEADCipher tlsAEADCipher = testCreateConstructor();
                TlsAEADCipherImpl decryptCipher = tlsAEADCipher.decryptCipher;
                byte[] decryptNonce = tlsAEADCipher.decryptNonce;
                int record_iv_length = tlsAEADCipher.record_iv_length;
                int macSize = tlsAEADCipher.macSize;

                /*
                * decodeCipherText
                *
                * params:
                *      byte[] ciphertext,
                *      int ciphertextOffset,
                *      int ciphertextLength
                */
                byte[] finished = hexStringToByteArray("16 03 03 00 28 84 ef 8c ec 32 b4 9f d9 a5 e6 04 \n" +
                        "70 54 90 69 44 14 84 ce ad d3 03 e0 eb 66 36 3e \n" +
                        "01 28 88 6e d7 5c ed f6 80 58 e6 6d a4");
                byte[] ciphertext = finished;
                int ciphertextOffset = 5;
                int ciphertextLength = ciphertext.length - ciphertextOffset;
                byte[] nonce = new byte[decryptNonce.length + record_iv_length];
                System.arraycopy(decryptNonce, 0, nonce, 0, decryptNonce.length);
                System.arraycopy(ciphertext, ciphertextOffset, nonce, nonce.length - record_iv_length, record_iv_length);
                decryptCipher.init(nonce, macSize);

                int encryptionOffset = ciphertextOffset + record_iv_length;
                int encryptionLength = ciphertextLength - record_iv_length;
                int innerPlaintextLength = decryptCipher.getOutputSize(encryptionLength);

                // byte[] additionalData = getAdditionalData(seqNo, recordType, recordVersion, ciphertextLength,innerPlaintextLength, decryptConnectionID);
                byte[] additionalData = hexStringToByteArray("00 00 00 00 00 00 00 01 17 03 03 09 ec ");
                additionalData = additionalData(0L,(short)0x16, ProtocolVersion.TLSv12, 0, innerPlaintextLength, null);
                byte[] target = new byte[ciphertext.length];
                decryptCipher.doFinal(additionalData, ciphertext, encryptionOffset, encryptionLength, ciphertext, encryptionOffset);
                System.out.println(TLSTest.bytesToHex(ciphertext));
            }

            // 16 03 03 00 28 84 ef 8c ec 32 b4 9f d9 14 00 00 0c 44 f4 d3 7c 7d ab 88 b1 0f c9 fa 3b 66 36 3e 01 28 88 6e d7 5c ed f6 80 58 e6 6d a4 
            ```

        - #### HP-Finished-Client 加密

            > [?] 就是我们需要对刚才协商好的算法通过发送一个 finished 报文让服务端进行验证，对应的数据是实例分析中的 [HP-EncryptedMsg-Client 实例分析](#hp-encryptedmsg-client-实例分析)，对原文的加密过程如下：

            ```java {51}
            @Test
            public void testEncodeCipherForFinished() throws IOException {
                TlsAEADCipher tlsAEADCipher = testCreateConstructor();
                TlsSecret masterSecret = tlsAEADCipher.masterSecret;
                TlsAEADCipherImpl encryptCipher = tlsAEADCipher.encryptCipher;
                byte[] encryptNonce = tlsAEADCipher.encryptNonce;
                int record_iv_length = tlsAEADCipher.record_iv_length;
                int macSize = tlsAEADCipher.macSize;

                String asciiLabel = ExporterLabel.client_finished;
                byte[] seed = testHash2ed();
                byte[] verify_data = masterSecret.deriveUsingPRF(PRFAlgorithm.tls_prf_sha256, asciiLabel, seed, 12).extract();

                byte[] assertResult = hexStringToByteArray("16 03 03 00 28 00 00 00 00 00 00 00 00 04 c1 4c \n" +
                        "a1 7a c3 ed ad 20 45 0a 2c 32 08 b2 db 6c 79 76 \n" +
                        "d8 5b 3d ae 76 ff 1e 28 6e 4e 12 56 8e ");

                byte[] plaintext = concat(hexStringToByteArray("14 00 00 0c"), verify_data);
                int plaintextOffset = 0;
                int plaintextLength= plaintext.length;
                boolean encryptUseInnerPlaintext = false;
                int headerAllocation = 5;

                byte[] nonce = new byte[encryptNonce.length + record_iv_length];
                System.arraycopy(encryptNonce, 0, nonce, 0, encryptNonce.length);
                // RFC 5288/6655: The nonce_explicit MAY be the 64-bit sequence number.
                TlsUtils.writeUint64(0, nonce, encryptNonce.length);

                int innerPlaintextLength = plaintextLength + (encryptUseInnerPlaintext ? 1 : 0);

                encryptCipher.init(nonce, macSize);
                int encryptionLength = encryptCipher.getOutputSize(innerPlaintextLength);
                int ciphertextLength = record_iv_length + encryptionLength;

                byte[] output = new byte[headerAllocation + ciphertextLength];
                int outputPos = headerAllocation;

                System.arraycopy(nonce, nonce.length - record_iv_length, output, outputPos, record_iv_length);
                outputPos += record_iv_length;

                byte[] additionalData = additionalData(0L,(short)0x16, ProtocolVersion.TLSv12, ciphertextLength, innerPlaintextLength, null);
                System.arraycopy(plaintext, plaintextOffset, output, outputPos, plaintextLength);

                encryptCipher.doFinal(additionalData, output, outputPos, innerPlaintextLength, output, outputPos);
                TlsUtils.writeUint8(ContentType.handshake, output, RecordFormat.TYPE_OFFSET);
                TlsUtils.writeVersion(ProtocolVersion.TLSv12, output, RecordFormat.VERSION_OFFSET);
                TlsUtils.writeUint16(ciphertextLength, output, RecordFormat.LENGTH_OFFSET);
                System.out.println(TLSTest.bytesToHex(output));
            }

            // 16 03 03 00 28 00 00 00 00 00 00 00 00 04 c1 4c a1 7a c3 ed ad 20 45 0a 2c 32 08 b2 db 6c 79 76 d8 5b 3d ae 76 ff 1e 28 6e 4e 12 56 8e 
            ```
        
        - #### Alert-Server 解密

            > [?] 这个对应 [AP-EncryptedMsg-Server 实例分析](#alert-encryptedmsg-server-实例分析) 中的加密数据。

            ```java {36}
            @Test
            public void testDecodeCipherForAlert() throws IOException {
                TlsAEADCipher tlsAEADCipher = testCreateConstructor();
                TlsAEADCipherImpl decryptCipher = tlsAEADCipher.decryptCipher;
                byte[] decryptNonce = tlsAEADCipher.decryptNonce;
                int record_iv_length = tlsAEADCipher.record_iv_length;
                int macSize = tlsAEADCipher.macSize;

                /*
                * decodeCipherText
                *
                * params:
                *      byte[] ciphertext,
                *      int ciphertextOffset,
                *      int ciphertextLength
                */
                byte[] alert = hexStringToByteArray("15 03 03 00 1a 84 ef 8c ec 32 b4 9f db a9 11 e8 \n" +
                        "3f 18 d6 fe 11 34 13 20 11 ce a3 c8 ca 1f 77");
                byte[] ciphertext = alert;
                int ciphertextOffset = 5;
                int ciphertextLength = ciphertext.length - ciphertextOffset;
                byte[] nonce = new byte[decryptNonce.length + record_iv_length];
                System.arraycopy(decryptNonce, 0, nonce, 0, decryptNonce.length);
                System.arraycopy(ciphertext, ciphertextOffset, nonce, nonce.length - record_iv_length, record_iv_length);
                decryptCipher.init(nonce, macSize);

                int encryptionOffset = ciphertextOffset + record_iv_length;
                int encryptionLength = ciphertextLength - record_iv_length;
                int innerPlaintextLength = decryptCipher.getOutputSize(encryptionLength);

                byte[] additionalData = additionalData(2L,(short)0x15, ProtocolVersion.TLSv12, 0, innerPlaintextLength, null);
                decryptCipher.doFinal(additionalData, ciphertext, encryptionOffset, encryptionLength, ciphertext, encryptionOffset);
                System.out.println(TLSTest.bytesToHex(ciphertext));
            }

            // 15 03 03 00 1a <mark class='box green'>84 ef 8c ec 32 b4 9f db</mark> 01 00 <mark class='under red'>e8 3f 18 d6 fe 11 34 13 20 11 ce a3 c8 ca 1f 77</mark>
            ```

        - #### AP-Server 解密

            > [?] 这个对应 [AP-EncryptedMsg-Server 实例分析](#ap-encryptedmsg-server-实例分析) 中的加密数据，也即服务器对 http response 的响应数据。

            ```java {42-54}
            @Test
            public void testDecodeCipherForResponse() throws IOException {

                TlsAEADCipher tlsAEADCipher = testCreateConstructor();
                TlsAEADCipherImpl decryptCipher = tlsAEADCipher.decryptCipher;
                byte[] decryptNonce = tlsAEADCipher.decryptNonce;
                int record_iv_length = tlsAEADCipher.record_iv_length;
                int macSize = tlsAEADCipher.macSize;

                /*
                * decodeCipherText
                *
                * params:
                *      byte[] ciphertext,
                *      int ciphertextOffset,
                *      int ciphertextLength
                */
                byte[] response = hexStringToByteArray("17 03 03 0a 04 84 ef 8c ec 32 b4 9f da 96 81 de e1 95 e3 1c 13 dc cf 70 da d2 f9 30 c6 70 49 47 f1 78 eb b6 eb 7e ba c7 7b 22 3a 18 6b 2e 29 fe f8 d6 46 62 21 89 d8 84 05 a5 29 28 23 bc a8 05 2b 0f 77 95 bb e2 ba 20 14 c5 5d 89 5d 11 c2 df 5d 46 f0 4d 38 5e 2c ca 1f 46 4f 05 4e 8d 6d f8 7a 0a 30 22 78 49 08 3f 40 bd e6 52 99 39 19 7d 59 8c 19 3c 9f b4 f5 0f 68 e7 51 00 47 71 4a 12 a2 97 35 8e e9 12 65 01 88 17 7e fd 85 c1 e6 f1 0f ca bf 0d 08 88 8c b9 d4 c4 5f 4b 18 bf ba 26 f6 15 84 92 df be e5 83 15 d7 3b 6c 92 d7 66 e5 a6 ad 42 77 0d eb 24 f2 21 fb 60 04 c4 a5 ab 8e 7b df 2f c9 74 a3 83 a5 29 bd b6 c3 e4 d7 d1 e9 b3 78 91 af c3 00 1a 0f 3a 61 19 ce 2a 9b 43 1e d1 f7 02 6c 93 90 46 0a 8b 6c 96 37 56 6d dd 3d 3b a0 68 ad 30 e6 f4 3b 02 b5 05 73 8b a9 4b 3f 2e 0d 26 e2 f4 be ad 30 9a 52 83 73 65 b4 b2 e8 6b b2 0d 66 ba dd 58 8d 5c 2e 34 13 e0 52 0b 91 66 dd ed 30 2c b9 9e 7a 14 4b 52 06 8d 3f bd 01 72 dc b2 68 0d 83 64 c4 00 01 12 b4 34 4a c4 bd 15 eb e6 1c 62 ec 92 e5 ea 5b 6f d6 94 1f 89 6b c7 37 82 ad 28 2f 33 ff ac 63 13 47 e8 81 fb 4e 6c ac 27 d5 f1 7c 0a 23 20 4d 15 77 7f 70 dd 4d e4 f3 18 6c b6 42 00 4f 5c 97 11 9b 4a 5d 46 90 c4 53 d4 1c 57 94 b9 ba 06 2d 53 ab f9 b8 f7 bf 4f 60 4d 28 f7 6e e1 be 83 e1 36 2f 61 65 d3 31 67 90 1f e0 ae 84 92 e8 d5 26 74 fc 15 82 37 cf 33 ed 86 df 7c e7 12 a9 c1 48 b3 53 23 e5 55 0d 50 7f 2b 9a 65 dd eb 40 1c 2a 1f b0 fa 44 97 49 99 cb a9 f0 f6 cd 95 1e a8 f3 b9 4b 2e 6a 09 cd 03 90 ec 04 a9 b7 6d ba 20 fe 38 cc e5 a6 cc e6 b2 62 f3 fb 2f 00 ae 9d 59 cb 9a 31 2c 74 5e e0 35 47 58 d8 68 25 9d fd 32 51 9a 82 a4 66 32 df 6b fa 4f e7 40 a3 31 6f cd ee 23 e5 fe 14 41 06 e2 cb b9 3a c3 85 c7 fa d7 aa dd e1 3a b9 5c 46 0d 17 9b e2 21 5a 3c b6 8f 97 a2 a6 5d 4f 8e 4a b0 3d 50 66 04 0f 9a 42 f0 42 bc 79 0f 0b 68 d6 a3 36 dd 36 f4 06 7d f9 51 69 18 5d 13 2f 6a 54 b5 8c f0 ee 0d 86 67 f5 85 54 51 30 06 48 5b f6 f9 e5 e1 d8 4a 45 f2 75 da 17 69 60 c2 c4 09 36 0d 76 9c 3e 30 0d ce ed 1c 55 aa d1 d0 c8 62 6f 18 41 cf f9 0c 49 df 19 15 a5 c5 25 57 5d f0 2f 14 86 e3 37 49 46 a3 01 5e c0 58 38 d6 20 70 4a 36 67 2c 5b d3 26 cf ed 46 12 d1 3e 83 a8 69 57 0e b0 8e f9 87 2e 69 dd fe e9 88 22 fb eb 74 0f a3 4a 3e 39 f5 cb b2 42 32 b8 3d b0 8e 72 3a 87 7f 79 ec 51 24 7e 34 7c a5 e3 e7 22 b0 05 bd 62 f8 75 da 28 0a 55 16 78 f2 ed 06 e1 5d d8 6e 56 2b 88 78 93 2d fd 4d da 8e 59 eb b6 f4 12 86 d9 c3 f6 7b 85 56 05 01 5d 1d ac 35 1a 4b 41 91 b6 aa 79 00 90 a7 47 0c 1c 9e 82 b6 43 f2 30 29 19 1b b4 72 e2 61 b9 24 77 1e 53 47 57 7d cf e3 4e 97 ae f2 88 01 5e 7a 8d 55 1f 7a a3 06 a7 4f 0c 56 72 c9 5b b1 41 73 5a 38 c0 fb 16 a3 d8 f8 da e3 fc a4 e3 91 4e ab 5a 94 1b 5a 02 69 d3 11 e9 06 e0 95 1a 21 bd 3c f5 ef ec e2 f3 ed be 5d b0 0d a7 49 7b 5c 66 4d e6 4d 2f 89 52 b9 d2 3c 2f c1 ec 26 73 8d 52 c5 d7 20 49 c9 57 3f f8 f2 da \n" +
                        "d5 46 13 a1 30 a1 24 60 27 cb 95 ab 7f 15 1c 6c\n" +
                        //"void\n" +
                        "9b 98 0c a8 80 ce 9b 17 06 3b 3d 6e c2 24 0b e3 29 36 b0 41 df 6b 94 97 8f e4 be e8 d8 5b fb 99 f8 01 e8 c8 9b fd 6e fd 67 74 74 02 27 9e dc c4 52 a8 18 dc 83 25 19 5e 34 5d e1 33 d9 55 cb 39 10 78 66 5a 88 65 44 de 11 3b 38 d2 94 42 26 4b 4e 64 c7 17 5a 62 f3 e8 83 46 37 15 01 94 f5 21 3e 81 03 17 05 7d fd ef 33 b6 2c 55 66 6a c2 36 a2 5b 7f 12 78 0a fb e1 a1 8f a1 7c 2a c9 19 cc bc 39 a5 e5 27 b6 e2 1c 2f 8e af fc 71 17 09 1b 1d 26 e5 ac ff 27 09 d7 20 16 43 42 8e 9e 4c f5 bb 2e 4f 57 bc 41 79 a1 93 d5 ca f5 23 36 56 82 7d 02 b5 0b af ef 35 d1 5a d0 f3 8a 92 8b 83 55 8d 0e e4 77 78 45 5a 57 01 19 a5 e5 d3 46 1b 06 e8 4a 18 ce 63 6e e9 4d 90 19 a1 6a 3e 86 d2 60 4f 45 84 56 a5 fd f4 b4 ef 55 cf 6e d3 08 40 a6 08 c7 2d e1 fe bd 25 0c 13 0f 08 4a ff d9 3d 92 e5 db 9d 22 53 5d e2 8a c5 65 74 14 f2 ff 81 54 04 2b bd 49 ee 02 ed 5c 64 35 f3 bb aa 50 8d a6 a9 5b ae 4b 93 82 ce a5 87 c2 65 4c c3 ab 05 f0 3d 4d 75 b7 ad d4 68 de b2 94 88 07 ce 31 ac 20 0e 97 41 e1 d4 89 af b9 e5 92 3c ce 56 e3 f5 c6 c8 f5 4f b7 94 db cc 30 f0 30 f1 49 4a 15 03 6d bc 64 85 6e 95 82 14 c1 5b 6c d7 43 81 f3 da b0 e1 6b a5 e3 59 5d 4e 41 38 7d d5 9a aa cb ef 12 8d 97 88 90 03 d3 92 91 90 96 7b 02 00 6a cc c3 01 f9 c1 fe 2c 8d a7 49 0b c3 7f 16 77 45 4b 9f 3d 5d f5 96 f6 94 3e f1 65 0b 33 90 f9 f0 6a ef c9 2b 9c 8c 92 53 e2 c9 26 30 6e fc fa b7 df 70 38 ee b9 02 16 5e d5 69 97 25 a1 ae a4 19 9f f8 93 31 85 7e 2b 78 29 b7 a0 fb f3 b9 15 71 59 ca e1 b5 6d 61 b9 df 8d 93 35 6d 25 ff ba 80 e1 b9 d0 32 b3 4a 08 7f 4f 1f 83 49 f3 61 59 59 18 7d 40 b2 f8 97 44 61 fa d9 a3 9f d5 d6 41 64 ee ef 22 3d f7 ba b6 47 a2 65 8f 50 7b d1 83 59 c0 51 0e 38 06 d5 03 17 d2 87 0c 38 bd 72 b3 1b 16 33 ca 41 91 d7 1f 73 bd 4a 88 d4 f7 58 1f 92 24 a5 80 51 ea 97 3c c9 67 3e 42 1d 70 4f 58 f2 e3 1b 49 e0 67 12 c4 35 37 3e 22 cc 3a 90 2d ea 23 4c 39 57 8f fb e8 6e 9c df bb c3 38 ae b5 e5 e5 ee 18 d0 b6 c5 c7 e0 a6 ad 5c 9e f7 59 ce 53 71 eb fe 09 c2 3b 39 fc d2 8d f8 bb 5c 37 c9 61 e3 94 3a de 42 85 b7 7b ca e8 c9 06 29 f8 e1 41 12 d5 1a 63 24 75 66 ee cf a2 bf 22 ae 55 33 49 4c 43 fa a1 1c a1 5b cb fb 0a 4d 07 cd d8 c2 16 e1 35 23 af a8 96 c6 a7 8d fc f6 c3 1b 85 70 2e ae 56 d4 6a af 1a 96 eb cd ac 25 5d c4 a3 00 fd 1b 8c 29 75 75 68 eb d6 2d 15 45 98 90 75 31 24 ac ec 87 70 34 ee 23 eb 15 3c 54 dd 8f ff 34 74 81 ae ea c6 19 45 6c 9a 88 0f 43 b0 f9 9c 4a 13 cc 6f 56 f1 f5 b7 c9 16 47 1d 54 f5 99 95 ba ac 5e c4 02 7e fd a2 66 76 92 fe 5b 06 63 cb b9 29 14 f2 2c 70 58 36 89 a8 84 af 60 aa 41 a0 21 84 d0 43 b7 1b f5 eb 3a 43 8f f9 c2 ec 6b 76 94 5b 7f e9 17 e7 7f 9d c8 19 28 c6 fe a1 7f 84 49 31 45 68 2f cc fc 0e a1 85 ef 79 e7 0f f5 65 b6 65 45 59 af 5b 3b 27 0f bf e6 5b 75 63 35 c5 03 e6 51 57 81 48 00 f9 b5 4f dd 0b 1e 41 45 ef 90 c4 4e 26 b4 15 c8 fc e2 60 be b9 a7 2f e5 73 06 0c d7 56 f2 e8 fc 70 64 e0 2e be 62 df a9 14 a1 ab 3d f8 dc 93 d8 32 77 d7 38 1d 6e ae ce 29 92 d9 90 9b cb cd 6c 5c 57 ce 91 05 16 72 64 5b 6a 9a 54 3b eb 42 8c b8 6e 6d ac 74 5c b0 c0 4c b6 f8 61 f1 a2 c5 aa b3 ed 6b e9 76 e1 9a c1 b8 70 43 d3 b9 8a 5e 0a c2 a6 f1 39 7d d9 fe 46 a0 17 56 f1 49 5d ed f9 2e 8e a1 56 4c fc a9 39 7c 95 a6 77 e2 e0 7c db ea a2 be ff 14 f7 04 ea 23 cf 6a 8a 02 18 bf 40 ce 2d de e4 b0 34 90 d4 c3 c3 15 bf 7b e7 03 de f7 5a d8 8f 27 5c 5f b8 37 c1 f9 54 22 39 52 e7 e9 78 a3 df ce 98 96 7a a4 ea 8b 62 28 00 25 3b 2c ce 1a 68 ae 46 1d 6c a1 f2 9c 16 29 91 05 18 3e ab fb a3 d4 fd d0 ed e0 54 5e c1 13 65 2b 59 ee 4f 6f bc 37 75 ff ab 1b 63 5e 7c fa f7 3b 91 46 ae 0c f5 5f f8 5f 43 5a 2c f4 82 b5 1d 10 ae 59 92 f9 fc 61 86 06 ec d1 43 18 c2 8e be fd 9a 41 2f 81 58 d4 c9 8a 49 43 2a 81 f5 d9 92 e9 b2 dd 23 5d 94 fb 7b 29 b3 7b cc 49 57 8d 26 63 1d 15 9d f7 12 87 df 7d c4 4c 0d fd 29 81 79 0b e0 9a c0 0b 27 93 ca d1 02 d0 ba 37 e1 67 53 dd 6c 1d b5 bd 99 11 c5 1a 84 06 6a 08 82 c8 fc 2b 92 fb b7 c3 dc 4a 4a eb 29 04 b4 42 1c 8f 4d a2 39 41 a7 d3 d6 ad c0 71 f3 ba c3 ad 00 0b a5 c7 60 6a 1a 5d 87 a3 80 5c 08 97 7a 04 4c 13 f5 6b 0c a4 90 78 fa 1d b6 d5 df 4c c3 27 a4 a9 ae a3 a8 db 37 cd 0e b0 ba a3 6f 7d 7f 40 b4 fc c3 35 59 ef 4e b7 dc 27 32 79 0a 30 e4 a4 58 f2 47 d7 f8 38 24 d1 41 41 53 de c8 ff f2 65 ff 1a f7 88 17 f3 59 84 4d 2d 5b ad 7d 3e d6 c2 c2 e7 5f 4e f5 d5 da 56 e5 1c a4 30 6a f7 60 5d 51 ff 4e ec 0d f7 60 5a 69 53 0e 63 d7 f8 af b7 e2 81 37 99 0a 4f 1f a5 9b fc ff 6a 11 dc 2b 1f f2 4e 06 e2 2d 53 b7 0f 65 9a 9c 6a 52 e1 7c 13 af 56 7b 50 f4 16 37 f2 67 12 a4 54 11 48 3a cd 9f bb d6 4f eb da d4 1c b9 47 db 2c 7e 3b 85 e3 77 03 aa 12 e8 af 4f 5f df e5 da 5a c9 53 14 2c 86 ed 02 5e 9c e3 77 f6 bf 18 ad 25 51 47 75 73 72 85 c6 0a 45 bf 2a 14 2c d8 07 5d 9f e6 e4 e2 6f 2c 5c e0 84 4f 50 16 6c 23 75 87 63 d9 2f 43 22 ef 31 2a c1 f9 be ce c3 54 64 5d 3c d7 71 06 9d 5f 85 b0 c2 26 87 dc 3f 33 f9 e5 5e 6c d3 7b 07 cd 41 35 80 ab 5c 6a 97 ec a1 c6 60 23 6d 3d c4 c3 f2 ea b2 59 fd 5b 38 b8 9d aa 24 bf 32 b0 fd 3f e6 c3 94 29 05 98 5c f7 39");
                byte[] ciphertext = response;
                int ciphertextOffset = 5;
                int ciphertextLength = ciphertext.length - ciphertextOffset;
                byte[] nonce = new byte[decryptNonce.length + record_iv_length];
                System.arraycopy(decryptNonce, 0, nonce, 0, decryptNonce.length);
                System.arraycopy(ciphertext, ciphertextOffset, nonce, nonce.length - record_iv_length, record_iv_length);
                decryptCipher.init(nonce, macSize);

                int encryptionOffset = ciphertextOffset + record_iv_length;
                int encryptionLength = ciphertextLength - record_iv_length;
                int innerPlaintextLength = decryptCipher.getOutputSize(encryptionLength);

                // byte[] additionalData = getAdditionalData(seqNo, recordType, recordVersion, ciphertextLength,innerPlaintextLength, decryptConnectionID);
                byte[] additionalData = hexStringToByteArray("00 00 00 00 00 00 00 01 17 03 03 09 ec ");
                additionalData = additionalData(1L,(short)0x17, ProtocolVersion.TLSv12, 0, innerPlaintextLength, null);
                byte[] target = new byte[ciphertext.length];
                decryptCipher.doFinal(additionalData, ciphertext, encryptionOffset, encryptionLength, ciphertext, encryptionOffset);
                System.out.println(new String(ciphertext, encryptionOffset, innerPlaintextLength));
            }

            // HTTP/1.1 200 OK
            // Server: X_12302.eli
            // Date: Mon, 30 Dec 2024 00:08:02 GMT
            // Content-Type: text/html
            // Content-Length: 2239
            // Last-Modified: Tue, 08 Oct 2024 01:41:47 GMT
            // Connection: close
            // Vary: Accept-Encoding
            // ETag: "67048ddb-8bf"
            // Strict-Transport-Security: max-age=15552000
            // Accept-Ranges: bytes

            // <!doctypehtml><html lang="en"><meta charset="UTF-8"><title>index</title><style>body{margin:0;padding:0;overflow:hidden;position:relative;display:flex;justify-content:center;align-items:center;height:150vh;background:#eee8ee;font-size:12px;font-family:Arial,sans-serif}canvas{position:absolute;bottom:0;left:0;z-index:-1}#bottom_layer{visibility:hidden;width:3000px;position:fixed;z-index:302;bottom:0;left:0;height:65px;padding-top:1px;zoom:1;margin:0;line-height:39px}#bottom_layer .s-bottom-layer-content{margin:0 17px;text-align:center}#bottom_layer .lh{display:inline-block;margin-right:14px}#bottom_layer .text-color{color:#979e87}#bottom_layer a{font-size:12px;text-decoration:none}</style><canvas id="waveCanvas"></canvas><div id="bottom_layer"style="visibility:visible;width:100%"><div class="s-bottom-layer-content"><p class="lh"><a class="text-color"href="//wtfu.site"target="_self">关于首页</a><p class="lh"><a class="text-color"href="https://wtfu.site"target="_self">About Site</a><p class="lh"><a class="text-color"href="https://beian.mps.gov.cn/#/query/webSearch?code=11011402053698"rel="noreferrer"target="_blank"><img src="https://beian.mps.gov.cn/img/logo01.dd7ff50e.png"class="w-full"style="width:12px;padding-right:3px;vertical-align:-2px"> 京公网安备11011402053698</a><p class="lh"><a class="text-color"href="https://beian.miit.gov.cn"target="_blank">陇ICP备2020004323号</a><p class="lh"><span class="text-color">© Copyright 2000-2023 12302, Inc. and Contributors</span><p class="lh"><span class="text-color">Created using OpenAI</span></div></div><script>let i=document.getElementById("waveCanvas"),n=i.getContext("2d"),h=(i.width=window.innerWidth,i.height=window.innerHeight,{yOffset:100,amplitude:50,wavelength:.03,frequency:.01,phase:0,speed:.1});window.addEventListener("resize",()=>{i.width=window.innerWidth,i.height=window.innerHeight}),function e(){n.clearRect(0,0,i.width,i.height),n.fillStyle="rgba(255, 255, 255, 0.5)",n.beginPath();for(let e=0;e<i.width;e+=10){var t=h.yOffset+Math.sin(e*h.wavelength+h.phase)*h.amplitude*Math.sin(h.frequency*e+h.phase);n.lineTo(e,t)}n.lineTo(i.width,i.height),n.lineTo(0,i.height),n.closePath(),n.fill(),h.phase+=h.speed,requestAnimationFrame(e)}()</script>
            ```

    + ### 工具解密(wireshark)

        > [!NOTE] wireshark 可以通过 **预主密匙（Pre Master Secret）** 或 **主密匙（Master Secret）** 来对报文进行解密，这两个密匙没太大区别，**因为主密匙就是在知道 hash 算法和两端随机数后，通过预主密匙来生成的** 。有一种叫 [SSLKEYLOGFILE](https://www.ietf.org/archive/id/draft-thomson-tls-keylogfile-00.html) 的文件，一般web客户端都会遵循某种规则，如果存在环境变量 **SSLKEYLOGFILE** ，就会将会话过程的主密匙写入到这个文件中。比如 [curl](https://curl.se/libcurl/c/libcurl-env.html#:~:text=SSLKEYLOGFILE)，[谷歌](https://www.google.com/search?q=SSLKEYLOGFILE+site%3Agoogle.com)，[火狐](https://www.google.com/search?q=SSLKEYLOGFILE+site%3Amozilla.org)，[openssl-s_client](https://docs.openssl.org/master/man1/openssl-s_client/#:~:text=Wireshark)，[nodejs](https://nodejs.org/docs/latest/api/all.html#all_cli_--tls-keylogfile) 等。
        <br>所以可以根据 SSLKEYLOGFILE 的格式，模拟一个类似的文件。因为本次使用的是 Java BC TLS 库，这次会话，即使存在环境变量，也没有写入任何数据。
        
        > [!WARNING] 对于 chrome 有些版本可能环境变量不起作用，需要指定启动参数的时候，可以参考 [chrome-not-firefox-are-not-dumping-to-sslkeylogfile-variable](https://stackoverflow.com/questions/42332792/chrome-not-firefox-are-not-dumping-to-sslkeylogfile-variable/43618193#43618193)，[run-chromium-with-flags](https://www.chromium.org/developers/how-tos/run-chromium-with-flags/) 等。
        <br>`chrome://quit`
        <br>`/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --ssl-key-log-file=/Users/stevenobelia/Desktop/tls_secrets`。（版本 131.0.6778.205）验证没问题。
        <br><br><span> 对于 curl 是否写入密匙到指定文件，也分情况。有可能和 curl 的启用特性有关系，也有可能是 TLS 版本的问题。
            <input type="checkbox" class="span toggle"><span class='content'>
                <br><br><span style="padding-left:2em"> 如下图所示的两个 curl 版本，都可以对 wtfu.site (TLSv1.2) 进行密匙写入，但是只有 brew 安装的才可以写入 music.163.com (TLSv1.3) 的密匙。</span>
                <br><span style="padding-left:2em"> ![](/.images/devops/network/tls-ssl/tls-curl-ssl-key-log-file-01.png ':size=70%') </span>
            </span>
        </span>

        ```markup [data-file:manual-secret.txt]
        CLIENT_RANDOM e3e50a0cc0f1f7494c1489459c280539fab4e9ce0877f0941f444e0534d64bc0 <mark class='under  red'>f404af8dad38c6560b52641bc79281fb7c4344e32a87401eff999735b1b239233c1ad86847808750fb181b80fa44d6a5</mark>
        ```

        - #### 解密展示

            ![](/.images/devops/network/tls-ssl/tls-decrypt-ms-01.png ':size=47%')
            ![](/.images/devops/network/tls-ssl/tls-decrypt-with-wireshark-01.gif ':size=50%')

* ## Reference
    + https://www.rfc-editor.org/rfc/rfc5246.html
    + https://en.wikipedia.org/wiki/Transport_Layer_Security
    + https://www.cloudflare.com/zh-cn/learning/ssl/what-happens-in-a-tls-handshake/
    + https://httpd.apache.org/docs/2.4/ssl/ssl_intro.html
    + https://fjhirsch.com/SSL/tlstut_html/index.htm
    + 
    + TLS/SSL 测试报告
    + https://www.ssllabs.com/ssltest/analyze.html?d=tech.wtfu.site&latest
    + https://myssl.com/tech.wtfu.site?domain=tech.wtfu.site&status=q
    + https://www.cdn77.com/tls-test
    + https://domsignal.com/tls-test
    + 
    + TLS-HP-finished decrypt
    + https://osqa-ask.wireshark.org/questions/57384/tls-finished-packet-renamed-encrypted-handshake-message/
    + https://crypto.stackexchange.com/questions/34754/what-does-the-tls-1-2-client-finished-message-contain
    + 
    + **SSLKEYLOGFIEL**
    + https://curl.se/libcurl/c/libcurl-env.html#:~:text=SSLKEYLOGFILE
    + https://wiki.wireshark.org/TLS#using-the-pre-master-secret
    + 
    + **SSLKEYLOGFIEL format**
    + https://www.ietf.org/archive/id/draft-thomson-tls-keylogfile-00.html