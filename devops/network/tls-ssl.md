* ## Intro(TLS/SSL)
    
    图片引用自：[7: TLS record format ](https://www.researchgate.net/figure/TLS-record-format_fig7_321347130)

    ![](/.images/devops/network/tls-ssl/tls-record-format-01.png ':size=50%')

* ## TLS-RECORD(协议规范)

    > [?] 参考 [wiki: TLS_record](https://en.wikipedia.org/wiki/Transport_Layer_Security#TLS_record)

    <br> TLS_record 通用格式如下表：

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
            <a href="https://en.wikipedia.org/wiki/Message_authentication_code" title="Message authentication code">MAC</a> (optional)
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

    <!-- panels:start -->
    <!-- div:left-panel-30 -->
    `Content type` 如下表：

    | Hex| Dec| Type |
    | - | - | - |
    | 0x14 | 20 | [ChangeCipherSpec](#changecipherspec-protocol) |
    | 0x15 | 21 | [Alert](#alert-protocol) |
    | 0x16 | 22 | [Handshake](#handshake-protocol) |
    | 0x17 | 23 | [Application](#application-protocol) |
    | 0x18 | 24 | Heartbeat |

    <!-- div:right-panel-50 -->
    `Legacy version` 表如下：
    
    | Major version | Minor version | version type |
    | - | - | - |
    | 3 | 0 | SSL 3.0 |
    | 3 | 1 | TLS 1.0 |
    | 3 | 2 | TLS 1.1 |
    | 3 | 3 | TLS 1.2 |
    | 3 | 4 | TLS 1.3 |
    <!-- panels:end -->

    `Length` ：表示 **protocol message(s)**，**MAC** 和 **padding** 的长度，不超过 2<sup>14</sup> 个字节(ob100000000000000 = 16384 = 16KB)。

    `Protocol message(s)` ：一个或多个消息在这个协议字段里面定义，这个属性或许被加密依赖于当前连接的状态。

    `MAC and padding` ：在所有密码算法和参数都进行了协商和握手，并且发送一个 **CipherStateChange** 记录（见下文）来确认这些参数生效之前，不能在TLS记录的末尾出现 **MAC** 或者 **padding** 字段。

    + ### Handshake protocol

        > [!TIP] 在建立TLS会话期间交换的大多数消息都基于此记录，除非发生错误或警告，并且需要通过 [Alert 协议](#alert-protocol) 记录发出信号，或者会话的加密方式被其他记录修改（比如：[ChangeCipherSpec 协议](#changecipherspec-protocol)）。

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

        - #### Handshake protocol 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`16`：Content type，(表示握手包)；
            <br><span style="padding-left:1em">`03 01`：Legacy version，(TLS 1.0)；
            <br><span style="padding-left:1em">`00 fd`：Length，(253)；
            <br><span style="padding-left:1em">`01 00 00 f9 03 ...... 0b 00 02 01 00`：Protocol message(s)，因为类型 **16** 是一个握手包，所以按照握手包协议进一步解析。
                <input type="checkbox" checked class="span toggle"><span class='content'>
                    <br><br><span style="padding-left:1em">Handshake protocol：</span>
                    <br><span style="padding-left:2em">`01`：Message type，(ClientHello)；</span>
                    <br><span style="padding-left:2em">`00 00 f9`：Handshake message data length，(249)；</span>
                    <br><span style="padding-left:2em">`03 03`：Version，(TLS 1.2 [0x0303])；</span>
                    <br><span style="padding-left:2em">`3a 67 f5 2b 44 ...... 58 28 b3 18 b4`：Random；
                        <input type="checkbox" class="span toggle"><span class='content'>
                            <br><span style="padding-left:3em">`3a 67 f5 2b`：GMT Unix Time，十进制值为 979891499；[在线转换](https://unixtime.org/) 为我们时区的值: **Fri Jan 19 2001 16:04:59 GMT+0800 (中国标准时间)** </span>
                            <br><span style="padding-left:3em">`44 11 e2 49 8e 32 6e 79 d2 ac 45 98 e8 1b 1c 74 94 3d 0d 2c 22 be 71 58 28 b3 18 b4`：Random Bytes；</span>
                        </span>
                    </span>
                    <br><span style="padding-left:2em">`20`：Session ID length，(32)；</span>
                    <br><span style="padding-left:2em">`83 ce 5a f0 a0 a9 a0 a8 1e b2 b5 42 fc 2d db 66 03 2a c0 60 4b 4e 04 e1 c4 1b 12 b2 5f d5 f1 a1`：Session ID；</span>
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
                            <br><span style="padding-left:3em">`00 33 00 26 00 24 00 1d 00 20 54 38 8f 34 90 97 4d ab 70 78 09 dd 48 2c 2d 70 bd f6 82 ea c1 7d 2d 29 31 3b 3d 64 27 69 93 22`：[Type: key_share (51) Length: 38]
                                <input type="checkbox" class="span toggle"><span class='content'>
                                    <br><span style="padding-left:4em">`00 33`：[Type: key_share (51)] </span>
                                    <br><span style="padding-left:4em">`00 26`：[Length: 38] </span>
                                    <br><span style="padding-left:4em">`00 24`：[Client Key Share Length: 36] </span>
                                    <br><span style="padding-left:5em">`00 1d`：[Group: x25519 (29)] </span>
                                    <br><span style="padding-left:5em">`00 20`：[Key Exchange Length: 32] </span>
                                    <br><span style="padding-left:5em">`54 38 8f 34 90 97 4d ab 70 78 09 dd 48 2c 2d 70 bd f6 82 ea c1 7d 2d 29 31 3b 3d 64 27 69 93 22`：[Key Exchange] </span>
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
            <mark class='under red'>16 03 01 00 fd</mark> <mark class='box green'>01 00 00 f9</mark> 03 03 <mark class='under  blue'>3a 67 f5 2b 44
            11 e2 49 8e 32 6e 79 d2 ac 45 98 e8 1b 1c 74 94
            3d 0d 2c 22 be 71 58 28 b3 18 b4</mark> 20 <mark class='under deeppink'>83 ce 5a f0
            a0 a9 a0 a8 1e b2 b5 42 fc 2d db 66 03 2a c0 60
            4b 4e 04 e1 c4 1b 12 b2 5f d5 f1 a1</mark> 00 1e <mark class='under chartreuse'>13 03
            13 01 cc a9 c0 2b c0 23 c0 09 cc a8 c0 2f c0 27
            c0 13 cc aa 00 9e 00 67 00 33 00 ff</mark> 01 00 <mark class='box red'>00 92</mark>
            <mark class='under dodgerblue'>00 17 00 00</mark> <mark class='under chocolate'>00 16 00 00</mark> <mark class='under dodgerblue'>00 2b 00 05 04 03 04 03
            03</mark> <mark class='under chocolate'>00 0a 00 10 00 0e 00 1d 00 1e 00 17 00 18 01
            00 01 01 01 02</mark> <mark class='under dodgerblue'>00 33 00 26 00 24 00 1d 00 20 54
            38 8f 34 90 97 4d ab 70 78 09 dd 48 2c 2d 70 bd
            f6 82 ea c1 7d 2d 29 31 3b 3d 64 27 69 93 22</mark> <mark class='under chocolate'>00
            05 00 05 01 00 00 00 00</mark> <mark class='under dodgerblue'>00 0d 00 30 00 2e 08 07
            08 08 04 03 05 03 06 03 08 04 08 05 08 06 08 09
            08 0a 08 0b 04 01 05 01 06 01 04 02 05 02 06 02
            03 03 03 01 03 02 02 03 02 01 02 02</mark> <mark class='under chocolate'>00 0b 00 02
            01 00</mark>
            ```

    + ### Alert protocol
    + ### ChangeCipherSpec protocol
    + ### Application protocol

* ## Reference
    + https://www.rfc-editor.org/rfc/rfc5246.html
    + https://en.wikipedia.org/wiki/Transport_Layer_Security
    + https://www.cloudflare.com/zh-cn/learning/ssl/what-happens-in-a-tls-handshake/
    + https://httpd.apache.org/docs/2.4/ssl/ssl_intro.html
    + https://fjhirsch.com/SSL/tlstut_html/index.htm