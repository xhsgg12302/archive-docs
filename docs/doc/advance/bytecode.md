* ## Intro(ByteCode)

    <!-- panels:start -->
    <!-- div:left-panel-55 -->
    !> 参考
    <br>[JDK8 JVM虚拟机实现规范 Chapter 4. The class File Format](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html#jvms-4.1)，包括[常量池](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html#jvms-4.4)数据结构
    <br>[JDK8 JVM虚拟机实现规范 Chapter 6. The Java Virtual Machine Instruction Set](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html)
    <br><br>工具：
    <br>进制转换`echo 'ibase=16;12'| bc`
    <br>十六进制转utf-8`echo -n "6E756D" | xxd -r -p | iconv -f utf-8`

    ?> 下面为java源代码和class字节码。编译使用`-g`参数，会包含 [***LocalVariableTable***](./javastrace.md#localvariabletable) 。
    <br><br>用vim打开class文件`vim -b Hello.class`。[不加`-b`会在末尾追加`0x0A`多一个字节]
    <br>并且使用xxd工具查看`:%!xxd -u`;
    <br><br>右边为使用`javap -v Hello.class`工具输出的字节码信息。
    ```java
    public class Hello { 
        private int num;
        public int foo() {
            return num;
        } 
    }
    ```
    ```shell
    00000000: CAFE BABE 0000 0034 0013 0A00 0400 0F09  .......4........
    00000010: 0003 0010 0700 1107 0012 0100 036E 756D  .............num
    00000020: 0100 0149 0100 063C 696E 6974 3E01 0003  ...I...<init>...
    00000030: 2829 5601 0004 436F 6465 0100 0F4C 696E  ()V...Code...Lin
    00000040: 654E 756D 6265 7254 6162 6C65 0100 0366  eNumberTable...f
    00000050: 6F6F 0100 0328 2949 0100 0A53 6F75 7263  oo...()I...Sourc
    00000060: 6546 696C 6501 000A 4865 6C6C 6F2E 6A61  eFile...Hello.ja
    00000070: 7661 0C00 0700 080C 0005 0006 0100 0548  va.............H
    00000080: 656C 6C6F 0100 106A 6176 612F 6C61 6E67  ello...java/lang
    00000090: 2F4F 626A 6563 7400 2100 0300 0400 0000  /Object.!.......
    000000a0: 0100 0200 0500 0600 0000 0200 0100 0700  ................
    000000b0: 0800 0100 0900 0000 1D00 0100 0100 0000  ................
    000000c0: 052A B700 01B1 0000 0001 000A 0000 0006  .*..............
    000000d0: 0001 0000 0001 0001 000B 000C 0001 0009  ................
    000000e0: 0000 001D 0001 0001 0000 0005 2AB4 0002  ............*...
    000000f0: AC00 0000 0100 0A00 0000 0600 0100 0000  ................
    00000100: 0400 0100 0D00 0000 0200 0E              ...........
    ```
    <!-- div:right-panel-45 -->
    ```shell
    Classfile /path/to/bytecode/Hello.class
    Last modified 2024-3-6; size 267 bytes
    MD5 checksum 4792914d35d8b3a70c01ee4fd462f512
    Compiled from "Hello.java"
    public class Hello
    minor version: 0
    major version: 52
    flags: ACC_PUBLIC, ACC_SUPER
    Constant pool:
    #1 = Methodref          #4.#15         // java/lang/Object."<init>":()V
    #2 = Fieldref           #3.#16         // Hello.num:I
    #3 = Class              #17            // Hello
    #4 = Class              #18            // java/lang/Object
    #5 = Utf8               num
    #6 = Utf8               I
    #7 = Utf8               <init>
    #8 = Utf8               ()V
    #9 = Utf8               Code
    #10 = Utf8               LineNumberTable
    #11 = Utf8               foo
    #12 = Utf8               ()I
    #13 = Utf8               SourceFile
    #14 = Utf8               Hello.java
    #15 = NameAndType        #7:#8          // "<init>":()V
    #16 = NameAndType        #5:#6          // num:I
    #17 = Utf8               Hello
    #18 = Utf8               java/lang/Object
    {
    public Hello();
        descriptor: ()V
        flags: ACC_PUBLIC
        Code:
        stack=1, locals=1, args_size=1
            0: aload_0
            1: invokespecial #1                  // Method java/lang/Object."<init>":()V
            4: return
        LineNumberTable:
            line 1: 0

    public int foo();
        descriptor: ()I
        flags: ACC_PUBLIC
        Code:
        stack=1, locals=1, args_size=1
            0: aload_0
            1: getfield      #2                  // Field num:I
            4: ireturn
        LineNumberTable:
            line 4: 0
    }
    SourceFile: "Hello.java"
    ```
    <!-- panels:end -->

    + ### 文件定义

        ?> Java字节码类文件（.class）是Java编译器编译Java源文件（.java）产生的“目标文件”。它是一种8位字节的二进制流文件， 各个数据项按顺序紧密的从前向后排列， 相邻的项之间没有间隙， 这样可以使得class文件非常紧凑， 体积轻巧， 可以被JVM快速的加载至内存， 并且占据较少的内存空间（方便于网络的传输）。
        <br><br>Java源文件在被Java编译器编译之后， 每个类（或者接口）都单独占据一个class文件， 并且类中的所有信息都会在class文件中有相应的描述， 由于class文件很灵活， 它甚至比Java源文件有着更强的描述能力。
        <br><br>class文件中的信息是一项一项排列的， 每项数据都有它的固定长度， 有的占一个字节， 有的占两个字节， 还有的占四个字节或8个字节。数据项的不同长度分别用
        <br>`u1, u2, u4, u8`表示，分别表示一种数据项在class文件中占据`1个字节， 2个字节， 4个字节，8个字节`。 可以把u1, u2, u3, u4看做class文件数据项的“类型” 。

    + ### 文件结构

        ?> 一个典型的class文件分为：
        <br>`MagicNumber`，`Version`，`Constant_pool`，`Access_flag`，`This_class`，`Super_class`，`Interfaces`，`Fields`，`Methods` 和`Attributes`这十个部分，用一个数据结构可以表示如下：

        | type           | descriptor                             | remark |
        | -------------- | -------------------------------------- | ------ |
        | u4             | magic                                  | 在class文件开头的四个字节， 存放着class文件的魔数。他是一个固定的值：`0XCAFEBABE` 。 也就是说他是判断一个文件是不是class格式的文件的标准。|
        | u2             | minor_version                          | 次版本号 `0000` |
        | u2             | major_version                          | 主版本号 `0034` 对应的十进制为`52` |
        | u2             | constant_pool_count                    | 
        | cp_info        | constant_pool[constant_pool_count -1 ] |
        | u2             | access_flags                           | 保存了当前类的访问权限 |
        | u2             | this_class                             | 保存了当前类的全局限定名在常量池里的索引 |
        | u2             | super_class                            | 保存了当前类的父类的全局限定名在常量池里的索引 |
        | u2             | interfaces_count                       | 指的是当前类实现的接口数目 |
        | u2             | interfaces[interfaces_count]           | 包含interfaces_count个接口的全局限定名的索引的数组 |
        | u2             | fields_count                           | 类变量和实例变量的字段的数量总和 |
        | field_info     | fields[fields_count]                   | 包含字段详细信息的列表 |
        | u2             | methods_count                          | 该类或者接口显示定义的方法的数量 |
        | method_info    | methods[methods_count]                 | 包含方法信息的一个详细列表 |
        | u2             | attributes_count                       | 列表中包含的attribute_info的数量 |
        | attribute_info | attributes[attributes_count]           | 属性可以出现在class文件的很多地方，而不只是出现在attributes列表里。<br>如果是attributes表里的属性，那么它就是对整个class文件所对应的类或者接口的描述；<br>如果出现在fileds的某一项里，那么它就是对该字段额外信息的描述；<br>如果出现在methods的某一项里，那么它就是对该方法额外信息的描述。 |

        !> ***constant_pool*** :
        <br><br> 在class文件中， 位于版本号后面的就是常量池相关的数据项。 常量池是class文件中的一项非常重要的数据。 常量池中存放了文字字符串， 常量值， 当前类的类名， 字段名， 方法名， 各个字段和方法的描述符， 对当前类的字段和方法的引用信息， 当前类中对其他类的引用信息等等。 常量池中几乎包含类中的所有信息的描述， class文件中的很多其他部分都是对常量池中的数据项的引用，比如后面要讲到的this_class, super_class, field_info, attribute_info等， 另外字节码指令中也存在对常量池的引用， 这个对常量池的引用当做字节码指令的一个操作数。此外，常量池中各个项也会相互引用。
        <br><br>常量池是一个类的结构索引，其它地方对“对象”的引用可以通过索引位置来代替，我们知道在程序中一个变量可以不断地被调用，要快速获取这个变量常用的方法就是通过索引变量。这种索引我们可以直观理解为“内存地址的虚拟”。我们把它叫静态池的意思就是说这里维护着经过编译“梳理”之后的相对固定的数据索引，它是站在整个JVM（进程）层面的共享池。
        <br><br>class文件中的项constant_pool_count的值为1, 说明每个类都只有一个常量池。 常量池中的数据也是一项一项的， 没有间隙的依次排放。常量池中各个数据项通过索引来访问， 有点类似与数组， 只不过常量池中的第一项的索引为1, 而不为0, 如果class文件中的其他地方引用了索引为0的常量池项， 就说明它不引用任何常量池项。class文件中的每一种数据项都有自己的类型， 相同的道理，常量池中的每一种数据项也有自己的类型。 常量池中的数据项的类型如下表：

        <!-- panels:start -->
        <!-- div:left-panel-54 -->
        | 常量池中数据项类型          | 类型标志 | 类型描述                       |
        | --------------------------- | -------- | ------------------------------ |
        | CONSTANT_Utf8               | 1        | UTF-8 编码的Unicode字符串 |
        | CONSTANT_Integer            | 3        | int 类型的字面值 |
        | CONSTANT_Float              | 4        | float 类型的字面值 |
        | CONSTANT_Long               | 5        | long 类型的字面值 |
        | CONSTANT_Double             | 6        | double 类型的字面值 |
        | CONSTANT_Class              | 7        | 对一个类或接口的符号引用 |
        | CONSTANT_String             | 8        | String类型字面值 |
        | CONSTANT_Fieldref           | 9        | 对一个字段的符号引用 |
        | CONSTANT_Methodref          | 10       | 对一个类中声明方法的符号引用 |
        | CONSTANT_InterfaceMethodref | 11       | 对接口中声明方法的符号引用 |
        | CONSTANT_NameAndType        | 12       | 对一个字段或方法的部分符号引用 |
        <!-- div:right-panel-46 -->
        ?> 每个数据项叫做一个XXX_info项，比如，一个常量池中一个CONSTANT_Utf8类型的项，就是一个CONSTANT_Utf8_info 。除此之外， 每个info项中都有一个标志值（tag），这个标志值表明了这个常量池中的info项的类型是什么， 从左边的表格中可以看出，一个CONSTANT_Utf8_info中的tag值为1，而一个CONSTANT_Fieldref_info中的tag值为9 。
        <br><br>Java程序是动态链接的， 在动态链接的实现中， 常量池扮演者举足轻重的角色。 除了存放一些字面量之外， 常量池中还存放着以下几种符号引用：
        ***（1） 类和接口的全限定名***
        ***（2） 字段的名称和描述符***
        ***（3） 方法的名称和描述符***
        <br><br>我们有必要先了解一下class文件中的特殊字符串， 因为在常量池中， 特殊字符串大量的出现，这些特殊字符串就是上面说的全限定名和描述符。对于常量池中的特殊字符串的了解，可以参考此文档：[Java class文件格式之特殊字符串_动力节点Java学院整理](https://www.jb51.net/article/116313.htm)
        <!-- panels:end -->

    + ### 实际分析

        - #### magic(u4)

            ?> `CAFE BABE`, class magic。

        - #### version(u4)

            ?> `0000 0034`, 次版本号 0， 主版本号 52，表示jdk8编译 `52.0`.
        
        - #### constant_pool(*)

            ?> 1). constant_pool_count
            <br>紧接着version下来的两个自己是`0013`代表常量池里面包含的常量的数目，因为字节码的常量池是从 ***1*** 开始计数的。这个常量池中包含`18`(0x0013 - 1)个常量.
            <br><br>第1个常量：type`0A`对应到`CONSTANT_Methodref_info`数据项。数据项内容为`0A00 0400 0F`，class_index=4，name_and_type=15。
            <br>第2个常量：type`09`对应到`CONSTANT_Fieldref_info`数据项。数据项内容为`09 0003 0010`，class_index=3，name_and_type=16。
            <br>第3个常量：type`07`对应到`CONSTANT_Class_info`数据项。数据项的内容为`0700 11`，name_index=17。
            <br>第4个常量：type`07`对应到`CONSTANT_Class_info`数据项。数据项的内容为`0700 12`，name_index=18。
            <br>第5个常量：type`01`对应到`CONSTANT_Utf8_info`数据项。数据项的内容为`0100 036E 756D`，length=3，bytes='num'。
            <br>第6个常量：type`01`对应到`CONSTANT_Utf8_info`数据项。数据项的内容为`0100 0149`，length=3，bytes='I'。
            <br>第7个常量：type`01`对应到`CONSTANT_Utf8_info`数据项。数据项的内容为`0100 063C 696E 6974 3E`，length=6，bytes='\<init\>'。
            <br>第8个常量：type`01`对应到`CONSTANT_Utf8_info`数据项。数据项的内容为`01 0003 2829 56`，length=3，bytes='()V'。
            <br>第9个常量：type`01`对应到`CONSTANT_Utf8_info`数据项。数据项的内容为`01 0004 436F 6465`，length=4，bytes='Code'。
            <br>第10个常量：type`01`对应到`CONSTANT_Utf8_info`数据项。数据项的内容为`0100 0F4C 696E 654E 756D 6265 7254 6162 6C65`，length=15，bytes='LineNumberTable'。
            <br>第11个常量：type`01`对应到`CONSTANT_Utf8_info`数据项。数据项的内容为`0100 0366 6F6F`，length=3，bytes='foo'。
            <br>第12个常量：type`01`对应到`CONSTANT_Utf8_info`数据项。数据项的内容为`0100 0328 2949`，length=3，bytes='()I'。
            <br>第13个常量：type`01`对应到`CONSTANT_Utf8_info`数据项。数据项的内容为`0100 0A53 6F75 7263 6546 696C 65`，length=10，bytes='SourceFile'。
            <br>第14个常量：type`01`对应到`CONSTANT_Utf8_info`数据项。数据项的内容为`01 000A 4865 6C6C 6F2E 6A61 7661`，length=10，bytes='Hello.java'。
            <br>第15个常量：type`0C`对应到`CONSTANT_NameAndType_info`数据项。数据项的内容为`0C00 0700 08`，name_index=7，descriptor_index=8。
            <br>第16个常量：type`0C`对应到`CONSTANT_NameAndType_info`数据项。数据项的内容为`0C 0005 0006`，name_index=5，descriptor_index=6。
            <br>第17个常量：type`01`对应到`CONSTANT_Utf8_info`数据项。数据项的内容为`0100 0548 656C 6C6F`，length=5，bytes='Hello'。
            <br>第18个常量：type`01`对应到`CONSTANT_Utf8_info`数据项。数据项的内容为`0100 106A 6176 612F 6C61 6E67 2F4F 626A 6563 74`，length=16，bytes='java/lang/Object'。

            ```c
            struct CONSTANT_Methodref_info {
                u1 tag;                     // 数据项类型
                u2 class_index;             // 该方法所属的类在常量池里的索引
                u2 name_and_type_index;     // 表示该方法的名称和类型的索引。常量池里的变量的索引从1开始。
            }
            struct CONSTANT_Fieldref_info {
                u1 tag;
                u2 class_index;
                u2 name_and_type_index;
            }
            struct CONSTANT_Class_info {
                u1 tag;
                u2 name_index;              // 即是指向常量池中第name_index个常量所表示的Class名称
            }
            struct CONSTANT_Utf8_info {
                u1 tag;
                u2 length;                  // 表示该字符串的字节数。
                u1 bytes[length];           // 该字符串的二进制表示。
            }
            struct CONSTANT_NameAndType_info {
                u1 tag;
                u2 name_index;              // 表示的是该变量或者方法的名称
                u2 descriptor_index;        // 指向该方法的描述符的引用
            }
            
            // 等等... 
            // https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html#jvms-4.4
            ```
        
        - #### access_flags(u2)
            
            ?> `00 21` = bin(0x0001 or  0x0020) = 0x0021,表示`ACC_PUBLIC | ACC_SUPER `

            | Flag Name	| Value	| Interpretation|
            | - | - | - 
            | ACC_PUBLIC	| 0x0001	| Declared public; may be accessed from outside its package. 
            | ACC_FINAL	    | 0x0010    | Declared final; no subclasses allowed.
            | ACC_SUPER	    | 0x0020    | Treat superclass methods specially when invoked by the invokespecial instruction.
            | ACC_INTERFACE | 0x0200	| Is an interface, not a class.
            | ACC_ABSTRACT  | 0x0400	| Declared abstract; must not be instantiated.
            | ACC_SYNTHETIC | 0x1000	| Declared synthetic; not present in the source code.
            | ACC_ANNOTATION| 0x2000	| Declared as an annotation type.
            | ACC_ENUM      | 0x4000	| Declared as an enum type.

        - #### this_class(u2)
            
            ?> `00 03` this_class指向constant pool的索引值，该值必须是CONSTANT_Class_info类型，这里是3，即指向常量池中的第三项，即是`Hello`。

        - #### super_class(u2)
            
            ?> `00 04` super_class存的是父类的名称在常量池里的索引，这里指向第4个常量，即是`java/lang/Object`。

        - #### interfaces_count(u2)

            ?> `00 00` 因为这里没有实现接口，所以就不存在interfces选项，所以这里的interfaces_count为0（0000），所以后面的内容也对应为空。

        - #### interfaces[interfaces_count] (u2)

            ?> 因为上面为0，所以此处没有值。

        - #### fields_count(u2)

            ?> `00 01` 表示成员变量的个数，此处为1个。
        
        - #### fields[fields_count] (*)

            ?> `00 02` access_flags = ACC_PRIVATE。
            <br>`00 05` 指向常量池中的第五个常量，为'num'。
            <br>`00 06` 指向常量池中的第六个常量，为'I'。 上述三个合起来就是`private int num;`
            <br>`00 00` attributes数量，因为这个变量没有附加属性，所以attributes_count=0。

            <!-- panels:start -->
            <!-- div:left-panel-60 -->
            | Flag Name | Value	| Interpretation |
            | - | - | - |
            | ACC_PUBLIC    | 0x0001    |	Declared public; may be accessed from outside its package.
            | ACC_PRIVATE	| 0x0002    |	Declared private; usable only within the defining class.
            | ACC_PROTECTED | 0x0004    |	Declared protected; may be accessed within subclasses.
            | ACC_STATIC    | 0x0008    |	Declared static.
            | ACC_FINAL     | 0x0010    |	Declared final; never directly assigned to after object construction (JLS §17.5).
            | ACC_VOLATILE  | 0x0040    |	Declared volatile; cannot be cached.
            | ACC_TRANSIENT | 0x0080    |	Declared transient; not written or read by a persistent object manager.
            | ACC_SYNTHETIC | 0x1000    |	Declared synthetic; not present in the source code.
            | ACC_ENUM	    | 0x4000    |	Declared as an element of an enum.
            <!-- div:right-panel-40 -->
            ```c
            struct field_info {
                u2             access_flags;
                u2             name_index;
                u2             descriptor_index;
                u2             attributes_count;
                attribute_info attributes[attributes_count];
            }

            struct attribute_info {
                u2 attribute_name_index;
                u4 attribute_length;
                u1 info[attribute_length];
            }
            ```
            <!-- panels:end -->
        
        - #### methods_count(u2)

            ?> `00 02` 表示方法的个数，此处为2个。写了一个，出现了两个，是因为JVM会自动生成一个构造方法`<init>`.
        
        - #### methods[methods_count] (*)

            ?> ***init方法***
            <br>`00 01` access_flags = ACC_PUBLIC。
            <br>`00 07` name_index 指向常量池中的第七个常量，为'\<init\>'。
            <br>`00 08` descriptor_index 指向常量池中的第八个常量，为'()V'。 上述三个合起来就是 ***public void \<init\>()***
            <br>`00 01` attributes_count attributes数量为1.
            <br>`00 09` attribute_name_index 常量池索引为9，为'Code'。
            <br>`00 0000 1D` attribute_length 属性长度，为'29'
            <br><br>`00 0100 0100 0000 052A B700 01B1 0000 0001 000A 0000 0006 0001 0000 0001` 注意此时不包含 ***attribute_name_index*** 和 ***attribute_length*** 属性了。
            <br>`00 01` max_stack表示这个方法运行的任何时刻所能达到的操作数栈的最大深度，这里是0001
            <br>`00 01` max_locals表示方法执行期间创建的局部变量的数目，包含用来表示传入的参数的局部变量，这里是0001.
            <br>`00 0000 05` code_length 表示该方法的所包含的字节码的字节数以及具体的指令码。这里的字节码长度为5，即是后面的5个字节
            <br>`2A` 即 [aload_0 = 42 (0x2a)](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.aload_n)。
            <br>`B700 01` 即 [invokespecial = 183 (0xb7)](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.invokespecial) B7后面的两个字节为对一个常量池中的索引位置。
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;计算方法为 $(indexbyte1 << 8) | indexbyte2$ 也就是对应1， 即' java/lang/Object."\<init\>":()V ' 
            <br>`B1` 即 [return = 177 (0xb1)](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.return)
            <br>`0000` exception_table_length 异常表的长度为0。
            <br>`0001` attributes_count 表示有一个附加属性。
            <br>`000A` attribute_name_index 常量池中的索引位置10，为'LineNumberTable'。
            <br>`0000 0006` attribute_length 长度为6。
            <br>`0001` line_number_table_length = 1。
            <br>`0000` start_pc = 0。
            <br>`0001` line_number = 1。
            <br><br><br> ***foo方法***
            <br>`0001` access_flags = ACC_PUBLIC。
            <br>`000B` name_index 指向常量池中的第11个常量，为'foo'。
            <br>`000C` descriptor_index 指向常量池中的第12个常量，为'()I'。 上述三个合起来就是 ***public int foo()***
            <br>`0001` attributes_count attributes数量为1.
            <br>`0009` attribute_name_index 常量池索引为9，为'Code'。
            <br>`0000 001D` attribute_length 属性长度，为'29'
            <br><br>`0001 0001 0000 0005 2AB4 0002 AC00 0000 0100 0A00 0000 0600 0100 0000 04` 注意此时不包含 ***attribute_name_index*** 和 ***attribute_length*** 属性了。
            <br>`00 01` max_stack表示这个方法运行的任何时刻所能达到的操作数栈的最大深度，这里是0001
            <br>`00 01` max_locals表示方法执行期间创建的局部变量的数目，包含用来表示传入的参数的局部变量，这里是0001.
            <br>`0000 0005` code_length 表示该方法的所包含的字节码的字节数以及具体的指令码。这里的字节码长度为5，即是后面的5个字节
            <br>`2A` 即 [aload_0 = 42 (0x2a)](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.aload_n)
            <br>`B4 0002` 即 [getfield = 180 (0xb4)](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.getfield) B4后面的两个字节为对一个常量池中的索引位置。
            <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;计算方法为 $(indexbyte1 << 8) | indexbyte2$ 也就是对应2， 即' Hello.num:I '。
            <br>`AC` 即 [ireturn = 172 (0xac)](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.ireturn)
            <br>`00 00` exception_table_length 异常表的长度为0。
            <br>`00 01` attributes_count 表示有一个附加属性。
            <br>`00 0A` attribute_name_index 常量池中的索引位置10，为'LineNumberTable'。
            <br>`00 0000 06` attribute_length 长度为6。
            <br>`00 01` line_number_table_length = 1。
            <br>`00 00` start_pc = 0。
            <br>`00 04` line_number = 4。

            <!-- panels:start -->
            <!-- div:left-panel-60 -->
            | Flag Name | Value	| Interpretation |
            | - | - | - |
            | ACC_PUBLIC    | 0x0001    |	Declared public; may be accessed from outside its package.
            | ACC_PRIVATE	| 0x0002    |	Declared private; usable only within the defining class.
            | ACC_PROTECTED | 0x0004    |	Declared protected; may be accessed within subclasses.
            | ACC_STATIC    | 0x0008    |	Declared static.
            | ACC_FINAL     | 0x0010    |	Declared final; never directly assigned to after object construction (JLS §17.5).
            | ACC_VOLATILE  | 0x0040    |	Declared volatile; cannot be cached.
            | ACC_TRANSIENT | 0x0080    |	Declared transient; not written or read by a persistent object manager.
            | ACC_SYNTHETIC | 0x1000    |	Declared synthetic; not present in the source code.
            | ACC_ENUM	    | 0x4000    |	Declared as an element of an enum.
            <!-- div:right-panel-40 -->
            ```c
            struct method_info {
                u2             access_flags;
                u2             name_index;
                u2             descriptor_index;
                u2             attributes_count;
                attribute_info attributes[attributes_count];
            }
            struct attribute_info {
                u2 attribute_name_index;
                u4 attribute_length;
                u1 info[attribute_length];
            }
            struct Code_attribute {
                u2 attribute_name_index;
                u4 attribute_length;
                u2 max_stack;
                u2 max_locals;
                u4 code_length;
                u1 code[code_length];
                u2 exception_table_length;
                {   u2 start_pc;   // 开始位置
                    u2 end_pc;     // 结束位置
                    u2 handler_pc; // 处理异常的代码的开始处
                    u2 catch_type; // 表示会被处理的异常类型，它指向常量池里的一个异常类。当catch_type为0时，表示处理所有的异常，这个可以用来实现finally的功能。
                } exception_table[exception_table_length];
                u2 attributes_count;
                attribute_info attributes[attributes_count];
            }
            struct LineNumberTable_attribute {
                u2 attribute_name_index;
                u4 attribute_length;
                u2 line_number_table_length;
                {   u2 start_pc;
                    u2 line_number;	
                } line_number_table[line_number_table_length];
            }
            ```
            <!-- panels:end -->

        - #### attributes_count(u2)

            ?> `00 01` 表示整个class文件的附加属性，这里有1个。

        - #### attributes[attributes_count] (*)

            ?> `00 0D` attribute_name_index 常量池中索引13的值为 ***SourceFile***
            <br>`00 0000 02` attribute_length 长度为2。
            <br>`00 0E` sourcefile_index 常量池中索引位置为14的值为 ***Hello.java***

            ```c
            struct attribute_info {
                u2 attribute_name_index;
                u4 attribute_length;
                u1 info[attribute_length];
            }
            struct SourceFile_attribute {
                u2 attribute_name_index;
                u4 attribute_length;
                u2 sourcefile_index;
            }
            ```
        
        - #### 整体结构图

            ![](/.images/doc/advance/bytecode/bytecode-struct-01.png ':size=80%')


* ## Reference

    + https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html#jvms-4.1
    + https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html
    + https://blog.csdn.net/weelyy/article/details/78969412
    + https://juejin.cn/post/6844903588716609543
    + https://www.cnblogs.com/chanshuyi/p/jvm_serial_05_jvm_bytecode_analysis.html