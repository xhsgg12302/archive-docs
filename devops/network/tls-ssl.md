* ## Intro(TLS/SSL)

    
    图片引用自：[7: TLS record format ](https://www.researchgate.net/figure/TLS-record-format_fig7_321347130)

    ![](/.images/devops/network/tls-ssl/tls-record-format-01.png ':size=50%')

* ## 协议规范

    + ### TLS-RECORD
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

        - #### Handshake protocol

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

        - #### Alert protocol
        - #### ChangeCipherSpec protocol
        - #### Application protocol

* ## Reference
    + https://en.wikipedia.org/wiki/Transport_Layer_Security
    + https://www.cloudflare.com/zh-cn/learning/ssl/what-happens-in-a-tls-handshake/
    + https://httpd.apache.org/docs/2.4/ssl/ssl_intro.html
    + https://fjhirsch.com/SSL/tlstut_html/index.htm