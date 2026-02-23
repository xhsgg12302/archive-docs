---
---

* ## 环境搭建

    ?> 使用跨平台的 [DOSbox](https://www.dosbox.com/) 进行实操。
    <br>下载：[DOSBox-0.74-3-3](https://sourceforge.net/projects/dosbox/files/dosbox/0.74-3/DOSBox-0.74-3-3.dmg/download)　
    <br>文档：[DOSBox-Manual](https://www.dosbox.com/DOSBoxManual.html)
    <br>
    <br> [FreeDOS](https://www.freedos.org/)?
    <br>
    <br> [代码样例](./example.md)


    ```shell
    # 编译
    1、masm demo.asm;
    2、nasm demo.asm -f obj -o nasm.obj

    ➜  demo objconv -h

    Object file converter version 2.56 for x86 and x86-64 platforms.
    Copyright (c) 2025 by Agner Fog. Gnu General Public License.

    Usage: objconv options inputfile [outputfile]

    Options:
    -fXXX[SS]  Output file format XXX, word size SS. Supported formats:
            PE, COFF, ELF, OMF, MACHO

    -fasm      Disassemble file (-fmasm, -fnasm, -fyasm, -fgasm)

    -dXXX      Dump file contents to console.
            Values of XXX (can be combined):
            f: File header, h: section Headers, s: Symbol table,
            r: Relocation table, n: string table.
    ```


    >[!CAUTION|style:flat] 学习完成之后，使用 GBD  进行 32，64 bit  测试。
    <br> 汇编多模块？

    | reg | desc | remark |
    | - | - | - |
    | `cs:ip` | 当前 cpu 执行指令的位置 | 只能通过跳转指令进行修改，而不能使用`mov` |
    | `ds:[*]` <br> `ds:[200+bx]` <br> `ds:200[bx]` <br> `ds:[bx].200` <br> `ds:[bx+si]`、`ds:[bx][si]` <br> `ds:[bx+di]`、`ds:[bx][di]` <br> ...<br>`ds:[200+bx+si]` <br> `ds:[bx+200+si]` <br> `ds:200[bx][si]` <br>`ds:[bx].200[si]` <br>`ds:[bx][si].200` | 内存取址的**段地址**和**偏移地址** | 1、ds  只能通过寄存器转写，不能直接写入，而且一般结合`bx`使用，在8086CPU中,只有这（bx,si,di,bp）4个寄存器可以用在“[...]”中来进行内存单元的寻址。<br> 2、在[...]中,这4个寄存器可以单个出现,或只能以4种组合出现:bx 和si、bx 和di、bp 和si、bp 和di。<br> 3、只要在[...]中使用寄存器 bp,而指令中没有显性地给出段地址,段地址就默认在 ss 中。[bx] 段地址在 ds: 中|
    | `ss:sp` | 栈顶的位置 | 入栈：高地址向低地址写入数据，`sp=sp-(1\|2)`|
    | `si,di` |  变址寄存器（index），source, destination | |　

    > [!TIP|style:flat|label:JMP跳转指令] ![](/.images/corner/assembly/book/b-masm-jmp-01.png ':size:50%')
    <br>`jmp short    标号`  段内短转移：**076C:0003 EB06 JMP 000B**
    <br>`jmp near ptr 标号` 段内近转移：**076C:0003 E9DA04 JMP 04E0**
    <br>`jmp far  ptr 标号` 段内远转移：**076C:0003 EB06 JMP 000B**、**076C:0003 EA0B016C07 JMP 076C:010B**，同一个段内的话，和段内短转移一致。

    1.  使用 `assume cs:seg` 设置后可以与 cs 寄存器相作用，但是 ds 不会有效果（默认指向 PSP（Program Segment Prefix）段）。还需要在汇编中进行代码赋值设置。
    2.  masm 对形如`mov ax, [0]`类似的指令，把取址当立即数，需要注意。
    3.  每个段貌似是 16 Byte  对齐的。
    4.  搜索命令`s cs:0 l ffff <B8 01 00 BB 01 00...>`.

* ## Reference

    - https://www.eng.auburn.edu/~sylee/ee2220/8086_instruction_set.html 【8086/iAPX 86 指令集】[backup](https://htmlpreview.github.io/?https://github.com/xhsgg12302/archive-docs/blob/master/corner/assmebly/8086_instruction_set.html)
    - https://en.wikipedia.org/wiki/Intel_8086#Registers_and_instruction 【8086/iAPX 86 寄存器】
    - https://en.wikibooks.org/wiki/X86_Assembly/16,_32,_and_64_Bits
    - https://ee.usc.edu/~redekopp/cs356/slides/CS356Unit4_x86_ISA.pdf
    - http://staff.ustc.edu.cn/~zhoudf/2014summer/debug.pdf 【debug 命令手册】
    - http://cc.bjtu.edu.cn:81/meol/analytics/resPdfShow.do;jsessionid=2EB54504B8498E0EA5C816F038138104?resId=299730&lid=12290 【(trubo c debug) TD 使用手册】
    - https://forum.winworldpc.com/discussion/13025/request-borland-turbo-debugger-1-5 【trubo debugger 1.5 软盘】
    - https://learn.microsoft.com/en-us/cpp/assembler/masm/microsoft-macro-assembler-reference?view=msvc-170 【masm 语法】
    - https://sourceware.org/gdb/current/onlinedocs/gdb 【gdb 手册】
    - https://gitlab.com/tkchia/gcc-ia16