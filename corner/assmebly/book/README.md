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


    + ### 8086 工具集

        下载[8086.rar](https://drive.usercontent.google.com/download?id=1LxcL2FIuhhWb0_IJqrXsexDtXsfBPwRW&export=download&authuser=0)，直接解压到新建目录 8086 里面。

    + ### Trubo C 2.0 c语言开发环境搭建

        从[页面](https://archive.org/details/msdos_borland_turbo_c_2.01) 找到 download options 区域，选中 zip 格式 [BorlandTurboC201-megapack.zip](https://archive.org/download/msdos_borland_turbo_c_2.01/BorlandTurboC201-megapack.zip) 下载 Trubo C 2.0 开发环境。直接解压就好了，里面包含 TC 目录就是，复制到 8086 里面，方便环境变量配置。

    + ### Trubo debugger 1.5 环境安装

        在[这儿](https://mega.nz/file/05dxkCZD#GCNozF8KAGWjVqSQBIC4-bVkH9Yp89VuqAg6Dq-2sTg)下载所需要的软盘文件（Turbo Debugger SN Y1F0111452 1988 Borland 3.5 720k gw27 F7Plus Disk1of1.rar）。解压后里面有三个文件 img、scp、txt。
        将解压的文件及里面的三个文件重命名为**TDB3_5**，因为当前环境对文件名的限制。并在 DOSBox 的配置文件 _~/Library/Preferences/DOSBox\ 0.74-3-3\ Preferences_ 中通过命令`imgmount f /Users/stevenobelia/Documents/project_assembly_test/floppy/TDB3_5/TDB3_5.img -t floppy`挂载。

        启动 DOSBox，切换到 f 盘，通过 dir 可以看到里面的内容。

        执行 install.exe 可以出现安装界面。source drive 选择 f:，另外一个目的地，输入我们挂载的 `e:\` 就行。之后选择`Start Installation`就行，可以看到软盘里面的内容被安装到了我们本地。

        其中 **TDUTIL.ARC** 是一个压缩包，正常使用`UNPACK.COM`去解压，但是这个软盘里面没带，所以可以使用另一个叫`unar`的工具去解压。解压出来的内容包括`TDUMP.EXE`工具。

        ![](/.images/corner/assembly/book/trubo-debugger-install-01.png ':size=45%') ![](/.images/corner/assembly/book/trubo-debugger-install-02.png ':size=49%')

* ## Reference

    - https://www.eng.auburn.edu/~sylee/ee2220/8086_instruction_set.html 【8086/iAPX 86 指令集】[backup](https://htmlpreview.github.io/?https://github.com/xhsgg12302/archive-assets/blob/d4be8742ae69e7b493be336346c057fa82ecc2e8/corner/assembly/book/8086_instruction_set.html)
    - https://en.wikipedia.org/wiki/Intel_8086#Registers_and_instruction 【8086/iAPX 86 寄存器】
    - https://en.wikibooks.org/wiki/X86_Assembly/16,_32,_and_64_Bits
    - https://ee.usc.edu/~redekopp/cs356/slides/CS356Unit4_x86_ISA.pdf
    - http://staff.ustc.edu.cn/~zhoudf/2014summer/debug.pdf 【debug 命令手册】
    - http://cc.bjtu.edu.cn:81/meol/analytics/resPdfShow.do;jsessionid=2EB54504B8498E0EA5C816F038138104?resId=299730&lid=12290 【(trubo c debug) TD 使用手册】
    - https://forum.winworldpc.com/discussion/13025/request-borland-turbo-debugger-1-5 【trubo debugger 1.5 软盘】 [backup](https://github.com/xhsgg12302/archive-assets/blob/cbfe99c87bc3e883e6bbb9938b58b8d35c394d88/corner/assembly/book/Turbo%20Debugger%20SN%20Y1F0111452%201988%20Borland%203.5%20720k%20gw27%20F7Plus%20Disk1of1.rar)
    - https://learn.microsoft.com/en-us/cpp/assembler/masm/microsoft-macro-assembler-reference?view=msvc-170 【masm 语法】
    - https://sourceware.org/gdb/current/onlinedocs/gdb 【gdb 手册】
    - https://gitlab.com/tkchia/gcc-ia16