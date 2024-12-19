* ## Intro(TLS/SSL)
    
    图片引用自：[7: TLS record format ](https://www.researchgate.net/figure/TLS-record-format_fig7_321347130)

    ![](/.images/devops/network/tls-ssl/tls-record-format-01.png ':size=50%')

* ## TLS-RECORD(协议规范)

    > [?] 参考 [wiki: TLS_record](https://en.wikipedia.org/wiki/Transport_Layer_Security#TLS_record)
    <br><br>实例分析用到的 [已经记录过的数据](./tls-record-console-output.md)，一个是来自 [bctls-debug-jdk15to18](https://github.com/bcgit/bc-java/tree/1.78.1) 结合 [idea bug(Evaluate and log)](https://www.jetbrains.com/help/idea/using-breakpoints.html#log) 输出，另外一个是 wireshark 

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

        - #### HP-ClientHello 实例分析

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

        - #### HP-ServerHello 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`16`：Content type，(表示握手包)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`00 59`：Length，(89)；
            <br><span style="padding-left:1em">`02 00 00 55 03 ...... 01 00 00 0b 00`：Protocol message(s)，因为类型 **16** 是一个握手包，所以按照握手包协议进一步解析。
                <input type="checkbox" checked class="span toggle"><span class='content'>
                    <br><br><span style="padding-left:1em">Handshake protocol：</span>
                    <br><span style="padding-left:2em">`02`：Message type，(ServerHello)；</span>
                    <br><span style="padding-left:2em">`00 00 55`：Handshake message data length，(85)；</span>
                    <br><span style="padding-left:2em">`03 03`：Version，(TLS 1.2 [0x0303])；</span>
                    <br><span style="padding-left:2em">`4e 4f 97 03 39 ...... 46 9c 16 8e 5b`：Random；
                        <input type="checkbox" checked class="span toggle"><span class='content'>
                            <br><span style="padding-left:3em">`4e 4f 97 03`：GMT Unix Time，十进制值为 1313838851； [在线转换](https://unixtime.org) 为我们时区的值: **Sat Aug 20 2011 19:14:11 GMT+0800 (中国标准时间)**；</span>
                            <br><span style="padding-left:3em">`39 fa 55 3b 42 5e 31 90 f8 25 8c 26 e2 a6 bc 43 00 4b 12 9d b7 d6 5d 46 9c 16 8e 5b`：Random Bytes；</span>
                        </span>
                    </span>
                    <br><span style="padding-left:2em">`20`：Session ID length，(32)；</span>
                    <br><span style="padding-left:2em">`d7 83 e1 70 ff 7f 85 69 c7 d7 cf 65 fd 35 c2 95 c8 ec 81 dd 79 0b e2 b5 f1 54 54 dd 9e c5 a9 29`：Session ID；</span>
                    <br><span style="padding-left:2em">`c0 2b`：Cipher Suite: TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 (0xc02b)；</span>
                    <br><span style="padding-left:2em">`00`：Compression Method: null (0)；</span>
                    <br><span style="padding-left:2em">`00 0d`：Extensions Length，(13)；</span>
                    <br><span style="padding-left:2em">`ff 01 00 01 00 00 0b 00 04 03 00 01 02`：Extensions；</span>
                </span>
            </span>

            ```markup
            <mark class='under red'>16 03 03 00 59</mark> 02 00 00 55 03 03 <mark class='under blue'>4e 4f 97 03 39
            fa 55 3b 42 5e 31 90 f8 25 8c 26 e2 a6 bc 43 00
            4b 12 9d b7 d6 5d 46 9c 16 8e 5b</mark> 20 <mark class='under deeppink'>d7 83 e1 70
            ff 7f 85 69 c7 d7 cf 65 fd 35 c2 95 c8 ec 81 dd
            79 0b e2 b5 f1 54 54 dd 9e c5 a9 29</mark> <mark class='under chartreuse'>c0 2b</mark> 00 <mark class='box red'>00
            0d</mark> <mark class='under dodgerblue'>ff 01 00 01 00 00 0b 00 04 03 00 01 02</mark>
            ```

        - #### HP-Certificate 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`16`：Content type，(表示握手包)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`0b 77`：Length，(2935)；
            <br><span style="padding-left:1em">`0b 00 0b 73 00 ...... 78 90 6e bf f7`：Protocol message(s)，因为类型 **16** 是一个握手包，所以按照握手包协议进一步解析。
                <input type="checkbox" checked class="span toggle"><span class='content'>
                    <br><br><span style="padding-left:1em">Handshake protocol：</span>
                    <br><span style="padding-left:2em">`0b`：Message type，(Certificate)；</span>
                    <br><span style="padding-left:2em">`00 0b 73`：Handshake message data length，(2931)；</span>
                    <br><span style="padding-left:2em">`00 0b 70`：Certificates Length: 2928；</span>
                    <br><span style="padding-left:2em">`00 04 07 30 82 ...... 78 90 6e bf f7`：Certificates；
                        <input type="checkbox" checked class="span toggle"><span class='content'>
                            <br><span style="padding-left:3em">`00 04 07`：Certificate Length: (1031)；</span>
                            <br><span style="padding-left:3em">`30 82 04 03 30 ...... f1 2f ea db 51`：Certificate；</span>
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
            82 04 03 30 82 03 89 a0 03 02 01 02 02 10 0b a5 
            7a 98 3f e4 c3 ae 18 ac 59 b0 b1 04 32 7f 30 0a 
            06 08 2a 86 48 ce 3d 04 03 03 30 4b 31 0b 30 09 
            06 03 55 04 06 13 02 41 54 31 10 30 0e 06 03 55 
            04 0a 13 07 5a 65 72 6f 53 53 4c 31 2a 30 28 06 
            03 55 04 03 13 21 5a 65 72 6f 53 53 4c 20 45 43 
            43 20 44 6f 6d 61 69 6e 20 53 65 63 75 72 65 20 
            53 69 74 65 20 43 41 30 1e 17 0d 32 34 30 39 32 
            32 30 30 30 30 30 30 5a 17 0d 32 34 31 32 32 31 
            32 33 35 39 35 39 5a 30 14 31 12 30 10 06 03 55 
            04 03 13 09 77 74 66 75 2e 73 69 74 65 30 59 30 
            13 06 07 2a 86 48 ce 3d 02 01 06 08 2a 86 48 ce 
            3d 03 01 07 03 42 00 04 4a cd f6 13 8a a1 45 4b 
            a1 b2 29 2f 8c 31 06 2a 7f 03 7f e0 cd be 38 9b 
            c3 85 02 0e 08 13 fb 14 31 8c 52 9c 12 31 76 dd 
            be bf 92 7a 7d 66 66 79 df 2e 90 18 2b 58 62 67 
            51 57 82 5a 6c eb 11 56 a3 82 02 84 30 82 02 80 
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
            63 6f 6d 30 82 01 04 06 0a 2b 06 01 04 01 d6 79 
            02 04 02 04 81 f5 04 81 f2 00 f0 00 75 00 76 ff 
            88 3f 0a b6 fb 95 51 c2 61 cc f5 87 ba 34 b4 a4 
            cd bb 29 dc 68 42 0a 9f e6 67 4c 5a 3a 74 00 00 
            01 92 18 a0 0e eb 00 00 04 03 00 46 30 44 02 20 
            60 ae ca 02 8d ce 11 be 52 af 5b 76 88 a0 a8 63 
            2b d2 1c 27 24 8d ca e8 72 d0 88 8f 65 c9 b2 c9 
            02 20 78 e3 ab fa b7 02 6e df 83 8f 91 5f 0b 9f 
            a4 95 1d bd 4b 60 1a 5f 1c 21 52 04 b3 75 38 53 
            5d 38 00 77 00 da b6 bf 6b 3f b5 b6 22 9f 9b c2 
            bb 5c 6b e8 70 91 71 6c bb 51 84 85 34 bd a4 3d 
            30 48 d7 fb ab 00 00 01 92 18 a0 0e f4 00 00 04 
            03 00 48 30 46 02 21 00 df a0 af be 8b 27 a0 0a 
            fc a5 de 4a 88 4f c0 f8 7b e4 c2 79 b5 a2 3d f5 
            fb 18 02 fd 23 f6 ef 92 02 21 00 af 1f 6b 00 2e 
            57 96 46 f7 9e 16 bb de fc 9c 4c c5 2b b8 dd 22 
            af 6e 70 32 b5 13 75 cb 75 00 c8 30 23 06 03 55 
            1d 11 04 1c 30 1a 82 09 77 74 66 75 2e 73 69 74 
            65 82 0d 77 77 77 2e 77 74 66 75 2e 73 69 74 65 
            30 0a 06 08 2a 86 48 ce 3d 04 03 03 03 68 00 30 
            65 02 30 05 e3 a7 f8 33 0f 4e b1 62 4c 83 bb 21 
            27 9f 1e b6 8c d4 3a 05 68 28 4e e2 21 68 ed 05 
            dc 7c 00 75 c2 b6 10 97 a0 21 83 d3 02 89 34 d4 
            6e 61 31 02 31 00 92 6e 16 04 c0 1e 20 f5 99 17 
            72 b6 33 eb e7 5f 32 46 03 68 6c 6f 05 5a 90 3a 
            82 89 90 98 05 00 8c 64 6c ae 68 5c d1 77 8d 74 
            e1 f1 2f ea db 51</mark> <mark class='box green'>00 03 89</mark> <mark class='under chocolate'>30 82 03 85 30 82 03 
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
            63 75 72 65 20 53 <mark class='box blue'>69</mark> 74 65 20 43 41 30 76 30 10
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
            d8 98 bc 7c 9f 19 39 df 8b ca <mark class='box blue'>00</mark> 64 73 bc 46 10 
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
            e1 a5 63 a3 cc ea 04 78 90 6e bf f7</mark>
            ```

        - #### HP-ServerKeyExchange 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`16`：Content type，(表示握手包)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`00 93`：Length，(147)；
            <br><span style="padding-left:1em">`0c 00 00 8f 03 ...... 91 bf 6b 44 11`：Protocol message(s)，因为类型 **16** 是一个握手包，所以按照握手包协议进一步解析。
                <input type="checkbox" checked class="span toggle"><span class='content'>
                    <br><br><span style="padding-left:1em">Handshake protocol：</span>
                    <br><span style="padding-left:2em">`0c`：Message type，(ServerKeyExchange)；</span>
                    <br><span style="padding-left:2em">`00 00 8f`：Handshake message data length，(143)；</span>
                    <br><span style="padding-left:2em">`03 00 17 41 04 ...... 91 bf 6b 44 11`：EC Diffie-Hellman Server Params；
                        <input type="checkbox" checked class="span toggle"><span class='content'>
                            <br><span style="padding-left:3em">`03`：Curve Type: named_curve (0x03)；</span>
                            <br><span style="padding-left:3em">`00 17`：Named Curve: secp256r1 (0x0017)；</span>
                            <br><span style="padding-left:3em">`41`：Pubkey Length: 65；</span>
                            <br><span style="padding-left:3em">`04 b2 9d da 3c ...... c9 77 a8 33 7b`：Pubkey；</span>
                            <br><span style="padding-left:3em">`06 03`：Signature Algorithm: ecdsa_secp521r1_sha512 (0x0603)；</span>
                            <br><span style="padding-left:3em">`00 46`：Signature Length: 70；</span>
                            <br><span style="padding-left:3em">`30 44 02 20 4d ...... 91 bf 6b 44 11`：Signature；</span>
                        </span>
                    </span>
                </span>
            </span>

            ```markup
            <mark class='under red'>16 03 03 00 93</mark> 0c 00 00 8f <mark class='box green'>03 00 17 41</mark> <mark class='under dodgerblue'>04 b2 9d 
            da 3c 48 fc b4 1b f2 d5 2a 8e e1 fe 50 c3 e0 7a 
            1a 25 9f f0 de fe 12 8d b5 24 2f f3 ac 79 7a e7 
            db 1a 7d 55 79 41 c1 f4 31 75 5d d8 64 8c c1 38 
            0c 6e a8 91 2e c1 c2 98 4a c9 77 a8 33 7b</mark> 06 03 
            00 46 <mark class='under chocolate'>30 44 02 20 4d 72 54 2a 6c 0f 82 02 c9 3e 
            cf a0 df 0f f4 36 ac d7 52 73 68 31 02 8c bd c1 
            48 69 20 5d 4b 2e 02 20 6c 70 35 34 b2 b3 79 66 
            4e f0 19 14 46 76 43 5b 6c e4 60 ff 6d 80 d6 ed 
            f2 fb 74 91 bf 6b 44 11</mark>
            ```

        - #### HP-ServerHelloDone 实例分析

            > [?] **TLS record**：
            <br><span style="padding-left:1em">`16`：Content type，(表示握手包)；
            <br><span style="padding-left:1em">`03 03`：Legacy version，TLS 1.2 (0x0303)；
            <br><span style="padding-left:1em">`00 04`：Length，(4)；
            <br><span style="padding-left:1em">`0e 00 00 00`：Protocol message(s)，因为类型 **16** 是一个握手包，所以按照握手包协议进一步解析。
                <input type="checkbox" checked class="span toggle"><span class='content'>
                    <br><br><span style="padding-left:1em">Handshake protocol：</span>
                    <br><span style="padding-left:2em">`0e`：Message type，(ServerHelloDone)；</span>
                    <br><span style="padding-left:2em">`00 00 00`：Handshake message data length，(0)；</span>
                </span>
            </span>

            ```markup
            <mark class='under red'>16 03 03 00 04</mark> 0e 00 00 00
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
    + 
    + TLS/SSL 测试报告
    + https://www.ssllabs.com/ssltest/analyze.html?d=tech.wtfu.site&latest