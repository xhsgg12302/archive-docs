---
---

* ## Intro(汇编语言(第4版)王爽-阅读笔记)

    + ### 9.1 复制指令(8BC3)到指定位置

        <!-- panels:start -->
        <!-- div:title-panel -->
        > [?] 有如下程序段,添写两条指令,使该程序在运行中将。处的一条指令复制到s0处。
        <!-- div:left-panel-50 -->
        <!-- tabs:start -->
        ##### **question**
        ```masm {7-8}
        ;
        assume cs:codesg
        codesg segment
            s:  mov ax, bx           ; mov ax,bx的机器码占两个字节
                mov si, offset s
                mov di, offset s0
                ...
                ...
            s0: nop                  ; nop 的机器码占一个字节
                nop
        codesg ends
        end s
        ```
        <!-- tabs:end -->
        <!-- div:right-panel-50 -->
        <!-- tabs:start -->
        ##### **masm**
        ```masm {7-8}
        ;
        assume cs:codesg
        codesg segment
            s:  mov ax, bx
                mov si, offset s
                mov di, offset s0
                mov ax, cs:[si]
                mov cs:[di], ax
            s0: nop
                nop
        codesg ends
        end s
        ```
        ##### **nasm**
        ```nasm
        section .text
            global _start

        _start:
            mov ax, 2h
            mov bx, 3h
            times 3 add ax,bx

            mov ax, 4c00h
            int 21h

        section .data
            msg db 'Hello, world!$$'
        ```
        <!-- tabs:end -->
        <!-- panels:end -->


    + ### 9.2 利用jcxz指令查找内存中的内容

        <!-- panels:start -->
        <!-- div:title-panel -->
        > [?] 补全程序,利用 jcxz 指令，实现在内存2000H 段中查找第一个值为0的字节,找到后,将它的偏移地址存储在 dx 中。
        <!-- div:left-panel-50 -->
        <!-- tabs:start -->
        ##### **question**
        ```masm {9-12}
        ; 
        assume cs:code

        code segment
            start:  mov ax,2000H
                    mov ds,ax
                    mov bx, 0

            s:      ...
                    ...
                    ...
                    ...
                    jmp short s

            ok:     mov dx,bx
                    mov ax,4c00h
                    int 21h
        code ends
        end start
        ```
        <!-- tabs:end -->
        <!-- div:right-panel-50 -->
        <!-- tabs:start -->
        ##### **masm**
        ```masm {9-12}
        ;
        assume cs:text

        text segment
            start:  mov ax, 2000H
                    mov ds, ax
                    mov bx, 0

            s:      mov ch, 0
                    mov cl, ds:[bx]
                    jcxz ok
                    inc bx
                    jmp short s

            ok:     mov dx, bx
                    mov ax, 4c00h
                    int 21h
        text ends
        end start
        ```
        ##### **nasm**
        ```nasm
        ```
        <!-- tabs:end -->
        <!-- panels:end -->

    + ### 9.3 使用loop指令查找内存中的内容

        <!-- panels:start -->
        <!-- div:title-panel -->
        > [?] 补全程序,利用 loop指令,实现在内存2000H 段中查找第一个值为0的字节,找到后,将它的偏移地址存储在 dx 中
        <!-- div:left-panel-50 -->
        <!-- tabs:start -->
        ##### **question**
        ```masm {10}
        ;
        assume cs:code

        code segment
            start:  mov ax, 2000H
                    mov ds, ax
                    mov bx, 0
            s:      mov cl, [bx]
                    mov ch, 0
                    ...
                    inc bx
                    loop s
            ok:     dec bx              ; dec 指令的功能和inc相反,dec by 进行的操作为: (bx) = (bx) -1
                    mov dx, bx
                    mov ax, 4c00h
                    int 21h
        code ends
        end start 
        ```
        <!-- tabs:end -->
        <!-- div:right-panel-50 -->
        <!-- tabs:start -->
        ##### **masm**
        ```masm {10}
        ;
        assume cs:text

        text segment
            start:  mov ax, 2000H
                    mov ds, ax
                    mov bx, 0
            s:      mov cl, [bx]
                    mov ch, 0
                    inc cx
                    inc bx
                    loop s
            ok:     dec bx
                    mov dx, bx
                    mov ax, 4c00h
                    int 21h
        text ends
        end start
        ```
        ##### **nasm**
        ```nasm
        ```
        <!-- tabs:end -->
        <!-- panels:end -->

    + ### 实验8 分析一个奇怪的程序

        <!-- panels:start -->
        <!-- div:title-panel -->
        > [?] 分析下面的程序,在运行前思考:这个程序可以正确返回吗?
        <br>运行后再思考:为什么是这种结果?
        <br>通过这个程序加深对相关内容的理解。
        <!-- div:left-panel-50 -->
        <!-- tabs:start -->
        ##### **question**
        ```masm
        ;
        assume cs:text

        text segment
                    mov ax, 4c00h       ;B8 00 4C
                    int 21h             ;CD 21

            start:  mov ax, 0           ;B8 00 00
            s:      nop                 ;90
                    nop                 ;90

                    mov di, offset s
                    mov si, offset s2
                    mov ax, cs:[si]
                    mov cs:[di], ax

            s0:     jmp short s

            s1:     mov ax, 0           ;B8 00 00
                    int 21h             ;CD 21
                    mov ax, 0           ;B8 00 00

            s2:     jmp short s1        ;EB F6
                    nop

        text ends
        end start
        ```
        <!-- tabs:end -->
        <!-- div:right-panel-50 -->
        <!-- tabs:start -->
        ##### **masm**
        ```masm [data-file:explain]
        ;
        ;1、从 start 开始 3 byte 没用的指令，外加 2 byte nop 占位
        ;2、复制 s2 处的 2 byte [EB F6] 到 s 处，替换两个 nop
        ;3、继续执行到 s0，跳到 s 处执行刚才复制的 [EB F6]，<mark class='clever'>跟标号无关，用的是偏移</mark>。
        ;    [EB F6]也就是从这条指末尾令往前数（F6 = -10）10 个 byte，
        ;    到了第 5 行 (mov ax, 4c00h) 处开始执行了。
        ;4、后面（int 21h）使用 p 命令执行，正常返回就好了。　

        ```
        ##### **nasm**
        ```nasm
        ```
        <!-- tabs:end -->
        <!-- panels:end -->

    + ### 实验9 根据材料编程

        <!-- panels:start -->
        <!-- div:title-panel -->
        > [!NOTE|style:flat] 80×25 彩色字符模式显示缓冲区(以下简称为显示缓冲区)的结构: 内存地址空间中,B8000H~BFFFFH共 32KB的空间,为 80×25 彩色字符模式的显示缓冲区。向这个地址空间写入数据,写入的內容将立即出现在显示器上。
        <br>在80×25彩色字符模式下,显示器可以显示25行,每行80个字符,每个字符可以有 256 种属性(背景色、前景色、闪烁、高亮等组合信息)。
        <br>这样,一个字符在显示缓冲区中就要占两个字节,分别存放字符的 ASCII 码和属性。80×25模式下,一屏的内容在显示缓冲区中共占4000个字节。显示缓冲区分为8页,每页 4KB(≈4000B),显示器可以显示任意一页的内容。一般情况下,显示第0页的内容。也就是说通常情况下,B8000H〜B8F9FH 中的4000个字节s的内容将出现在显示器上。
        <!-- div:left-panel-50 -->
        <!-- tabs:start -->
        ##### **figure**
        ![](/.images/corner/assembly/book/as-ws/book-memory-80*25.png ':size=99%')
        <!-- tabs:end -->
        <!-- div:right-panel-50 -->
        <!-- tabs:start -->
        ##### **masm**
        ![](/.images/corner/assembly/book/as-ws/book-memory-80*25.gif ':size=99%')
        ##### **nasm**
        ```nasm
        ```
        <!-- tabs:end -->
        <!-- panels:end -->

    + ### 10.2 执行自定义汇编代码

        <!-- panels:start -->
        <!-- div:title-panel -->
        > [?] 下面的程序执行后，ax中的数值为多少?
        <br> ![](/.images/corner/assembly/book/as-ws/book-10-call-ret-01.png ':size=35%')
        <br>
        <br>通过汇编代码(p10.1)跳转到指定位置，然后通过`e 1000:0`写入汇编指令，验证执行效果。
        <!-- div:left-panel-50 -->
        <!-- tabs:start -->
        ##### **question**
        ```masm
        ;
        assume cs:text

        stack segment
            db 16 dup (0)
        stack ends

        text segment
            start:  mov ax, stack
                    mov ss, ax
                    mov sp, 16
                    mov ax, 1000h

                    push ax
                    mov ax, 0
                    push ax
                    retf
        text ends
        end start
        ```
        <!-- tabs:end -->
        <!-- div:right-panel-50 -->
        <!-- tabs:start -->
        ##### **masm**
        ![](/.images/corner/assembly/book/as-ws/book-10-call-ret-01-answer.png ':size=95%')
        ##### **nasm**
        ```nasm
        ```
        <!-- tabs:end -->
        <!-- panels:end -->

    + ### 10.8 mul/div 指令

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        <table>
        <tr><th rowspan="2" width="20%">a*b</th><th colspan="2" width="42%">8bit同乘</th><th colspan="2" width="47%">16bit同乘</th></tr>
        <tr><td>8bit</td><td>16bit</td><td>16bit</td><td>32bit</td></tr>
        <tr><td>a</td><td>al</td> <td></td> <td>ax</td> <td></td> </tr>
        <tr> <td>b</td> <td>reg</td> <td></td> <td>reg</td> <td></td> </tr>
        <tr> <td>rst</td> <td></td> <td>AX</td> <td></td> <td>DX * 1000H + AX</td> </tr>
        </table>

        > [?] `(1)` 两个相乘的数:两个相乘的数,要么都是8位,要么都是 16位。如果是8位,一个默认放在AL中,另一个放在8位 reg或内存字节单元中;如果是 16位,一个默认在 AX中,另一个放在 16位 reg 或内存字单元中。
        <br>`(2)` 结果:如果是 8位乘法,结果默认放在 AX 中;如果是 16位乘法,结果高位默认在 DX 中存放,低位在 AX 中放。
        <br><br>![](/.images/corner/assembly/book/as-ws/book-10-mul-01.png ':size=95%')
        <!-- div:right-panel-50 -->
        <table>
        <tr><th rowspan="2" width="20%">a/b</th><th colspan="2" width="41%">8bit除数</th><th colspan="2" width="49%">16bit除数</th></tr>
        <tr><td>8bit</td><td>16bit</td><td>16bit</td><td>32bit</td></tr>
        <tr><td>a</td><td></td> <td>ax</td> <td></td> <td>DX * 1000H + AX</td> </tr>
        <tr> <td>b</td> <td>reg</td> <td></td> <td>reg</td> <td></td> </tr>
        <tr> <td>rst</td> <td></td> <td>AH(余数)、AL(商)</td> <td></td> <td>DX(余数)、AX(商)</td> </tr>
        </table>

        > [?] `(1)` 除数:有8位和16位两种,在一个 reg 或内存单元中。
        <br>`(2)` 被除数:默认放在 AX或 DX和 AX中,如果除数为8位,被除数则为 16 位, 默认在AX 中存放;如果除数为 16位,被除数则为32位,在 DX和AX中存放,DX 存放高16位,AX 存放低16位。
        <br>`(3)` 结果:如果除数为 8位,则 AL存储除法操作的商,AH 存储除法操作的余数;如果除数为16位,则AX存储除法操作的商,DX存储除法操作的余数。
        <br><br>![](/.images/corner/assembly/book/as-ws/book-8-div-01.png ':size=95%')
        <!-- panels:end -->

    + ### 10.10 call/ret 指令

        <!-- panels:start -->
        <!-- div:title-panel -->
        > [?] 编程,计算 data 段中第一组数据的3次方,结果保存在后面一组 dword 单元中。
        <!-- div:left-panel-50 -->
        <!-- tabs:start -->
        ##### **code**
        ```masm [data-cc:298px]
        ;
        assume cs:text

        data segment
            dw 1, 2, 3, 4, 5, 6, 7, 8
            dd 0, 0, 0, 0, 0, 0, 0, 0
        data ends

        text segment
            start:  mov ax, data
                    mov ds, ax
                    mov si, 0
                    mov di, 16

                    mov cx, 8
            s:      mov bx, [si]
                    call cube
                    mov [di], ax
                    mov [di].2, dx
                    add si, 2
                    add di, 4
                    loop s

                    ; for `go 23`，
                    mov ax, 4c00h
                    int 21h

            cube:   mov ax, bx
                    mul bx
                    mul bx
                    ret

        text ends
        end start
        ```
        <!-- tabs:end -->
        <!-- div:right-panel-50 -->
        <!-- tabs:start -->
        ##### **figure**
        ![](/.images/corner/assembly/book/as-ws/book-10-call-ret-02.png ':size=95%')
        ##### **nasm**
        ```nasm
        ```
        <!-- tabs:end -->
        <!-- panels:end -->

    + ### 10.11 批量数据的传递

        <!-- panels:start -->
        <!-- div:title-panel -->
        > [?] 下面看一个例子,设计一个子程序,功能:将一个全是字母的字符串转化为大写。
        <br>
        <br>注意,除了用寄存器传递参数外,还有一种通用的方法是用`栈来传递参数`。关于这种技术请参看[附注4](#附注4-用栈传递参数)。
        <!-- div:left-panel-48 -->
        <!-- tabs:start -->
        ##### **code**
        ```masm
        ;
        assume cs:text

        data segment
            db 'conversation'
        data ends

        text segment
            start:  mov ax, data
                    mov ds, ax
                    mov si, 0                       ;ds: si 指向字符串(批量数据)所在空间的首地址
                    mov cx, 12                      ;cx 存放字符串的长度
                    call capital
                    mov ax, 4c00h
                    int 21h

            capital:and byte ptr [si], 11011111b    ;将ds: si所指单元中的字母转化为大写
                    inc si                          ;ds: si 指向下一个单元
                    loop capital
                    ret
        text ends
        end start
        ```
        <!-- tabs:end -->
        <!-- div:right-panel-51 -->
        <!-- tabs:start -->
        ##### **figure**
        <br>![](/.images/corner/assembly/book/as-ws/book-10-call-ret-03.png ':size=99%')
        ##### **nasm**
        ```nasm
        ```
        <!-- tabs:end -->
        <!-- panels:end -->

    + ### 10.12 寄存器冲突的问题

        <!-- panels:start -->
        <!-- div:title-panel -->
        > [?] 设计一个子程序,功能:将一个全是字母,以 0 结尾的字符串,转化为大写。程序要处理的字符串以 0 作为结尾符,这个字符串可以如下定义:`db 'conversation',0`。
        <br>
        <br>分析：不使用长度参数，以末尾的 0 来判断当前字符串结束，可以使用 jcxz，借助`cx`比较，而上一个转换子程序内部的 loop 指令也会用到`cx`，从而导致内外`cx`冲突。
        <br>
        <br>解决：一种有效的解决办法是，<span style='color:blue'>在子程序中将子程序内部需要用到的寄存器入栈，返回时出栈到相应的寄存器</span>，达到恢复现场的目的。
        <br>这样做的好处是：外部不需要关心子程序内部实现细节，子程序也不用关心外部如何调用。
        <!-- div:left-panel-50 -->
        <!-- tabs:start -->
        ##### **error**
        ```masm {24-35}
        ;
        assume cs:text

        data segment
            db 'word', 0
            db 'unix', 0
            db 'wind', 0
            db 'good', 0
        data ends

        text segment
            start:  mov ax, data
                    mov ds, ax
                    mov si, 0
                    mov cx, 4

            s:      call capital
                    add si, 5
                    loop s

                    mov ax, 4c00h
                    int 21h

            capital:mov cl, [si]
                    mov ch, 0
                    jcxz ok
                    and byte ptr [si], 11011111B
                    inc si
                    jmp short capital



            ok:     ret


        text ends
        end start
        ```
        <!-- tabs:end -->
        <!-- div:right-panel-50 -->
        <!-- tabs:start -->
        ##### **normal**
        ```masm {24-35}
        ;
        assume cs:text

        data segment
            db 'word', 0
            db 'unix', 0
            db 'wind', 0
            db 'good', 0
        data ends

        text segment
            start:  mov ax, data
                    mov ds, ax
                    mov si, 0
                    mov cx, 4

            s:      call capital
                    add si, 5
                    loop s

                    mov ax, 4c00h
                    int 21h

            capital:<mark class='clever' style='background:linear-gradient(-100deg, rgba(250, 226, 133, .3), rgb(64 206 128 / 70%) 95%, rgba(250, 226, 133, .1));'>push cx</mark>
                    <mark class='clever' style='background:linear-gradient(-100deg, rgba(250, 226, 133, .3), rgb(64 206 128 / 70%) 95%, rgba(250, 226, 133, .1));'>push si</mark>

            cp_head:mov cl, [si]
                    mov ch, 0
                    jcxz ok
                    and byte ptr [si], 11011111B
                    inc si
                    jmp short cp_head
            ok:     <mark class='clever' style='background:linear-gradient(-100deg, rgba(250, 226, 133, .3), rgb(64 206 128 / 70%) 95%, rgba(250, 226, 133, .1));'>pop si</mark>
                    <mark class='clever' style='background:linear-gradient(-100deg, rgba(250, 226, 133, .3), rgb(64 206 128 / 70%) 95%, rgba(250, 226, 133, .1));'>pop cx</mark>
                    ret
        text ends
        end start
        ```
        ##### **figure**
        ![](/.images/corner/assembly/book/as-ws/book-10-call-ret-04.png ':size=99%')
        <!-- tabs:end -->
        <!-- panels:end -->

    + ### 实验10 编写子程序

        > [!NOTE|style:flat] 二刷此书，第一次距今已然十载，当时写的这个程序的笔记还在博客[【汇编语言（王爽）实验十 编写子程序】](https://blog.csdn.net/XHS_12302/article/details/52056350)里。期间也偶尔翻到，只剩成就感了，至于里面的东西，别说代码了，就是格式也显得陌生，正好利用这次机会重拾一下，顺便把里面的小问题尽量修复。

        - #### 1.显示字符串

            <!-- panels:start -->
            <!-- div:title-panel -->
            > [?] 问题：显示字符串是现实工作中经常要用到的功能,应该编写一个通用的子程序来实现这个功能。我们应该提供灵活的调用接口,使调用者可以决定显示的位置(行、列)、内容和颜色。
            <br><br>子程序描述
            <br>名称: show str
            <br>功能:在指定的位置,用指定的颜色,显示一个用0结束的字符串。
            <br>参数: (dh)=行号(取值范围0\~24), (dl)=列号(取值范围0\~79), (cl)=颜色,ds:si指向字符串的首地址
            <br>返回:无
            <br>应用举例:在屏幕的8行3列,用绿色显示 data 段中的字符串。
            <br><br>提示
            <br>(1)子程序的入口参数是屏幕上的行号和列号,注意在子程序内部要将它们转化为显存中的地址,首先要分析一下屏幕上的行列位置和显存地址的对应关系:
            <br>(2) 注意保存子程序中用到的相关寄存器:
            <br>(3)这个子程序的内部处理和显存的结构密切相关,但是向外提供了与显存结构无关的接口。通过调用这个子程序,进行字符串的显示时可以不必了解显存的结构,为编程提供了方便。在实验中,注意体会这种设计思想。
            <!-- div:left-panel-50 -->
            <!-- tabs:start -->
            ##### **code**
            ```masm {22-54} [data-cc:298px]
            ;
            assume cs:text

            data segment
                db 'Welcome to masm!', 0
            data ends

            text segment
                start:  mov dh, 8
                        mov dl, 3
                        mov cl, 2
                        mov ax, data
                        mov ds, ax
                        mov si, 0
                        call show_str

                        mov ax, 4c00h
                        int 21h

                        ; 主要保存并恢复子程序内部用到的寄存器的值，这个程序后面并没有其他程序使用，
                        ; 不保存也无妨的
               show_str:push ax
                        push bx
                        push cx
                        push es

                        ; 计算屏幕起始位置并保存到 bx.
                        mov ax, 160
                        mul dh
                        mov bx, ax
                        mov ax, 2
                        mul dl
                        add bx, ax

                        ; 初始化显存段寄存器
                        mov ax, 0b800h
                        mov es, ax
                        ; 保存颜色属性到 al,因为后面的代码会使用到 cx, 导致冲突。
                        mov al, cl

                        ; 取值，判断，放置，上色，循环。
                cp:     mov cl, [si]
                        mov ch, 0
                        jcxz ok
                        mov es:[bx], cl
                        mov es:[bx].1, al
                        inc si
                        add bx, 2
                        jmp short cp
                ok:     pop es
                        pop cx
                        pop bx
                        pop ax
                        ret

            text ends
            end start
            ```
            <!-- tabs:end -->
            <!-- div:right-panel-50 -->
            <!-- tabs:start -->
            ##### **figure**
            ![](/.images/corner/assembly/book/as-ws/book-10-exam-01.png ':size=97%')
            ##### **nasm**
            ```nasm
            ```
            <!-- tabs:end -->
            <!-- panels:end -->

        - #### 2. 解决除法溢出的问题

            <!-- panels:start -->
            <!-- div:title-panel -->
            > [?] 问题：前面讲过,div 指令可以做除法。当进行8位除法的时候,用 al 存储结果的商,ah 存储结果的余数;进行16位除法的时候,用 ax 存储结果的商,dx 存储结果的余数。可是,现在有一个问题,如果结果的商大于 al或ax 所能存储的最大值,那么将如何? 比如`11000H / 1H`，11000H 在 AX 中放不下。将引发 CPU 的一个内部错误,这个错误被称为：**除法溢出**。
            <br>
            <br>子程序描述
            <br>名称:divdw
            <br>功能:进行不会产生溢出的除法运算,被除数为 dword 型,除数为 word型,结果为 dword 型。
            <br>参数:(ax)=dword 型数据的低16位
            <br><span style='padding:2.3em'>(dx)=dword 型数据的高16位</span>
            <br><span style='padding:2.3em'>(cx)=除数</span>
            <br>返回:(dx)=结果的高 16位,(ax)结果的低 16位 (cx)一余数
            <br><br>应用举例:计算1000000/10(F4240H/OAH)
            <br><span style='padding:2.3em'>mov ax,4240H
            <br><span style='padding:2.3em'>mov dx,000FH
            <br><span style='padding:2.3em'>mov cx, 0AH
            <br><span style='padding:2.3em'>call divdw
            <br>结果:(dx)=0001H, (ax)=86A0H, (cx)=0

            > [!WARNING] 编码貌似很简单，但是里面的逻辑比较复杂，书中给出了提示，使用公式即可：$X/N = int(H/N)*65536+[rem(H/N)*65536+L]/N$，附录里面也有推导过程，对我来说比较晦涩，看了之前的记录里面提到了小甲鱼大佬视频[052第十章 Call和ret指令05](https://www.bilibili.com/video/BV13J41117b9/?vd_source=550a4dc4b2a914c0681a14307bbe8cbe&p=52&t=767) 里面的一个简单示例，豁然开朗。
            <br><br>![](/.images/corner/assembly/book/as-ws/book-10-exam-02-2.png ':size=60%')
            <!-- div:left-panel-45 -->
            <!-- tabs:start -->
            ##### **code**
            ```masm {13-23}
            ;
            assume cs:text

            text segment
                start:  mov ax, 4240h
                        mov dx, 0fh
                        mov cx, 0ah
                        call divdw

                        mov ax, 4c00h
                        int 21h

                divdw:  push ax
                        mov ax, dx
                        mov dx, 0
                        div cx          ;first div

                        mov bx, ax
                        pop ax
                        div cx          ;second div
                        mov cx, dx
                        mov dx, bx
                        ret
            text ends
            end start
            ```
            <!-- tabs:end -->
            <!-- div:right-panel-55 -->
            <!-- tabs:start -->
            ##### **figure**
            <br><br>![](/.images/corner/assembly/book/as-ws/book-10-exam-02.png ':size=99%')
            ##### **nasm**
            ```nasm
            ```
            <!-- tabs:end -->
            <!-- panels:end -->

        - #### 3.数值显示

            <!-- panels:start -->
            <!-- div:title-panel -->
            > [?] 问题：编程,将 data 段中的数据以十进制的形式显示出来。
            <br><span style='padding:2.3em'>data segment</span>
            <br><span style='padding:4.3em'>dw 123,12666,1,8,3,38</span>
            <br><span style='padding:2.3em'>data ends</span>
            <br><br>这些数据在内存中都是二进制信息，标记了数值的大小。要把它们显示到屏幕上，成为我们能够读懂的信息，需要进行信息的转化。比如，数值 12666，在机器中存储为二进制信息:0011000101111010B(317AH)，计算机可以理解它。而要在显示器上读到可以理解的数值 12666，我们看到的应该是一串字符：“12666”。由于显卡遵循的是 ASCII 编码,为了让我们能在显示器上看到这串字符，它在机器中应以 ASCII 码的形式存储为：31H、32H、36H、36H、36H（字符 “0”\~“9” 对应的 ASCII 码为 30H\~39H）。
            <br><br>通过上面的分析可以看到，在概念世界中，有一个抽象的数据 12666，它表示了一个数值的大小。在现实世界中它可以有多种表示形式，可以在电子机器中以高低电平(二进制)的形式存储，也可以在纸上、黑板上、屏幕上以人类的语言“12666”来书写。现在，我们面临的问题就是，要将同一抽象的数据，从一种表示形式转化为另一种表示形式。可见，要将数据用十进制形式显示到屏幕上，要进行两步工作：
            <br><span style='padding:2.3em'>(1) 将用二进制信息存储的数据转变为十进制形式的字符串;</span>
            <br><span style='padding:2.3em'>(2) 显示十进制形式的字符串。</span>
            <br><br>第二步我们在本次实验的第一个子程序中已经实现,在这里只要调用一下 **show_str** 即可。我们来讨论第一步,因为将二进制信息转变为十进制形式的字符串也是经常要用到的功能，我们应该为它编写一个通用的子程序。
            <br><br>子程序描述
            <br><span style='padding:2.3em'>名称：dtoc</span>
            <br><span style='padding:2.3em'>功能：将 word型数据转变为表示十进制数的字符串，字符串以0为结尾符。</span>
            <br><span style='padding:2.3em'>参数：(ax)=word 型数据 ds:si指向字符串的首地址</span>
            <br><span style='padding:2.3em'>返回：无</span>
            <br><br>应用举例：编程，将数据 12666 以十进制的形式在屏幕的 8 行 3 列,用绿色显示出来。在显示时我们调用本次实验中的第一个子程序 show_str。
            <br><br>提示：下面我们对这个问题进行一下简单的分析。
            <br><span style='padding:2.3em'>(1) 要得到字符串“12666”，就是要得到一列表示该字符串的 ASCII 码：31H、32H、 36H、 36H、36Hj。十进制数码字符对应的ASCII码 = 十进制数码值+30H。要得到表示十进制数的字符串，先求十进制数每位的值。例：对于 12666，先求得每位的值：1、2、6、6、6。再将这些数分别加上 30H，便得到了表示 12666 的 ASCII 码串：31H、32H、36H、36H、36H。
            <br><span style='padding:2.3em'>(2)那么，怎样得到每位的值呢？采用下面的方法：
            <br><span style='padding:4.3em'>![](/.images/corner/assembly/book/as-ws/book-10-exam-03-02.png ':size=40%')
            <br><span style='padding:2.3em'>(4) 对(3)的质疑。在已知数据是 12666 的情况下，知道进行5次循环。可在实际问题中，数据的值是多少程序员并不知道，也就是说，程序员不能事先确定循环次数。那么,如何确定数据各位的值已经全部求出了呢?我们可以看出，只要是除到商为0，各位的值就已经全部求出。可以使用jcxz 指令来实现相关的功能。

            
            > [!CAUTION|style:flat] 需要注意**除法溢出**，新版使用 16bit 除法，老版是上面的 divdw。但是老版本偏硬编码，5位数好使，其他位就不行了，比如 1024，13等。
            <!-- div:left-panel-50 -->
            <!-- tabs:start -->
            ##### **新版**
            ```masm {24-46,49-81} [data-cc:343px]
            ;
            assume cs:text

            data segment
                db 10 dup (0)
            data ends

            text segment
                start:  mov ax, 12302
                        mov bx, data
                        mov ds, bx
                        mov si, 0
                        call dtoc

                        mov dh, 8
                        mov dl, 3
                        mov cl, 2
                        call show_str

                        mov ax, 4c00h
                        int 21h

                        ; dtoc 需要把转化好的数据放置在 ds:[si],以 0 结尾。
                dtoc:   push si
                        mov dx, 0
                        mov di, 0
                        mov bx, 10
                        ; 获取除法余数压栈
                div_:   div bx
                        push dx
                        inc di
                        mov cx, ax
                        jcxz divok
                        mov dx, 0
                        jmp short div_
                        ; 出栈并保存到数据段，供 show_str 调用
                divok:  mov cx, di
                        jcxz copyok
                        pop ax
                        add ax, 30h
                        mov [si], ax
                        dec di
                        inc si
                        jmp short divok
                copyok: pop si
                        ret

                        ; copy from exam10.1
               show_str:push ax
                        push bx
                        push cx
                        push es

                        ; 计算屏幕起始位置并保存到 bx.
                        mov ax, 160
                        mul dh
                        mov bx, ax
                        mov ax, 2
                        mul dl
                        add bx, ax

                        ; 初始化显存段寄存器
                        mov ax, 0b800h
                        mov es, ax
                        ; 保存颜色属性到 al,因为后面的代码会使用到 cx, 导致冲突。
                        mov al, cl

                        ; 取值，判断，放置，上色，循环。
                cp:     mov cl, [si]
                        mov ch, 0
                        jcxz ok
                        mov es:[bx], cl
                        mov es:[bx].1, al
                        inc si
                        add bx, 2
                        jmp short cp
                ok:     pop es
                        pop cx
                        pop bx
                        pop ax
                        ret
            text ends
            end start
            ```
            ##### **旧版**
            ```masm [data-cc:343px]
            ;
            assume cs:code

            data segment
                db 16 dup(0)
            data ends

            code segment
                start:  mov ax,12666
                        mov bx,data
                        mov ds,bx
                        mov si,0
                        call dtoc
                        mov dh,8
                        mov dl,3
                        mov cl,2
                        call show_str
                        mov ax,4c00h
                        int 21h
                dtoc:
                        mov cx,ax    ;17
                        jcxz bk
                        push ax
                        mov al,ah
                        mov ah,0
                        mov bl,10
                        div bl
                        mov cl,al
                        mov ch,ah
                        pop ax
                        mov ah,ch
                        div bl
                        mov dl,ah
                        mov dh,0
                        push dx
                        mov ah,cl
                        jmp short dtoc   ;29
                bk:     pop ax
                        add ax,30h
                        mov [si],al

                        pop ax
                        add ax,30h
                        mov [si+1],al
                        pop ax
                        add ax,30h
                        mov [si+2],al

                        pop ax
                        add ax,30h
                        mov [si+3],al    ;44

                        pop ax
                        add ax,30h
                        mov [si+4],al
                        mov byte ptr [si+5],0
                        ret

                show_str:
                        mov si,0
                        mov ax,0b800h
                        mov es,ax

                        mov al,160
                        mul dh
                        mov bx,ax
                        mov al,2
                        mul dl
                        add bx,ax
                        mov al,cl

                s:     mov cl,[si]
                        jcxz ok
                        mov dx,[si]
                        mov es:[bx],dx
                        mov es:[bx+1],al
                        inc si
                        add bx,2
                        loop s

                ok:     ret
            code ends
            end start
            ```
            <!-- tabs:end -->
            <!-- div:right-panel-50 -->
            <!-- tabs:start -->
            ##### **figure**
            <br><br>![](/.images/corner/assembly/book/as-ws/book-10-exam-03.png ':size=99%')
            ##### **nasm**
            ```nasm
            ```
            <!-- tabs:end -->
            <!-- panels:end -->

    + ### 研究试验3 使用内存空间

        - #### (2)编一个程序,用一条 C语句实现在屏幕的中间显示一个绿色的字符“a”
            <!-- panels:start -->
            <!-- div:left-panel-50 -->
            <!-- tabs:start -->
            ##### **code**
            ```c
            ;
            main()
            {
                /*  b800h
                    160 * 13 + 80 = 870h
                    a = 61, control = 2
                */
                *(int far *)0xb8000870=0x0261;
            }
            ```
            <!-- tabs:end -->
            <!-- div:right-panel-50 -->
            <!-- tabs:start -->
            ##### **figure**
            ![](/.images/corner/assembly/book/as-ws/book-experiment-03-02-01.png ':size=93%')
            ##### **nasm**
            ```nasm
            ```
            <!-- tabs:end -->
            <!-- panels:end -->

        - #### (3)分析下面程序中所有函数的汇编代码,思考相关的问题。
            <!-- panels:start -->
            <!-- div:title-panel -->
            > [?] 问题:C 语言将全局变量存放在哪里?将局部变量存放在哪里?每个函数开头的 “push bp mov bp sp”有何含义?
            <br>
            <br> 分析：首先进入函数后先`push bp`，说明后续函数会使用到这个寄存器，把原来的值保存到栈里面，返回时恢复即可。
            <br><span style='padding:3em'>然后`mov bp sp`，让 bp 和 sp 指向同一内存单元，为后续分配栈空间做准备。下面的指令执行分配后 sp 会置顶，函数内部会使用基址 bp + 偏移的方式寻址。
            <br><span style='padding:3em'>接着`sub sp,+06`，栈空间的分配从高到底，3个 int，总共分配 3*2 = 6 个内存单元。
            <br><span style='padding:3em'>对于`mov word ptr [01A6],  00A1`来说，是全局变量/静态变量，采用 ds:[01A6] 的**直接寻址**方式进行操作。
            <br><span style='padding:3em'>对于`mov word ptr [BP-06], 00B1`而言，是局部变量，采用刚才的 bp **基址 + 偏移**（-06）的方式操作。而且 -6=b1,-4=b2,-2=b3，如下图：
            <br>
            <br><span style='padding:3em'>![](/.images/corner/assembly/book/as-ws/book-experiment-03-03-02.png ':size=30%')

            > [!TIP|style:flat|label:友情提示] 在 **Turbo C 2.0**（16 位 DOS 环境）中，`int` 类型的大小是：2 字节 （16bit）
            <!-- div:left-panel-50 -->
            <!-- tabs:start -->
            ##### **question**
            ```c
            ;
            int a1, a2, a3;

            void f(void);

            main()
            {
                int b1, b2, b3;
                a1=0xa1; a2=0xa2; a3=0xa3;
                b1=0xb1; b2=0xb2; b3=0xb3;
            }

            void f(void)
            {
                int c1, c2, c3;
                a1= 0x0fa1; a2=0x0fa2; a3=0x0fa3;
                c1= 0xc1; c2=0xc2; c3=0xc3;
            }
            ```
            <!-- tabs:end -->
            <!-- div:right-panel-50 -->
            <!-- tabs:start -->
            ##### **figure**
            ![](/.images/corner/assembly/book/as-ws/book-experiment-03-03-01.png ':size=93%')
            ##### **nasm**
            ```nasm
            ```
            <!-- tabs:end -->
            <!-- panels:end -->

        - #### (4)分析下面程序的汇编代码,思考相关的问题
            <!-- panels:start -->
            <!-- div:title-panel -->
            > [?] 问题：C 语言将函数的返回值存放在哪里?
            <br><br>从汇编代码[figure01](#figure01) 中可以分析得知：
            <br><span style='padding-left:2.3em'>`1)`、先将相加的结果存放到数据段中`ab`的地址处，然后复制到 AX(原来就是这个值，好像有点多余，应该是编译器的规范？)，返回后将 AX 传送到`c`变量所在的栈地址。 
            <br><span style='padding-left:2.3em'>`2)`、如何使用的是 C代码注释中的局部变量 rst 的话，依旧是用 **AX** 寄存器返回，如图[figure02](#figure02)。
            <br><span style='padding-left:2.3em'>`3)`、`XOR SI,SI` 是一种高效清零的方式。

            > [!TIP|style:flat|label:友情提示] 添加代码`int flag = 0x1A2B3C4D;`是为了使用 debug 子命令 **s** 快速从代码段搜索需要的代码，上面介绍过这种环境下 sizeof(int)=2，但是此处给了四个字节，会导致前面的两个被丢弃。最终变成`int flag = 0x3C4D;`，从汇编代码中也可以看出来。如下[附注4-用栈传递参数](#code-6) 使用 long 型就没问题。
            <!-- div:left-panel-50 -->
            <!-- tabs:start -->
            ##### **question**
            ```c
            ;
            int f(void);

            int a, b, ab;

            main()
            {
                /*volatile*/ int flag = 0x1A2B3C4D;
                int c;
                c = f();
            }

            int f(void)
            {
                ab = a + b;     // int rst = a + b;
                return ab;      // return rst;
            }
            ```
            <!-- tabs:end -->
            <!-- div:right-panel-50 -->
            <!-- tabs:start -->
            ##### **figure01**
            ![](/.images/corner/assembly/book/as-ws/book-experiment-03-04-01.png ':size=88%')
            ##### **figure02**
            ![](/.images/corner/assembly/book/as-ws/book-experiment-03-04-02.png ':size=88%')
            <!-- tabs:end -->
            <!-- panels:end -->

    + ### 研究试验4 不用main 函数编程

        <!-- panels:start -->
        <div class="docsify-example-panel title-panel">
        
        > [?] 在本研究试验中，我们看看如何不用 main 函数，编写可以正确运行的程序。我们用一个简单的程序来进行研究。
        <br>下面,我们研究如何用 tc.exe 对 f.c 进行编译，连接，生成可正确运行的 f.exe。我们用 c:\minic 下的 tc.exe 完成以下试验。

        ```c [data-file:f.c]
        f()
        {
            *(char far *)(0xb8000000 + 160 * 10 + 80) = 'a';
            *(char far *)(0xb8000000 + 160 * 10 + 81) =  2;
        }
        ```

        - #### (1) 把程序 f.c 保存在 c:\minic 下，对其进行编译，连接。思考相关的问题。

            * ① 编译和连接哪个环节会出问题?
            * ② 显示出的错误信息是什么?
            * ③ 这个错误信息可能与哪个文件相关? 

            编译成功，但是链接失败。使用`objconv -ds NOMAIN.OBJ`命令查看符号，发现只有一个 **_f** 符号。

            ![](/.images/corner/assembly/book/as-ws/book-experiment-04-01-01.png ':size=40%')
            
        - #### (2) 用学习汇编语言时使用的 link.exe 对 tc.exe 生成的 f.obj 文件进行连接，生成 f.exe。用 Debug 加载 f.exe,察看整个程序的汇编代码。思考相关的问题。
        
            * ① f.exe 的程序代码总共有多少字节?
            * ② f.exe 的程序能正确返回吗? ❌
            * ③ f 函数的偏移地址是多少?
            
            <span style='color:blue'>f.exe 的程序的返回结果是随机的，有可能正确，但是大部分是错误的，有异常，因为程序没有正常中断返回</span>。详情查看下方[异常追踪](#yczz)。
            <br>~另外如果把 f 换成 main ，则文件大小变为了 4305B~

            ![](/.images/corner/assembly/book/as-ws/book-experiment-04-02-02.png ':size=40%') ![](/.images/corner/assembly/book/as-ws/book-experiment-04-02-01.png ':size=40%')

            <h6 id="yczz"></h6>
            <details><summary>异常追踪</summary>
            <br>
            <br>刚开始我以为卡住或者 dosbox 奔溃是因为死循环引起的，出现这种意识是由于使用了 debug nomain.exe 进行调试，
            <br>每次执行完 ‘076C:001C’ 处代码 ret 的时候就会重新从“076C:0000”开始执行。
            <br>后面查阅资料反思到，可能是由于 debug 程序本身的干扰。但是没有 debug 我又调试不了。
            <br>换种思路？要是程序内部可以直接打印出 ret 之后跳转的 IP，也就是当前栈顶的那个值，不也可以继续追踪流程，判断问题嘛，
            <br>说干就干，写代码的时候发现原来的是 c 程序，应该怎么写汇编打印啊？内嵌汇编？不，我们可以直接使用将 c 反汇编后的代码，照抄过来，形成如下：
            <p><img src="/.images/corner/assembly/book/as-ws/book-experiment-04-02-03.png" alt="" width="40%" class="medium-zoom-image"></p>
            <br>
            <br>现在我们就可以在里面编写汇编将栈顶数据放入到 AX 里面，然后通过实验10里面的数值显示程序，将 AX的值，也就是 ret 之后跳转的地址以十进制的方式打印出来。
            <br>拿到地址了，现在怎么办？是错的？是对的？没法判断跟当前卡住的状态有什么关系?
            <br>反向思考一下，原来编写的汇编要想正常退出，一般都会有`int 21`之类的中断，如果我们给一个正确的退出地址，里面包含就是正常退出的代码，又会是什么效果？
            <br>继续干，修改代码如下：
            <p><img src="/.images/corner/assembly/book/as-ws/book-experiment-04-02-04.png" alt="" width="40%" class="medium-zoom-image"></p>
            <br>
            <br>最终发现脱缰的野马被牵回家了......
            <br>所以综上所述，就是因为没有调用到正常的退出中断导致的，一个随机或者固定的栈顶值，把我们的 CPU 陷入到了非法空间中
            <br>（没有看到无效指令之类的提示，这或许和 dosbox 模拟器的实现有关，就好像除法溢出没有报错书中的提示一样。然而这些已然不在我们的学习范畴了）。
            </details>

        - #### (3) 写一个程序 m.c

            ```c [data-file:m.c]
            main()
            {
                *(char far *)(0xb8000000 + 160 * 10 + 80) = 'a';
                *(char far *)(0xb8000000 + 160 * 10 + 81) =  2;
            }
            ```
            
            用 tc.exe 对 m.c 进行编译，连接，生成 m.exe，用 Debug 察看 m.exe 整个程序的汇编代码。思考相关的问题。

            * ① m.exe 的程序代码总共有多少字节?
            * ② m.exe 能正确返回吗? ✅
            * ③ m.exe 程序中的 main 函数和 f.exe 中的 f 函数的汇编代码有何不同?

            <span style='color:blue'>使用 tc 编译链接的 main.exe 可以正常执行并且返回，但是如果使用 tc 编译，如上 link 链接的话，效果一样（卡住，碰到非法指令）</span>。

            ![](/.images/corner/assembly/book/as-ws/book-experiment-04-03-01.png ':size=40%') ![](/.images/corner/assembly/book/as-ws/book-experiment-04-03-02.png ':size=40%')

        - #### (4) 用Debug 对m.exe 进行跟踪:
        
            * ① 找到对 main 函数进行调用的指令的地址；
            * ② 找到整个程序返回的指令。注意；使用 g 命令和 p 命令。

            一、先找到 main 函数汇编代码，可以通过 **特征** 或者 **打flag** 来搜索。一般 tc 编译链接的基本从`01F*`开始。当前的为`01FA`。
            <br>然后重新启动`debug main.exe`，通过`g 1fa`执行到马上开始调用了，找被调用代码地址的方式有如下：
            <br><span style='padding-left:2.3em'>1、查看上一条 call 指令压入的栈下一条指令地址，也就是`call XXX，011D`中的 `011D`。然后向上三个字节（011A）就是调用地址了。
            <br><span style='padding-left:2.3em'>2、使用 p 命令(不要进入函数内部)继续执行到 ret，这条执行完之后就会跳到被调用方下一条指令地址。然后向上三个字节（011A）就是调用地址了。

            二、找整个程序的返回地址也有如下方式：
            <br><span style='padding-left:2.3em'>1、找到调用地址后，一直使用`u`命令翻新后续汇编代码，直到找到`mov ah,4c ... int 21`代码。
            <br><span style='padding-left:2.3em'>2、用`s cs:0 l ffff b4 4c`搜索整个代码段，`B44C = MOV AH,4C`表示要终止当前程序并返回 DOS 了，而不能使用`CD21 = INT 21`的汇编代码来搜索，因为程序中可能包含很多个系统中断。

            ![](/.images/corner/assembly/book/as-ws/book-experiment-04-04-01.png ':size=40%') ![](/.images/corner/assembly/book/as-ws/book-experiment-04-04-02.png ':size=40%')

        - #### (5) 思考如下几个问题:

            * ① 对 main 函数调用的指令和程序返回的指令是哪里来的?
            * ② 没有 main 函数时，出现的错误信息里有和“c0s”相关的信息；而前面在搭建开发环境时，没有 c0s.obj 文件 tc.exe 就无法对程序进行连接。是不是 tc.exe 把cOs.obj 和用户程序的.obj 文件一起进行连接生成.exe 文件? ✅
            * ③ 对用户程序的 main 函数进行调用的指令和程序返回的指令是否就来自 c0s.obj 文件?
            * ④ 我们如何看到 c0s.obj 文件中的程序代码呢?
            * ⑤ c0s.obj 文件里有我们设想的代码吗?

            根据原来的编译，链接 f.c 那个文件的时候报的错：未定义符号`_main`在 C0S 模块中可知，应该是在 c0s.obj 中对 main 函数进行引用的。或者通过命令`objconv -ds C0S.OBJ | grep main`查看 c0s.obj 文件中引用的符号也可猜测一二，再加上后文中出现的各种 c0s 关键字，基本可以确定来自 **c0s.obj**。

            <br>至于 c0s.obj 里面到底是怎么样的，还得想办法处理。目前可以了解到的就是它是中间文件，格式是 MS OMF。对于目前的状态，有两种处理方式：
            
            一、使用 [objconv](https://www.agner.org/optimize/#:~:text=Object%20file%20converter) 直接转换成 masm 风格汇编代码，还是比较厉害的（如下左）：
            <br><span style='padding-left:2.3em'>`objconv -fmasm c0s.obj objconv.c0s.asm`

            二、使用 Trubo debugger 1.5 环境中的小工具 tdump，目前没有找到反编译的功能，只能解析文件：
            <br><span style='padding-left:2.3em'>解析 obj 文件：`tdump -o C0S.OBJ > C0S.txt`，生成 [C0S.TXT](https://github.com/xhsgg12302/archive-assets/raw/e32cabe84e49c0c2e8f3f97d8ce57fb5a25beea9/corner/assembly/book/as-ws/C0S.TXT#:~:text=FixUp:%2011b%20%20Mode:%20Self%20Loc:%20Offset%20%20%20%20%20Frame:%20SI[1]%20%20%20Target:%20EI[1])
            <br><span style='padding-left:2.3em'>根据 FIXUPP 中对 **EI[1]** 的引用（因为 _main 是 EXTDEF 1 → EI[1]）得知需要修正的地址在代码段偏移`11b`处。
            <br><span style='padding-left:2.3em'>我们将代码段通过 dd 命令提取出来：`dd if=C0S.OBJ of=output.bin bs=1 skip=1164 count=504`，偏移和长度在 txt 文件中都有注明。
            <br><span style='padding-left:2.3em'>反汇编二进制文件：`ndisasm -b 16 -p intel output.bin | grep -C 10 '11A'`

            ![](/.images/corner/assembly/book/as-ws/book-experiment-04-05-01.png ':size=43%') ![](/.images/corner/assembly/book/as-ws/book-experiment-04-05-02.png ':size=40%')

        - #### (6) 用link.exe 对 c:\minic 目录下的 c0s.obj 进行连接,生成 c0s.exe。

            用 Debug 分别察看 c0s.exe 和 m.exe 的汇编代码。注意：从头开始察看，两个文件中的程序代码有何相同之处？

            ![](/.images/corner/assembly/book/as-ws/book-experiment-04-06-01.png ':size=40%') ![](/.images/corner/assembly/book/as-ws/book-experiment-04-06-02.png ':size=40%')

        - #### (7) 对两处的指令进行对比。
        
            用 Debug 找到 m.exe 中调用 main 函数的 call指令的偏移地址，从这个偏移地址开始向后察看 10 条指令;然后用 Debug 加载 c0s.exe，从相同的偏移地址开始向后察看 10 条指令。对两处的指令进行对比。

            ![](/.images/corner/assembly/book/as-ws/book-experiment-04-07-01.png ':size=40%') ![](/.images/corner/assembly/book/as-ws/book-experiment-04-07-02.png ':size=40%')
            
        - #### (8) 从上我们可以看出，tc.exe 把c0s.obj 和用户.obj 文件一同进行连接，生成.exe 文件。按照这个方法生成的.exe 文件中的程序的运行过程如下。

            * ① c0s.obj 里的程序先运行，进行相关的初始化，比如，申请资源、设置 DS、SS等寄存器：
            * ② c0s.obj 里的程序调用main函数，从此用户程序开始运行；
            * ③ 用户程序从main 函数返回到 c0s.obj 的程序中：
            * ④ c0s.obj 的程序接着运行，进行相关的资源释放，环境恢复等工作；
            * ⑤ c0s.obj 的程序调用DOS 的int 21h 例程的4ch号功能，程序返回。

            看来，C 程序必须从 main 函数开始,是 C语言的规定，这个规定不是在编译时保证的(tc.exe 对 f.c 的编译是可以通过的)，也不是连接的时候保证的(虽然，tc.exe 文件对 f.obj 文件不能连接成 f.exe,但 link.exe 却可以)，而是用如下的机制保证的。
            <br>首先，C 开发系统提供了用户写的应用程序正确运行所必须的初始化和程序返回等相关程序，这些程序存放在相关的 .obj 文件(比如, c0s.obj)中。
            <br>其次，需要将这些文件和 用户.obj 文件一起进行连接，才能生成可正确运行的.exe文件。
            <br>第三，连接在 用户.obj 文件前面的由 C 语言开发系统提供的 .obj 文件里的程序要对 main 函数进行调用。
            
            
            基于这种机制，我们只要改写 c0s.obj，让它调用其他函数，编程时就可以不写 main 函数了。也可以多种方式：比如

            - 一、最简单的，直接将 c0s.obj 文件中的符号`_main`，改成其他四个字母的符号，比如：`_fain`，因为过长过短都会影响整个文件的解析，形成一个没法识别的脏文件。
                <br><div style='padding-left:2.3em'>![](/.images/corner/assembly/book/as-ws/book-experiment-04-08-01.gif ':size=70%')</div>

            - 二、写一个更简于书里面的 c0s.asm，编译后生成 c0s.obj，覆盖 tc.exe 自带的那个。主要用来对比，学习。发现 link.exe 好使，tc 编译链接也没得问题。

                <div style='padding-left:2.3em'>
                
                ```masm [data-file:c0s.asm]
                assume cs:text

                text segment
                    start:  call s
                            mov ax, 4c00h
                            int 21h
                    s:
                text ends
                end start
                ```
                </div>
                
                <br><div style='padding-left:2.3em'>![](/.images/corner/assembly/book/as-ws/book-experiment-04-08-02.gif ':size=70%') ![](/.images/corner/assembly/book/as-ws/book-experiment-04-08-02.png ':size=43%')</div>

        - #### (9) 在 c:\minic 目录下，用 tc.exe 将 f.c 重新进行编译，连接，生成 f.exe。这次能通过连接吗？f.exe 可以正确运行吗？用 Debug 察看 f.exe 的汇编代码

            从（8）中第二种方式就可以看出来是完全可以编译，链接，运行的。下面给出 debug 汇编代码。

            ![](/.images/corner/assembly/book/as-ws/book-experiment-04-09-01.png ':size=40%')

        </div>
        <!-- panels:end -->
    
    + ### 研究试验5 函数如何接收不定数量的参数

        <!-- panels:start -->
        <div class="docsify-example-panel title-panel">
        
        - #### (3) 实现一个简单的printf函数，只需要支持“%c、%d”即可。

            <!-- panels:start -->
            <!-- div:left-panel-50 -->
            ```c [data-cc:300px]
            void printf(char * format, ...);

            main()
            {
                printf("%c like number: %d a%cd %d!", 'I', -3 , 'n', 12302);
            }

            void printf(char * format, ...)
            {
                int a = 0, mirror = 0, flag = 0, pos = 0, dx = 0;
                int temp;
                while((temp = *format++) != 0)
                {
                    if(temp == '%') { flag = 1; continue; }
                    if(temp == 'c' && flag) {
                        flag = 0;
                        temp = *(char *)(_BP + 6 + pos);
                        pos += 2;
                    }
                    if(temp == 'd' && flag) {
                        temp = *(int *)(_BP + 6 + pos);
                        if(temp < 0) {
                            *(char far *)(0xb8000000+160*10+80+a+ a++) = '-';
                            /* The absolute value of a negative number，like -3,3 */
                            temp = ~(temp - 1);
                        }
                        /* Mirror value，like 12302 -> 20321 */
                        while(temp != 0) {
                            temp = temp / 10;
                            /* Immediately save the value of register DX; otherwise, it will be lost. */
                            dx = _DX;
                            mirror = mirror * 10 + dx;
                        }
                        while(mirror != 0) {
                            mirror = mirror / 10;
                            dx = _DX;
                            *(char far *)(0xb8000000+160*10+80+a+ a++)= dx + 0x30;
                        }
                        mirror = 0;
                        pos += 2;
                        flag = 0;
                        continue;
                    }
                    *(char far *)(0xb8000000+160*10+80+a+ a++)=temp;
                }
            }
            ```
            <!-- div:right-panel-50 -->
            ![](/.images/corner/assembly/book/as-ws/book-experiment-05-03-01.png ':size=99%')
            <!-- panels:end -->
        </div>
        <!-- panels:end -->
        
    + ### 附注4 用栈传递参数

        <!-- panels:start -->
        <!-- div:title-panel -->
        > [?] 这种技术和高级语言编译器的工作原理密切相关。我们下面结合 C 语言的函数调用，看一下用栈传递参数的思想。
        <br>用栈传递参数的原理十分简单，就是由调用者将需要传递给子程序的参数压入栈中，子程序从栈中取得参数。我们看下面的例子。
        <!-- div:left-panel-30 -->
        <!-- tabs:start -->
        ##### **code**
        ```masm
        ;
        assume cs:text

        text segment
                    ; calc (a-b)^3, a、b 字型数据
            start:  mov ax, 1
                    push ax
                    mov ax, 3
                    push ax
                    call difcube

                    mov ax, 4c00h
                    int 21h

            difcube:push bp
                    mov bp, sp
                    mov ax, [bp+4]
                    sub ax, [bp+6]
                    mov bp, ax
                    mul bp
                    mul bp
                    pop bp
                    ret 4
        text ends
        end start
        ```
        <!-- tabs:end -->
        <!-- div:right-panel-70 -->
        <!-- tabs:start -->
        ##### **figure**
        <br><br>![](/.images/corner/assembly/book/as-ws/book-anno-4-01.png ':size=99%')
        ##### **nasm**
        ```nasm
        ```
        <!-- tabs:end -->
        <!-- panels:end -->

        <!-- panels:start -->
        <!-- div:title-panel -->
        > [?] 下面，我们通过一个 C语言程序编译后的汇编语言程序，看一下栈在参数传递中的应用。要注意的是，在C语言中，局部变量也在栈中存储。
        <br><br>从汇编代码中可以分析得知：
        <br><span style='padding-left:2.3em'>`1)`、有些局部变量放在寄存器里面了（a->di，c->si），有些放在栈里面了（b->stack）。
        <br><span style='padding-left:2.3em'>`2)`、在调用方法之前所有的参数会入栈，相当于变量复制，拷贝供函数使用，所以修改这些值并不会影响到原来的值。
        <br><span style='padding-left:2.3em'>`3)`、`XOR SI,SI` 是一种高效清零的方式。
        <!-- div:left-panel-55 -->
        <!-- tabs:start -->
        ##### **code**
        ```c
        ;
        void add(int, int, int);

        main()
        {
            long flag = 0x1A2B3D4F;
            int a = 1;
            int b = 2;
            int c = 0;
            add(a, b, c);
            c++;
        }

        void add(int a, int b, int c)
        {
            c = a + b;
        }
        ```
        <!-- tabs:end -->
        <!-- div:right-panel-44 -->
        <!-- tabs:start -->
        ##### **figure**
        ![](/.images/corner/assembly/book/as-ws/book-anno-4-02.png ':size=99%')
        ##### **nasm**
        ```nasm
        ```
        <!-- tabs:end -->
        <!-- panels:end -->