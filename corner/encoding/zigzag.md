* ## Intro(ZigZag-encoding)

    + ### 定义描述

        > [?] 正如 [varint 编码](./varint.md) 中所述，它对于负数来说显得手足无措，心有余而力不足。**zigzag 编码** 就是用来弥补 varint 的缺陷的。它会将负数映射成正值（但是不需要额外的映射表），比如：`(0 = 0, -1 = 1, 1 = 2, -2 = 3, 2 = 4, -3 = 5, 3 = 6 ...)`，从而能够使用 varint 继续编码。zigzag 编码的大致原理就是采用异或操作，将阻碍压缩的 1 进行消除。
        <br><br><span style='color:blue'>没有特别说明的都已 32 bit 进行阐述。</span>

        > [!WARNING|label:异或操作] 异或运算(`XOR` | `^`)，相同为 0，不同为 1。并且异或操作有如下特性：
        <br><span style='padding-left:2.0em'/>`1).` 任何数（0，1）与 1 进行异或的结果相当于取反，比如 0 ^ 1 = 1, 1 ^ 1 = 0。
        <br><span style='padding-left:2.0em'/>`2).` 任何数（0，1）与 0 进行异或的结果相当于不变，比如 0 ^ 0 = 0, 1 ^ 0 = 1。

    + ### 编码过程

        > [!NOTE] `1).` 先将 32 位左移一位，用来保留正负标志位在最低有效位(LSB)

    + ### 举例演示
    + ### 编码实现

* ## Reference

    + [ZigZag encoding/decoding explained](https://gist.github.com/mfuerstenau/ba870a29e16536fdbaba)