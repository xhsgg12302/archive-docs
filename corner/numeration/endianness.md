---
tags: ["endian", "byte", "network"]
---

* ## Intro(ENDIANNESS | 字节序 | 端序 | 尾序)

    > [?] [参考 wiki](https://zh.wikipedia.org/wiki/字节序)，记录这个是因为老是记不住，天天查，所以已自己比较容易记忆的方式将其记录下来，动手过一遍，避免胡翻乱找。
    <br><br>字节序分为大端（Big）和小端（Little）
    <br><span style='padding-left:2em'>**高位低地址，低位高地址被称为大端序（Big &nbsp;&nbsp;）**，参考下图可以理解为 **顺序放置**。</span>
    <br><span style='padding-left:2em'>**低位低地址，高位高地址被称为小端序（Little）**，参考下图可以理解为 **逆序放置**。</span>
    <br><br>大小端序出现的背景：
    <br><span style='padding-left:2em'>计算机都是从低位往高位读取字节数据，而且方便处理，所以出现了小端序。但是小端序不符合人类的阅读习惯，且除了计算机内部之外，其他场合几乎都是大端字节序，比如网络传输一般采用大端序，也被称为 **网络字节序**，或 **网络序**。有 [Berkeley套接字](https://zh.wikipedia.org/wiki/Berkeley套接字) 定义了一组转换函数，用于 16 和 32 位整数在网络序和本机字节序之间的转换。htonl(Host to Network Long(4Byte))，htons(Host to Network Short(2Byte))用于本机序转换到网络序；ntohl，ntohs用于网络序转换到本机序。
    <br><br>![](/.images/corner/numeration/endianness/endianness-layout-01.png ':size=100%')

    + ### 检测处理器字节序

        > [!NOTE] 使用 C 语言程序即可验证：定义一个整形值`int num = 1 = 0x 0000 0000 0000 0001;`，然后强转成字符指针，取第一个字符（1 Byte）判断是否为 0，(小端逆序放置为 1，大端为 0)。
        <br>编辑如下代码为 endian.c
        <br>编译运行：`gcc endian.c -o endian && ./endian`

        ```c [data-file:endian.c]
        #include <stdio.h>

        int main(){
            unsigned int num = 1;
            char *ptr = (char*) &num;

            if (*ptr) printf("Little\n");
            else printf("Big\n");

            return 0;
        }
        ```

* ## Reference
    + https://zh.wikipedia.org/wiki/%E5%AD%97%E8%8A%82%E5%BA%8F