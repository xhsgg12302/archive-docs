
* ## Intro

    > [!] GCC（GNU Compiler Collection，GNU编译器套件）是一套开源的编译器集合，支持C、C++、Objective-C、Fortran、Ada、Go等多种语言，为GNU/Linux等系统和多种平台提供编译和开发工具，是开源软件开发和嵌入式开发中广泛使用的标准编译工具链。
    <br><br>主要特点：
    <br>**多语言支持**: 最初是GNU C编译器，后来扩展到C++、Objective-C、Fortran、Ada、Go等。
    <br>**跨平台性**: 可在Linux、Windows、macOS等多种操作系统上运行，并支持x86、ARM、MIPS等众多CPU架构。
    <br>**完整的工具链**: 除了编译器，还包含预处理器、汇编器、链接器、标准库和调试器等。
    <br>**自由软件**: 由自由软件基金会 (FSF)编写和发布，遵循GNU GPL/LGPL许可证。
    <br><br>工作流程(编译过程)：
    <br>1. 预处理 (Preprocessing): 展开头文件、宏定义等。
    <br>2. 编译 (Compiling): 将预处理后的代码转换成汇编代码。
    <br>3. 汇编 (Assembling): 将汇编代码转换成机器码目标文件 (.o)。
    <br>4. 链接 (Linking): 将多个目标文件和库文件合并成最终的可执行文件。 
    <br><br>与gcc和g++的关系？
    <br>GCC是整个工具集的名字，GNU Compiler Collection，而`gcc`是其下用于编译C语言的命令行工具的简称。`g++`是GCC套件中用于编译C++代码的工具。

    + ### gcc

        ```shell [data-file:version]
        vscode ➜ /workspaces/c_learning (master) $ gcc -v
        Using built-in specs.
        COLLECT_GCC=gcc
        COLLECT_LTO_WRAPPER=/usr/libexec/gcc/x86_64-linux-gnu/13/lto-wrapper
        OFFLOAD_TARGET_NAMES=nvptx-none:amdgcn-amdhsa
        OFFLOAD_TARGET_DEFAULT=1
        Target: x86_64-linux-gnu
        Configured with: ../src/configure -v --with-pkgversion='Ubuntu 13.3.0-6ubuntu2~24.04' --with-bugurl=file:///usr/share/doc/gcc-13/README.Bugs --enable-languages=c,ada,c++,go,d,fortran,objc,obj-c++,m2 --prefix=/usr --with-gcc-major-version-only --program-suffix=-13 --program-prefix=x86_64-linux-gnu- --enable-shared --enable-linker-build-id --libexecdir=/usr/libexec --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --enable-bootstrap --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --with-default-libstdcxx-abi=new --enable-libstdcxx-backtrace --enable-gnu-unique-object --disable-vtable-verify --enable-plugin --enable-default-pie --with-system-zlib --enable-libphobos-checking=release --with-target-system-zlib=auto --enable-objc-gc=auto --enable-multiarch --disable-werror --enable-cet --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-offload-targets=nvptx-none=/build/gcc-13-fG75Ri/gcc-13-13.3.0/debian/tmp-nvptx/usr,amdgcn-amdhsa=/build/gcc-13-fG75Ri/gcc-13-13.3.0/debian/tmp-gcn/usr --enable-offload-defaulted --without-cuda-driver --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu --with-build-config=bootstrap-lto-lean --enable-link-serialization=2
        Thread model: posix
        Supported LTO compression algorithms: zlib zstd
        gcc version 13.3.0 (Ubuntu 13.3.0-6ubuntu2~24.04)
        ```

        ``` [data-file:options] {46-48}
        vscode ➜ /workspaces/c_learning (master) $ gcc --help
        Usage: gcc [options] file...
        Options:
        -pass-exit-codes         Exit with highest error code from a phase.
        --help                   Display this information.
        --target-help            Display target specific command line options (including assembler and linker options).
        --help={common|optimizers|params|target|warnings|[^]{joined|separate|undocumented}}[,...].
                                Display specific types of command line options.
        (Use '-v --help' to display command line options of sub-processes).
        --version                Display compiler version information.
        -dumpspecs               Display all of the built in spec strings.
        -dumpversion             Display the version of the compiler.
        -dumpmachine             Display the compiler's target processor.
        -foffload=<targets>      Specify offloading targets.
        -print-search-dirs       Display the directories in the compiler's search path.
        -print-libgcc-file-name  Display the name of the compiler's companion library.
        -print-file-name=<lib>   Display the full path to library <lib>.
        -print-prog-name=<prog>  Display the full path to compiler component <prog>.
        -print-multiarch         Display the target's normalized GNU triplet, used as
                                a component in the library path.
        -print-multi-directory   Display the root directory for versions of libgcc.
        -print-multi-lib         Display the mapping between command line options and
                                multiple library search directories.
        -print-multi-os-directory Display the relative path to OS libraries.
        -print-sysroot           Display the target libraries directory.
        -print-sysroot-headers-suffix Display the sysroot suffix used to find headers.
        -Wa,<options>            Pass comma-separated <options> on to the assembler.
        -Wp,<options>            Pass comma-separated <options> on to the preprocessor.
        -Wl,<options>            Pass comma-separated <options> on to the linker.
        -Xassembler <arg>        Pass <arg> on to the assembler.
        -Xpreprocessor <arg>     Pass <arg> on to the preprocessor.
        -Xlinker <arg>           Pass <arg> on to the linker.
        -save-temps              Do not delete intermediate files.
        -save-temps=<arg>        Do not delete intermediate files.
        -no-canonical-prefixes   Do not canonicalize paths when building relative
                                prefixes to other gcc components.
        -pipe                    Use pipes rather than intermediate files.
        -time                    Time the execution of each subprocess.
        -specs=<file>            Override built-in specs with the contents of <file>.
        -std=<standard>          Assume that the input sources are for <standard>.
        --sysroot=<directory>    Use <directory> as the root directory for headers
                                and libraries.
        -B <directory>           Add <directory> to the compiler's search paths.
        -v                       Display the programs invoked by the compiler.
        -###                     Like -v but options quoted and commands not executed.
        -E                       Preprocess only; do not compile, assemble or link.
        -S                       Compile only; do not assemble or link.
        -c                       Compile and assemble, but do not link.
        -o <file>                Place the output into <file>.
        -pie                     Create a dynamically linked position independent
                                executable.
        -shared                  Create a shared library.
        -x <language>            Specify the language of the following input files.
                                Permissible languages include: c c++ assembler none
                                'none' means revert to the default behavior of
                                guessing the language based on the file's extension.

        Options starting with -g, -f, -m, -O, -W, or --param are automatically
        passed on to the various sub-processes invoked by gcc.  In order to pass
        other options on to these processes the -W<letter> options must be used.

        For bug reporting instructions, please see:
        <file:///usr/share/doc/gcc-13/README.Bugs>.
        ```