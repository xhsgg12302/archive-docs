---
weight: 10
---

* ## Intro(RIFF)

    > [?] 摘自：维基百科
    <br><br>**资源交换文件格式**（英语：[Resource Interchange File Format](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format)，缩写为 RIFF），又译资源互换文件格式，是一种文件格式（meta-format）标准，把资料存储在被标记的区块（tagged chunks）中。它是在 1991 年时，由 Microsoft 和 IBM 提出。它是 Electronic Arts 在 1985 提出的 Interchange File Format（IFF）的翻版。这两种标准的唯一不同处是在多比特整数的存储方式。 RIFF 使用的是小端序，这是 IBM PC 使用的处理器 80x86 所使用的格式，而 IFF 存储整数的方式是使用大端序，这是 Amiga 和 Apple Macintosh 电脑使用的处理器，68k，可处理的整数类型。
    <br><br>Microsoft在 AVI，WAV，以及接下来要介绍的 [ANI](./ani.md) 这些的文件格式中，都使用 RIFF 的格式当成它们的基础。

    + ### 结构组成

        > [!NOTE|label:通用 trunk 结构] RIFF 文件完全由各种`chunk`块组成，truck 是可以 **嵌套** 的，可以类比文件系统中的文件夹和文件。所有的`chunk`都有下面的格式：
        <br><span style='padding-left:2em'>`1.` ID(4B)，比如 `'RIFF'`，`'LIST'`，`'anhi'` 等 ASCII 码，字符不够四个 ASCII，则用空格补全，比如`'fmt '`,`'seq '`。
        <br><span style='padding-left:2em'>`2.` SIZE(4B)，当前 chunk 无符号的整形值，尺寸不包括它自身占用的字节数和 ID 的四个字节。
        <br><span style='padding-left:2em'>`3.` DATA(*)，当前块包含的数据，长度就是上一个 SIZE(4B) 对应的值。
        <br><span style='padding-left:2em'>`4.` 一个填充字节，（可选值）如果当前 chunk 的数据不是偶数的话，会存在这个。例如 [综合图例](#综合图例) 中的 ID 为 'IART' 的 truck 后面就有一个填充字节。

        > [!CAUTION|label:特殊 trunk 结构] 在上述 ID 不是 `'RIFF'`，`'LIST'` 这两个的普通 trunk 结构的话。他们的 DATA 数据按照各自的格式解析就行，而且通用 trunk 不能包含其他 truck。
        <br>如果是的话，则可以包含其他 truck，并且 DATA 数据中前四个字节为 type【或者可以叫 HeaderID】，剩下的数据组成 truck lists.

        - #### 综合图例

            > 此处以实际 ani(type="ACON") 格式填充部分数据及结构展示。数据结构的验证可以参考 [DOC|ANI 数据结构验证](./ani.md#数据结构验证)。
        
            ![](/.images/corner/file-format/riff/riff-struct-01.png ':size=100%')

* ## Reference
    + https://en.wikipedia.org/wiki/Resource_Interchange_File_Format
    + https://www.daubnet.com/en/file-format-riff