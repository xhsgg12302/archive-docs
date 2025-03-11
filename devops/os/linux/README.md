---
---

* ## Intro(Linux)

    + ### 系统调用实现

        ![](/.images/devops/os/linux/linux-system-call-01.png ':size=65%') ![](/.images/devops/os/linux/linux-system-call-02.png ':size=29%')
        > [?] 系统调用的实质是通过中断去切换到内核态，执行内核函数，访问硬件资源或者执行更高权限的操作。
        <br>内核函数和我们普通的函数形态上并没有什么区别，只是一个运行在用户态，一个运行在 **受保护空间地址** 的内核态。好像早期的操作系统允许用户执行中断调用特定的内核函数。
        <br><br>对于中断的调用，可以通过保存在 **中断向量表** 里面的 *中断号* 去调用，调用的 *系统调用号(如上右图)* 保存在 **[系统调用表](https://chromium.googlesource.com/chromiumos/docs/+/master/constants/syscalls.md)** 中，对于中断号，可以直接写，比如：`int 0x80`，系统调用号可以在触发中断前保存在 eax 寄存器里面：`movl eax, 2`，表示调用一个 `fork`系统调用。其他参数传递，也是通过寄存器这种方式。所以一个调用流程图如下：
        <br><br>![](/.images/devops/os/linux/linux-system-call-03.png ':size=70%')

        - ###### Reference
            * [详细讲解，Linux内核----系统调用的概念及原理](https://zhuanlan.zhihu.com/p/502490525)

* ## Reference

    + https://drive.google.com/file/d/1VG6rcDJKFK0EDNxqeCBnFsSahGq_BBje/view?usp=sharing