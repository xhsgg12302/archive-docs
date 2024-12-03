* ## Intro(Varint-encoding)

    + ### 定义描述

        > [?] varint 类似于 utf-8，varchar等长度不定。顾名思义，属于变长的 int。对于一个 int 类型的数据来说，一般都为四个字节(固长)。所以变长的话就是将固定的四个字节中浪费的那部分去掉，比如对于整形 2 来说对应的二进制为 $00000000\space00000000\space00000000\space{\color{red}00000010}$ 前面的三个字节都为0，而且完全可以使用一个字节表示，此处对于整数2 来说，前三个字节就是浪费。所以出现了`varint`。使用少量字节来表示整数。使用定义型的方式描述就是：**采用不定长的字节来编码或序列化一个整数**。

    + ### 编码过程

        > [!NOTE] 将一个 int 型的数据二进制表示，从后至前，依次取 7 位，与 varint 特性 msb 占 1 位组成一个字节，直到无效字节(也就是前面全是零)，然后将这些字节按照小端字节序排列。形成一个新的数字，这个过程中会丢弃浪费字节，达到压缩的目的。
        <br><br><span style='color:red'>varint msb 最高有效位为 1 表示后面都得字节属于当前值，为 0 表示当前字节是最后一部分。用来区分 varint 值边界。</span>
        <br><br><span style='color:red'>对于负数来说，编码过程并没有什么区别，只是因为负数最高位一直为 1，所以不存在浪费，也就是 vaint 对于负数压缩无效，字节不减反增。原本 -2 可以使用 8 个字节表示，但最终用使用了 10 个字节。所以在 ProtoBuf 中一般只处理 无符号 64bit 值，签名如：`func EncodeVarint(v uint64) []byte {}`。所以我们此处传入的 -2 其实是当一个无符号正值处理的，负数的 varint 编码没有意义，我们应当避免对于负值进行 varint 编码。</span>

    + ### 举例演示

        ![](/.images/corner/encoding/varint/varint-demo-sample-01.png ':size=100%')

        - #### 取值表格

            | value | process01 | process02 | final |
            | -- | -- | -- | -- |
            | 2 | | | $00000010$ |
            | 0 | | | $00000000$ |
            | -2 | | | $11111110\space 11111111\space 11111111\space 11111111\space 11111111\space 11111111\space 11111111\space 11111111\space 11111111\space 00000001$ |
            | 123456 | | | $11000000\space11000100\space00000111$ |


        - #### ProtoBuf内置编解码

            ![](/.images/corner/encoding/varint/varint-demo-sample-02.png ':size=80%')

        - #### Python工具编解码

            参考[python: Command Line Varint Decoder/Encoder Tool](https://gist.github.com/Techcable/38c27ef1a3cbb0f78c6d03cc8620af9c)，但是对负数支持不太好。
        
            ![](/.images/corner/encoding/varint/varint-demo-sample-03.png ':size=60%')

    + ### 编码实现

        > [?] 由于 protocol buffer 中大量使用了 varint 编码，可以在 [此处](https://github.com/golang/protobuf/blob/75de7c059e36b64f01d0dd234ff2fff404ec3374/proto/buffer.go#L26) 找到了对数据进行 varint 编解码的 Go 语言实现方法，实际在 [库文件: encoding/protowire](https://github.com/protocolbuffers/protobuf-go/blob/v1.35.2/encoding/protowire/wire.go#L185-L263) 实现代码中用位运算完成了上面说的 varint 编码过程。

* ## Reference

    + https://protobuf.dev/programming-guides/encoding/#varints
    + https://cloud.tencent.com/developer/article/1520305
    + [python: Command Line Varint Decoder/Encoder Tool](https://gist.github.com/Techcable/38c27ef1a3cbb0f78c6d03cc8620af9c)
    + 
    + https://drive.google.com/file/d/1QdlrKnjQkO2u5VWchgZuB0wjov06BLSP/view?usp=sharing