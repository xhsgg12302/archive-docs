---
tags: ["hex", "editor", "tools"]
---

* ## Intro(ImHex | 十六进制编辑器)

    > [!] 一个瑞士小孩哥使用 C++23 写的开源 **十六进制编辑器**，主要包括十六进制预览（Hex editor），单多字节数据编码（data inspector）,基于节点的预处理器（data processor）,书签列表（Bookmarks），一些其他内置工具，数据分析和图表可视统计，使用向导，在线商店，其中包括各路大神写的（类库，magic文件，编码集等）。
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
    <br><br>如果有`main`函数会直接调用。

    + ### 前期准备

        ```shell [data-file:字节数据]
        4D 5A 90 00 03 00 00 00 04 00 00 00 FF 01 00 00 B8 00 00 00 00 00 06 00 40 00 00 00 00 00 1A 41 00 00 20 00 40 00 21 00 1A 2D 44 54 FB 21 09 40 00 00 00 05 00 80 C3 8D 00 01 02 00 00 00 00 00 54 C9 A7 0A F5 A0 A5 0B 52 69 63 68 F4 A0 A5 0B 00 01 02 68 65 6C 6C 6F 00 69 51 44 75 2E 00 00 00 00 00 00 02 94 5F 00 00 00 00 00 00 00 00 00
        ```

        > [!WARNING|label:准备一份分析用的二进制数据] 复制如上字节数据，使用如下命令写入到 im-type 文件里面：`echo '4D 5A 90 ...' | tr -d ' ' | xxd -p -r > im-type`

    + ### 数据类型

        > [!NOTE] 数据类型有好多种，其中包括内建类型(Built-in)，字节序(Endianness)，字面量(Literals)，枚举(Enums)，数组(Arrays)，指针(Pointers)，位域/位段(Bitfields)，结构体(Structs)，共用体(Unions)，Using 别名声明，模板/泛型(Templates)等。

        - #### 1.内建类型

            > [?] 无符号整形，范围 $[0 , 2^{8*size}-1]$，<span style='padding-left:7.5em'>分别使用 *1B*，*2B*，*3B*，*4B*，*6B*，*8B*，*12B*，*16B* 等字节表示 `u8`，`u16`，`u24`，`u32`，`u48`，`u64`，`u96`，`u128`。
            <br>有符号整形，范围 $[-(2^{8*size-1})$ , $(2^{8*size-1}) - 1]$，分别使用 *1B*，*2B*，*3B*，*4B*，*6B*，*8B*，*12B*，*16B* 等字节表示 `s8`，`s16`，`s24`，`s32`，`s48`，`s64`，`s96`，`s128`。
            <br>浮点型，*4B*:`float`，*8B*:`double`。
            <br>特殊类型，*1B*:`char`，*2B*:`char16`，*1B*:`bool`，仅用于函数中都的`str`和`auto`。
            <br><br>![](/.images/other/misc/imhex/imhex-pattern-type-builtin-01.png ':size=100%')

            ```rust
            import std.io;

            fn format_double(ref double value){
                return std::format("{:.15f}", value);
            };

            u8     u1        @0x0c;
            s8     s1        @0x0c;
            u16    u2        @0x04;
            char   c1        @0x00;
            char16 c2        @0x00;
            bool   b1        @0x0d;
            bool   b2        @0x0e;
            float  f1        @0x1c;
            double pi        @0x28;
            double pi_format @0x28 [[format_read("format_double")]];
            ```

        - #### 2.字节序

            > [?] 默认所有的内建类型都使用原生的字节序，也就是电脑默认的（一般是小端序）。但是也提供可覆写的选项，通过`le`，`be`修饰即可。

        - #### 3.字面量

            > [?] 一般解释为固定值的常量，有以下类型，十进制整形`42`，`-1337`，无符号 32 bit 整形`69U`，有符号 32 bit 整形`69`，`-123`，十六进制整形`0xDEAD`，二进制整形`0b00100101`，八进制整形`0o644`，Float`1.414F`，Double`3.14159`，`1.414D`，Boolean`true`，`false`，字符型`'A'`，字符串`"hello world"`等。
            <br><br>![](/.images/other/misc/imhex/imhex-pattern-type-literals-01.png ':size=100%')

            ```rust
            import std.io;

            // 2 endianness
            le u16 ul        @0x08;
            be u16 ub        @0x08;

            // 3 constant
            s32      c_s32_01     = 42         [[export]];
            s32      c_s32_02     = -1337      [[export]];

            u32      c_u32_01     = 69U        [[export]];
            s32      c_s32_03     = 69         [[export]];
            s32      c_s32_04     = -123       [[export]];

            s16      c_s32_05     = 0xDEAD     [[export]];
            s8       c_s8         = 0b00100101 [[export]];
            s24      c_s24_01     = 0o644      [[export]];

            double   c_d_pi       = 3.14159    [[export]];
            double   c_d_pi_02    = 1.414D     [[export]];

            bool     c_b_01       = 0          [[export]];
            bool     c_b_02       = true       [[export]];
            bool     c_b_03       = 1          [[export]];
            bool     c_b_04       = false      [[export]];
            char     c_c_01       = 'A'        [[export]];

            struct STR{
                str value = "hello world" [[export]];
            };
            STR c_str_01 @0x00;
            ```

        - #### 4.枚举

            > [?] 枚举类型一般是第一个的对应的值位 0x00，后面的累加 1，如果显示指定的话，这个之后从显示指定值开始加。
            <br>而且还可以枚举范围，比如用`0x1A ... 0x2D`中间的任何一个值来表示一个枚举类型值。
            <br><br>![](/.images/other/misc/imhex/imhex-pattern-type-enums-01.png ':size=100%')

            ```rust
            enum StorageType : u16 {
            Plain,    // 0x00
            Compressed = 0x20, // pay attention to endianness. default(native:little).
            Encrypted // 0x11
            };
            StorageType storage[4] @0x20;

            enum Type : u8 {
                A = 0x1A ... 0x2D,
                B = 0x09,
                C = 0x40
            };
            Type types[8] @0x28;
            ```

        - #### 5.数组

            > [?] 有界数组：`u32 array[100] @0x00;`，
            <br>无界数组：`char string[] @0x53;`只有碰到`0x00`才停止。
            <br>循环数组：`u8 string[while(std::mem::read_unsigned($, 1) != 0xFF)] @ 0xFB;`条件符合才停止。`$`表示当前位置，这个表达式的意思是从当前位置一个一个往后读，读到`0xFF`字节停止。
            <br>数组优化：内部是可以自动优化的，跟内存布局一致的结构体加`[[static]]`属性一个效果，不理解也没什么关系的。
            <br>字符数组；可以出现`char`，`char16`这样的数组，这儿应该跟宽窄字符有关系，char 一个字符一个字节，char16 一个字符使用两个字节。一般也不用具体关心解码值，使用 char 即可。
            <br><br>![](/.images/other/misc/imhex/imhex-pattern-type-arrays-01.png ':size=100%')

            ```rust
            import std.mem;

            u8 array[4]   @0x00;
            char string01[] @0x53;
            u8 string02[while(std::mem::read_unsigned($, 1) != 0xFB)] @ 0x28;
            ```

        - #### 6.指针

            > [?] 普通指针形如：`u16 *pointer          : u32 @0x08;`表示 pointer 在 0x08 这个位置使用之后四个字节保存地址，地址对应的值指向一个占用两个字节的 u16 无符号整形值。 
            <br>数组指针形如：`u8  *pointerToArray[8] : s16 @0x22;`表示 pointer 在 0x22 这个位置使用之后两个字节保存地址，地址对应的值指向一个数组，这个数组中的每一元素是一个字节的 u8。 
            <br><br>![](/.images/other/misc/imhex/imhex-pattern-type-pointer-01.png ':size=100%')

            ```rust
            u16 *pointer           : u32   @0x08;
            u8  *pointerToArray[8] : s16   @0x22;
            ```

        - #### 7.位段/位域

            > [?] 位域类似于结构体，但它们处理的是单独的、未对齐的位，以 bit 为单位。它们可用于解码位标志或其他使用少于8位来存储值的类型。通常来说不够一个字节，不足一个字节按一个字节处理，也就是作用范围是 8 bit 的倍数。可以在这个结构里面内嵌正常类型，也可以使用`padding`关键字来填充。
            <br><br>单个字节从低位往高位依次读取，多个字节的时候需要注意字节序问题，具体查看代码中的注释。
            <br><br>![](/.images/other/misc/imhex/imhex-pattern-type-bitfield-01.png ':size=100%')

            ```rust {13-18}
            bitfield Permission {
                r : 1;
                w : 1;
                x : 1;
            };
            Permission perm @0x16;

            enum TestEnum : u8{
                Item01 = 0x11,
                Item02,
            };

            // 0x69     0x51     0x44     0x75     0x2E
            // 01101001 01010001 01000100 01110101 00101110
            // 从起始点最地位依次从右往左读需要的位数。
            // 需要四位 1001，1 0110，此处的单独的 1 下一个字节(0x51)的最低位。
            // 然后继续 1000，0，00010001，101，10，(...)，
            // 内嵌的类型 使用单独的字节，不会在位里面拼凑。
            bitfield TestBitfield {
                regular_value           : 4;    // Regular field, regular unsigned value
                unsigned unsigned_value : 5;    // Unsigned field, same as regular_value
                signed   signed_value   : 4;    // Signed field, interpreting the value as two's complement
                bool     boolean_value  : 1;    // Boolean field, 
                TestEnum enum_value     : 8;    // Enum field, displays the enum value corresponding to that value
                padding                 : 3;
                         byte_value01   : 2;
                u8       byte_value02      ; 
            };
            TestBitfield test01 @0x59;
            ```

        - #### 8.结构体

            > [?] 结构体表示绑定多个内建类型到一个单一的类型。
            <br>也可以使用填充`padding`关键字。
            <br>继承允许复制所有父类成员给子类使用。
            <br>也可以使用匿名成员，就是不用声明变量的成员，只有类型。
            <br>包括条件判断，比如根据成员 A，确定后面的结构是 B，还是 C。
            <br><br>![](/.images/other/misc/imhex/imhex-pattern-type-struct-01.png ':size=100%')

            ```rust {9-11}
            struct Vector3f{
                float x, y;
                padding[2];
                u8 i;
                float z @0x0c; 
            };
            Vector3f vector @0x00;

            // 需要注意如果结构体里面的某个成员是固定地址，则当前成员不参与位置计算，
            // 也就是如果后面还有类型，则在它的值跟随固定地址成员的上一个成员。
            // 也就是 `addressof($)` 位置不会前进。
            struct Vector3f_extend : Vector3f {
                u16 j;
            };
            Vector3f_extend child @0x00;


            enum Type : u8 {  A = 0x54,  B = 0xA0, C = 0x0B };
            struct PacketA {  float x   ; };
            struct PacketB {  u8    y   ; };
            struct PacketC {  char  z[] ; };
            struct Packet  {
                Type type;
                if (type == Type::A) {
                    PacketA packet;
                }
                else if (type == Type::B) {
                    PacketB packet;
                }
                else if (type == Type::C)
                    PacketC packet;
            };
            Packet packet[3] @ 0x40;
            ```

        - #### 9.共用体

            > [?] 共用体和结构体类似，绑定多个类型组成一个新类型，不一样的地方在于，成员不是连续放置的，而是共享起始地址。而且以内部最大的类型占用存储空间。
            <br><br>![](/.images/other/misc/imhex/imhex-pattern-type-union-01.png ':size=100%')

            ```rust
            union Converter {
                u32   integerData;
                u16   shortData;
                float floatingPointData;
                u128  maxData;
            };
            Converter con @0x00;
            ```
            
        - #### 10.别名声明

            > [?] 使用`using`关键字给一个已经存在类型添加一个别名，并且可以附加其他说明符，比如：`using Offset = be u32;`
            <br>还有一种作用就是解决两种类型之间相互引用。可以提前声明。类似：`using TypeName;`

            ```rust
            using B;

            struct A {
                B b;
            };

            struct B {
                A a;
            };
            ```

        - #### 11.模板/泛型

            > [?] 模板可用于用占位符替换自定义类型成员类型的部分，这些占位符可在稍后实例化该类型时定义。类似于 Java 中的泛型。模板语法可以用于结构体，共用体，别名声明。
            <br>可以使用`auto`关键字传递非类型的模板参数，比如，数字，字符串，或变量等。
            <br>也可以在模式内部使用`=`来声明局部变量，用来存储临时数据。
            <br><br>![](/.images/other/misc/imhex/imhex-pattern-type-template-01.png ':size=100%')

            ```rust
            struct MyTemplateStruct<T> {
                T member;
            };

            union MyTemplateUnion<Type1, Type2> {
                Type1 value1;
                Type2 value2;
            };

            using MyTemplateUsing<Type1> = MyTemplateUnion<Type1, u64>;
            MyTemplateUnion<u32, u64> myConcreteUnion01 @ 0x00;
            MyTemplateUsing<u32>      myConcreteUnion02 @ 0x00;


            struct MyType {
                u8   x, y, z;                         // Regular members
                float localVariable = 0.5 [[export]]; // Local variable
            };
            MyType mt @0x00;


            struct Array<T, auto Size> {
                T data[Size];
            };
            Array<u8, 0x10> array @ 0x00;
            ```

    + ### 变量放置

        > [!NOTE] 变量的放置，理解成当前模式开始解析的位置可能比较容易些。就是在变量后面使用`@`符跟随一个地址。上面 [数据类型](#数据类型) 中其实已经演示了，或者使用属性`pointer_base`动态计算地址。
        <br><br>另外也可以不用追加 `@`和地址 来代表一个运行时临时保存数据的全局变量。
        <br>至于文档中提到的 **Calculated pointers**，意思就是 [8.结构体](#8结构体) 中使用的`float z @0x0c;`，用来指定模式解析的具体位置，但这些变量不影响他们所在结构体的总大小（参考结构体代码注释）。
        <br><br>另外在资料查找中，发现 [issue:133](https://github.com/WerWolv/ImHex/issues/133),[issue:364](https://github.com/WerWolv/ImHex/issues/364) 跟 **Calculated pointers** 概念有关系。读完大致可以了解到使用`pointer_base`属性可以动态计算一个新的指针。但是大佬在 issue:364 中给出的 [代码样例 2](https://github.com/WerWolv/ImHex/issues/364#issuecomment-986313497) 有点懵逼，而且报错。貌似这种方式解决不了尺寸在指针数组之后的问题（这种情况很少碰到）。
        <br><br>如下使用`pointer_base`动态计算分割的地址(真正的地址 0x45 分别保存在 0x94 的后四位和 0x5F 的前四位)，模拟 [x86 GDT](https://zh.wikipedia.org/wiki/全局描述符表#/media/File:SegmentDescriptor.svg) 中的情况：
        <br>![](/.images/other/misc/imhex/imhex-pattern-placement-01.png ':size=100%')

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```rust
        import std.ptr;

        fn relative_to_pointer(u16 value){
            // value = 0x5F94 =  (0101) 1111 1001 (0100)    
            //         0x45   =  0100 0101
            u8 part01 = (value & 0b1111000000000000) >> 12; //0b 00000101
            u8 part02 = (value & 0b0000000000001111);       //0b 00000100
            u16 rst = part02 << 4 | part01;                 //0x 45
            return rst - value;
        };

        struct Parent {
            u8 *child[5] : u16 [[pointer_base("relative_to_pointer")]];
        };
        Parent pa @0x65;
        ```
        <!-- div:right-panel-50 -->
        ```rust [data-file:另外的访问方式记录]
        struct Bytes {
            u8 bytes[parent.size];
        };

        struct Test01 {
            // A pointer to an u8 array of size bytes.
            $ += 4;
            u32 size;
            $ -= 8;
            Bytes *offset : u32;
        };

        u32 offset = 0x08;
        Test01 test01 @offset;

        struct Test02 {
            // A pointer to an u8 array of size bytes
            u32 offset;
            u32 size;
        };
        Test02 test02 @offset;
        //u8 bytes[test02.size] @test02[offset]; not work
        u8 bytes[test02.size] @test02.offset;
        ```
        <!-- panels:end -->

    + ### 命令空间
    + ### 表达式
    + ### 函数
    + ### 控制流
    + ### 输入/输出变量

        > [!NOTE] 两个内置值`in`，`out`，可以在 **Pattern editor** 区域下面的 **Settings** tab 里面指定值，然后在程序中使用，对`out` 的修改会同步到此处。
        <br><br>![](/.images/other/misc/imhex/imhex-pattern-inout-01.png ':size=100%')

        ```rust
        import std.io;
    
        u32 inputValue in;
        u32 outputValue out;

        fn main() {
            std::print("hello world");
            outputValue = inputValue * 2;
        };
        ```
    + ### 属性

        > [!NOTE] 属性是一种可以追加到单独变量或者类型的特殊指令。可以理解位扩展信息，或者 Java 中的注解。可以应用多个属性到同一个变量或者类型。

        - #### 变量属性

            > [?] `[[color("RRGGBB")]]`：颜色属性，改变变量的显示颜色。
            <br>`[[name("new_name")]]`：改变 **Pattern data** 中展示的 name,并不影响 **Pattern editor** 代码区域后面的引用名。
            <br>`[[comment("text")]]`：鼠标在变量上悬浮的注释信息。
            <br>`[[format/format_read("formatter_function_name")]]`：使用函数格式化就好了，对于返回值，一般是字符串，其他类型使用默认对应的的格式化形式。
            <br>`[[format_write("formatter_function_name")]]`：这个一度让我摸不着头脑，苦苦搜索之后在 [Modifying pattern values](https://docs.werwolv.net/imhex/views/pattern-data#modifying-pattern-values) 发现了用法，
            <br><span style='padding-left:3em'>大致就是：在 **Pattern data** 区域中的 Value 字段双击可以编辑，将你输入的字符串按照给定的写函数转换成字节覆盖原本模式内容的值。
            <br>`[[format_entries/format_read_entries("formatter_function_name")]]`：与上面的对应，不过这个只针对数组，而且覆盖所有数组实体的默认显示格式，而不仅仅是数组模式本身。
            <br>`[[hidden]]`：不在 **Pattern data** 中展示，并不影响 **Pattern editor** 代码区域后面的引用名。
            <br>`[[inline]]`：仅被用于数组或类结构体中。可视化地将该变量的所有成员内联到父作用域。在保持模式结构的同时，可以使显示的树结构变平，避免不必要的缩进。
            <br>`[[transform("transformer_function_name")]]`：指定一个函数，在通过`.`语法（某种结构体）访问该变量之前，将执行该函数对从该变量读取的值进行预处理。
            <br>`[[pointer_base("pointer_base_function_name")]]`：根据函数重新计算基址，并以当前位置的值为偏移量。只针对指针有效。可以参考[变量放置](#变量放置) 里面的使用情况。
            <br><span style='padding-left:3em'>使用官方类库中的 [relative_to_pointer](https://github.com/WerWolv/ImHex-Patterns/blob/master/includes/std/ptr.pat)，会报错，不知道为什么。
            <br>`[[no_unique_address]]`：由此属性标记的变量将被放置在内存中，但不会增加当前光标的位置。跟 [8.结构体](#8结构体) 中的`@`使用一样，当前成员不参与位置计算，且一般与`[[hidden]]`配合使用。
            <br>`[[single_color]]`：强制所有类结构体成员或数组中的每个元素都使用相同的颜色显示。
            <br><br>![](/.images/other/misc/imhex/imhex-pattern-attributes-01.gif ':size=100%')

            ```rust
            import std.io;
            //import std.ptr;
            import type.mac;
            import type.size;

            fn format_array(ref auto array) {
                type::MACAddress macAddress @ 0x40;
                return macAddress;
            };
            fn format_read_u8(ref auto byte){ return std::format("0x{:02X}", byte.value); };
            fn format_write_u8(str value){ return 0xAA; };
            fn trans_value(ref u8 stru){  return stru + 3; };
            fn relative_to_pointer(u128 addr){ return $;};

            struct Wrapper   { u8 value; } [[format_read("format_array")]];
            struct Transform { u8 value [[transform("trans_value")]]; };
            struct NoUniqueAddr01 { u8 a [[no_unique_address]]; u8 b;};
            struct NoUniqueAddr02 { u8 a [[no_unique_address]]; u8 b;}[[single_color]];

            u8 red                    @0x00 [[color("FF0000"),name("red-new-name"),comment("text")]];
            u8 green                  @0x10 [[color("00FF00")]];
            u8 blue                   @0x20 [[color("0000FF")]];
            type::Size<u32> size      @0x24;
            Wrapper         wrapper01 @0x40;
            Wrapper         wrapper02 @0x41 [[inline]];
            // The [[format_entries]] attribute can only be applied to dynamic array types.
            Wrapper         data[3]   @0x43 [[format_entries("format_read_u8"),format_write_entries("format_write_u8")]];
            u8 hidden                 @0x00 [[hidden]];
            Transform       transform @0x4C;

            // use lib std.ptr.relative_to_pointer can occur error, don't know why.
            u8 *child[2] : u16        @0x08 [[pointer_base("relative_to_pointer")]];

            // `no_unique_address` and `single_color` demonstration
            NoUniqueAddr01 nqa01      @0x5A;
            NoUniqueAddr02 nqa02      @0x5A;
            ```

        - #### 类型属性

            > [!NOTE] `[[static]]`：这个属性标注在自定义类型上表示当前类型的内存布局和尺寸大小完全一致，有助于优化相关的东西。比如定义了一个结构体，里面没有任何流程控制，表示每个实例对象都是一样的结构，但是有流程控制的就不一样了。比如 [8.结构体](#8结构体) 中的 **Packet**。
            <br>`[[bitfield_order(ordering, size)]]`：专门对位域的属性，order表示解析顺序，size表示长度，尤其是 MostToLeast 的时候告诉它起始地址。
            <br>`[[sealed]]`：属性用于类结构体，或者位域，它会密封实现细节，结合[[format]]只展示自身的值。
            <br>`[[highlight_hidden]]`：和 [[hidden]] 属性一样，但是这个只是不高亮，但并没有影响变量在 **Pattern view** 中的展示。
            <br>`[[export]]`：默认局部变量不会展示在输出中，并且只是存储临时值在模式内部，增加这个属性将把它变成普通变量。官方描述的”对于在输出之前进行预处理很有帮助“，不知其意。
            <br>`[[fixed_size(size)]]`：强制一个结构体使用指定的尺寸，当小于的时候，填充到合适尺寸，大于的时候，报错。
            <br><br>![](/.images/other/misc/imhex/imhex-pattern-attributes-02.png ':size=100%')

            ```rust
            import std.core;
            import std.io;

            // not use control flow
            struct Static { u8 a; u16 b; u8 c;}[[static]];
            Static sta @0x00;

            // std::core::BitfieldOrder::MostToLeastSignificant
            // std::core::BitfieldOrder::LeastToMostSignificant
            bitfield Permission {
                r : 1;
                w : 1;
                x : 1;
            }[[bitfield_order(std::core::BitfieldOrder::MostToLeastSignificant,4)]];
            Permission perm @0x6E;

            struct Sealed { u8 a; u16 b; u8 c;}[[sealed]];
            Sealed sea @0x22;

            u8 highlight_hidden  @0x08 [[highlight_hidden]];

            struct Export { u8 a; u8 local_variable = 0x10 [[export]];};
            Export export @0x28; 

            struct FixedSize { u8 a; u8 b; u8 c;}[[fixed_size(4)]];
            FixedSize fs01 @0x40;
            ```

    + ### 预处理器
    + ### 导入模块
    + ### 注释
    + ### 内存段

        > [!NOTE] 内存段是一种可以创建附加数据缓冲区的方式，比如自定义一些数据，填充到这个段里面，然后应用到某个模式。这主要用于分析需要在运行时生成的数据，例如压缩、加密或以其他方式转换的数据。在 **Pattern editor**区域下面的 **Sections** tab 中可以选择某个段，点击后面的 `view content` 可以查看详情。
        <br><br>![](/.images/other/misc/imhex/imhex-pattern-sections-01.png ':size=100%')

        ```rust
        #include <std/mem.pat>

        std::mem::Section mySection = std::mem::create_section("My Section");

        u8 sectionData[0x100] @ 0x00 in mySection;

        sectionData[0] = 0xAA;
        sectionData[1] = 0xBB;
        sectionData[2] = 0xCC;
        sectionData[3] = 0xDD;

        sectionData[0xFF] = 0x00;

        u32 value @ 0x00 in mySection;
        ```

* ## Reference

    + https://github.com/WerWolv/ImHex
    + https://docs.werwolv.net/imhex [软件文档]
    + https://docs.werwolv.net/pattern-language [模式语言文档]