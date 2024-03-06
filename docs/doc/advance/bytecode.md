* ## Intro(ByteCode)

    <!-- panels:start -->
    <!-- div:left-panel-40 -->
    ?> 下面为java源代码
    <br><br>右边的是class字节码。使用vim打开class文件`vim Hello.class`，并且使用xxd工具查看`:%!xxd -u`;
    ```java
    public class Hello { 

        private int num;

        public int foo() {

            return num;
        } 
    }
    ```
    <!-- div:right-panel-60 -->
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
    00000100: 0400 0100 0D00 0000 0200 0E0A            ............
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
        ?> 每个数据项叫做一个XXX_info项，比如，一个常量池中一个CONSTANT_Utf8类型的项，就是一个CONSTANT_Utf8_info 。除此之外， 每个info项中都有一个标志值（tag），这个标志值表明了这个常量池中的info项的类型是什么， 从上面的表格中可以看出，一个CONSTANT_Utf8_info中的tag值为1，而一个CONSTANT_Fieldref_info中的tag值为9 。
        <br><br>Java程序是动态链接的， 在动态链接的实现中， 常量池扮演者举足轻重的角色。 除了存放一些字面量之外， 常量池中还存放着以下几种符号引用：
        ***（1） 类和接口的全限定名***
        ***（2） 字段的名称和描述符***
        ***（3） 方法的名称和描述符***
        <br><br>我们有必要先了解一下class文件中的特殊字符串， 因为在常量池中， 特殊字符串大量的出现，这些特殊字符串就是上面说的全限定名和描述符。对于常量池中的特殊字符串的了解，可以参考此文档：[Java class文件格式之特殊字符串_动力节点Java学院整理](https://www.jb51.net/article/116313.htm)
        <!-- panels:end -->

* ## Reference

    + https://blog.csdn.net/weelyy/article/details/78969412 来自JVM[原来](https://blog.csdn.net/n_fly/article/details/114026651)的笔记。
    + https://juejin.cn/post/6844903588716609543
    + https://www.cnblogs.com/chanshuyi/p/jvm_serial_05_jvm_bytecode_analysis.html