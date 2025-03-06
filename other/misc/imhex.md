---
tags: ["hex", "editor", "tools"]
---

* ## Intro(ImHex | 十六进制编辑器)

    > [?] 一个瑞士小孩哥使用 C++23 写的开源 **十六进制编辑器**，主要包括十六进制预览（Hex editor），单多字节数据编码（data inspector）,基于节点的预处理器（data processor）,书签列表（Bookmarks），一些其他内置工具，数据分析和图表可视统计，使用向导，在线商店，其中包括各路大神写的（类库，magic文件，编码集等）。
    <br>当然，最主要，也是最核心的就是下面需要单独介绍的 [模式语言](#imhex-pattern-language)（暂且这么翻译）。
    <br><br>使用场景：
    <br>针对某个实例文件，结合模式语言，展现结构列表，可视化二进制数据。比如[分析 java 字节码](/doc/advance/bytecode.md#使用imhex分析)，[RIFF,ANI 文件格式分析](/corner/file-format/riff.md)，或者 [分析 ext2 文件系统](/devops/os/core/fs/ext2.md) 等。
    <br><br>当前使用版本 [v1.37.4](https://github.com/WerWolv/ImHex/releases/tag/v1.37.4)
    <br>优点的话：开源，看不惯的话可以改。马上快 50K 的星标了，你想想。
    <br>缺点目前来说的话，就是，macosx 上面一不小心就闪退了，window上面保存项目文件，不知道死哪儿去了。数据处理器不能复用，另外模式语言部分的文档缺少示例，api 文档定义跟开玩笑一样。
    <br><br>软件使用 [官方文档](https://docs.werwolv.net/imhex)。
    <br>开源仓库 [地址](https://github.com/WerWolv/ImHex)。

* ## ImHex Pattern language

    > [?] ImHex 的精髓所在。简单的理解，就是通过模式编辑器（Pattern editor），通过编程的方式将二进制文件按照内存地址解析成对应的模式(变量)，并通过 Pattern Data 视图窗口将数据展示出来，可以自定义展示数据格式，颜色等，数据布局，格式分布一目了然。类比于 Java POJO中的 toString()。[参考文档](https://docs.werwolv.net/pattern-language)

    + ### 数据类型

        > [!NOTE] 数据类型有好多种，其中包括内建类型(Built-in)，字节序(Endianness)，字面量(Literals)，枚举(Enums)，数组(Arrays)，指针(Pointers)，位域/位段(Bitfields)，结构体(Structs)，共用体(Unions)，Using 别名声明，模板/泛型(Templates)等。
        <br><br>`1.` 内建类型：
        <br><span style='padding-left:1.5em'>无符号整形，范围 0 ~ $2^{8*size}-1$，分别使用 *1B*，*2B*，*3B*，*4B*，*6B*，*8B*，*12B*，*16B* 等字节表示 `u8`，`u16`，`u24`，`u32`，`u48`，`u64`，`u96`，`u128`。
        <br><span style='padding-left:1.5em'>有符号整形，范围 $-(2^{8*size-1})$ ~ $(2^{8*size-1}) - 1 $，分别使用 *1B*，*2B*，*3B*，*4B*，*6B*，*8B*，*12B*，*16B* 等字节表示 `s8`，`s16`，`s24`，`s32`，`s48`，`s64`，`s96`，`s128`。
        <br><span style='padding-left:1.5em'>浮点型，*4B*:`float`，*8B*:`double`。
        <br><span style='padding-left:1.5em'>特殊类型，*1B*:`char`，*2B*:`char16`，*1B*:`bool`，仅用于函数中都的`str`和`auto`。
        <br><br>`2.` 字节序
        <br><span style='padding-left:1.5em'>默认所有的内建类型都使用原生的字节序，也就是电脑默认的（一般是小端序）。但是也提供可覆写的选项，通过`le`，`be`修饰即可。
        <br><br>`3.` 字面量
        <br><span style='padding-left:1.5em'>一般解释为固定值的常量，有以下类型，十进制整形`42`，`-1337`，无符号 32 bit 整形`69U`，有符号 32 bit 整形`69`，`-123`，十六进制整形`0xDEAD`，二进制整形`0b00100101`，八进制整形`0o644`，Float`1.414F`，Double`3.14159`，`1.414D`，Boolean`true`，`false`，字符型`'A'`，字符串`"hello world"`等。
        <br><br>`4.` 枚举
        <br><span style='padding-left:1.5em'>枚举类型一般是第一个的对应的值位 0x00，后面的累加 1，如果显示指定的话，这个之后从显示指定值开始加。
        <br><span style='padding-left:1.5em'>而且还可以枚举范围，比如用`0x00 ... 0x7F`中间的任何一个值来表示一个枚举类型值。
        <br><br>`5.` 数组
        <br><span style='padding-left:1.5em'>有界数组：`u32 array[100] @0x00;`，
        <br><span style='padding-left:1.5em'>无界数组：`char string[] @0x00;`只有碰到`0x00`才停止。
        <br><span style='padding-left:1.5em'>循环数组：`u8 string[while(std::mem::read_unsigned($, 1) != 0xFF)] @ 0x00;`条件符合才停止。`$`表示当前位置，这个表达式的意思是从当前位置一个一个往后读，读到`0xFF`字节停止。
        <br><span style='padding-left:1.5em'>数组优化：内部是可以自动优化的，跟内存布局一致的结构体加`[[static]]`属性一个效果，不理解也没什么关系的。
        <br><span style='padding-left:1.5em'>字符数组；可以出现`char`，`char16`这样的数组，这儿应该跟宽窄字符有关系，char 一个字符一个字节，char16 一个字符使用两个字节。一般也不用具体关心解码值，使用 char 即可。
        <br><br>`6.` 指针
        <br><span style='padding-left:1.5em'>普通指针形如：`u16 *pointer : u32 @ 0x08;`表示 pointer 在 0x08 这个位置使用之后四个字节保存地址，地址对应的值指向一个占用两个字节的 u16 无符号整形值。 
        <br><span style='padding-left:1.5em'>数组指针形如：`u32 *pointerToArray[10] : s16 @ 0x10;`表示 pointer 在 0x10 这个位置使用之后两个字节保存地址，地址对应的值指向一个数组，这个数组中的每一元素是四个字节的 u32。 
        
    + ### 变量放置
    + ### 命令空间
    + ### 表达式
    + ### 函数
    + ### 控制流
    + ### 输入/输出变量
    + ### 属性
    + ### 预处理器
    + ### 导入模块
    + ### 注释
    + ### 内存段

* ## Reference

    + https://github.com/WerWolv/ImHex
    + https://docs.werwolv.net/imhex [软件文档]
    + https://docs.werwolv.net/pattern-language [模式语言文档]