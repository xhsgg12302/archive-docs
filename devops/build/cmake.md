---

---

* ## Intro(CMAKE :100:)

    -  ### 简介

        > [?] gcc </br>gcc是用来编译c文件的编译器。简单来说就是给他输入一个（或几个）c文件，它可以输出一个可执行文件。当你的程序只有一个源文件时，直接就可以用gcc命令编译它。但是当你的程序包含很多个源文件时，用gcc命令逐个去编译时，容易混乱而且工作量大。

            
        > [?] make </br> 因此出现了make工具。它本身没有编译和链接功能。但是它可以按照`Makefile`文件中的命令调用gcc来编译工程。有了make，我们就不用一个一个文件去编译，只要把他们的编译指令都写在Makefile中，然后make就可以一键完成。

        
        > [?] cmake </br> 为了进一步简化Makefile的书写，出现了`cmake`。cmake根据CMakeLists.txt文件生成项目的makefile，而且写法也大大简化。总的来说，CMake，make，gcc组成一个工具链，让我们在构建项目时只需要编写源文件以及CmakeLists.txt即可。它的流程如下：

        ```mermaid
        graph LR
        A(编写CMakeLists.txt)-->B((Cmake))
        B-->M(Makefile等配置文件)
        B-->N(build.ninja 等配置文件)
        M-->DM((make))
        N-->DN((ninja))
        DM-->F((gcc/g++/clang))
        DN-->F
        E(源文件)-->F
        F-->G(可执行程序)
        ```
        
        <!-- panels:start -->
        <!-- div:left-panel-54 -->
        ![](/.images/devops/build/clion-base-step-01.png ':size=98%')
        <!-- div:left-panel-46 -->
        ![](/.images/devops/build/clion-base-step-02.png ':size=100%')
        <!-- panels:end -->
        

    - ### 基本规则

        1. #### 变量

            使用`${}`方式取值，**变量名区分大小写**（指令名不区分大小写）</br>
            定义一个变量可以用**SET**指令：</br>
            ```cmake
            SET(valname main.c src1.c src2.c)
            ```
            这条语句就是定一个一个名字叫`valname`的变量，它的值是`main.c src1.c src2.c`;相当于是`valname=main.c src1.c src2.c`
            
            - **系统预定义的变量**
            
                在我们使用 PROJECT 指令指定工程名后，CMake 会自动生成两个变量;可以在 CMakeCache.txt 中看到定义。

                * `${pname}_BINARY_DIR`: 可执行程序目录，比如 **step1_BINARY_DIR**，其实就是执行 cmake 命令时的当前目录，比如在 build/外部编译，那么它就是 build 的绝对路径：
                * `${pname}_SOURCE_DIR`: 源文件目录，比如 **step1_SOURCE_DIR**，就是主 CmakeLists.txt 所在的绝对路径。
                
                我们可以使用他们。同时系统还会帮我们预定义两个与工程名无关的变量，他们的内容与上述两个变量相同，但是好处是变量名不会因为工程名改变而改变，因此推荐使用：

                * `PROJECT_BINARY_DIR`: 可执行程序目录
                * `PROJECT_SOURCE_DIR`: 源文件目录

        2. #### 指令

            > [?] 指令就是CMake里的函数。它的名称不区分大小写，调用的格式如下:
                * 指令名（参数1，参数2，参数3） 
            参数之间用空格或者分号隔开。
            如果一个参数中包含空格，可以使用双引号将参数固定，而不是当两个对待，如下所示：

            ```cmake
            SET(val fu n.c)     # 指令会认为是'fu n.c'是两个参数，等价于 set(val "fu;n.c")
            SET(val "fu n.c")   # 使用双引号避免错误
            ```

        3. #### 使用
        
            > [?] `cmake` 命令用于生成中间文件，使用时要传入 CmakeLists.txt 文件所在的目录。
            <br>如果在主目录下直接生成配置文件，**内部构建**，这样做的坏处是使代码文件与中间文件相互混淆，因此最好的做法时**外部构件**。

            ```shell
            # 先配置
                # 外部配置
                cmake -S . -G Ninja -B build -DCMAKE_C_COMPILER=gcc -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
                # 内部配置
                mkdir build && cd build     # 用于存放中间文件
                cmake ..                    # 因为 CmakeLists.txt 在上一层目录中，或者 `cmake -S ..`
            
            # 再构建
                # 外部构建
                cmake --build build --config Debug
                # 内部构建
                cmake --build build --config Debug
                # other
                make
            ```

        4. #### 工程目录结构

            ```shell
            hello/	                # 根目录
            ├── CMakeLists.txt	    # 工程的cmakelists
            ├── CopyRight	        # 版权声明
            ├── README		        # 说明
            ├── runhello.sh		    # 调用二进制文件的脚本
            ├── bin/		        # 存放编译生成二进制文件
            ├── build/		        # 存放cmake生成的中间配置文件
            ├── doc/		        # 存放文本文档
            └── src/		        # 存放代码文档
                ├── hello.c
                └── CMakeLists.txt  # 要为任何代码目录添加Cmakelists
            ```
            > [?] 最终安装工程时，如下操作：
            ```shell
            # 把 bin/* 和 runhello.sh 装到可执行文件目录下面
            # `-t`: --target-directory=DIRECTORY  move all SOURCE arguments into DIRECTORY
            mv bin/* runhello.sh -t /usr/bin
            # 把 doc/*，README，CopyRight 装到 /usr/share/doc/hello 下面
            sudo mkdir /usr/share/doc/hello
            mv doc/* README CopyRight -t /usr/share/doc/hello
            ```

    <!-- * #### 具体指令
    > [?] 1. 最低CMake版本（每个CmakeLists.txt文件都必须有）</br>
    `CMAKE_MINIMUM_PREQUIRED(VERSION 2.6)` </br> 2. 指定工程名 </br>
    `PROJECT(PRJNAME)` <br> 3. 输出提示信息 </br>
    `MESSAGE(STATUS "This is binary dir" ${PROJECT_BINARY_DIR})` -->

    - ### 具体指令

        1. #### [cmake\_minimum\_required](https://cmake.org/cmake/help/v3.20/command/cmake_minimum_required.html)
        
            > [?]  最低 CMake 版本（每个 CmakeLists.txt 文件都必须有）`cmake_minimum_required(VERSION 2.6)`
            <br>其语法为 `cmake_minimum_required(VERSION <min>[...<max>] [FATAL_ERROR])`
            <br>比如 `cmake_minimum_required(VERSION 2.5 FATAL_ERROR)`
            <br>如果 cmake 版本小与 2.5，则出现严重错误，整个过程中止。

        2. #### [project](https://cmake.org/cmake/help/v3.20/command/project.html)
        
            > [?]  指定工程名 `project(PRJNAME)`

            ```cmake
            project(<PROJECT-NAME> [<language-name>...])
            project(<PROJECT-NAME>
                [VERSION <major>[.<minor>[.<patch>[.<tweak>]]]]
                [DESCRIPTION <project-description-string>]
                [HOMEPAGE_URL <url-string>]
                [LANGUAGES <language-name>...])
            ```

        3. #### [message](https://cmake.org/cmake/help/v3.20/command/message.html)
            
            > [?]  输出提示信息 `MESSAGE(STATUS "This is binary dir" ${PROJECT_BINARY_DIR})`
            <br>`message(STATUS "自动发现并导入大章节目录: ${DIR}")`
            
            第一个参数表示输出的信息类型，有三种选项：
            * **STATUS**: 输出前缀为--的信息。
            * **SEND_ERROR**: 产生错误，生成过程中被跳过
            * **FATAL_ERROR**: 重要错误，CMake进城终止。
        
        4. #### [add\_executable](https://cmake.org/cmake/help/v3.20/command/add_executable.html)
        
            ```cmake
            # Normal Executables
            add_executable(<name> [WIN32] [MACOSX_BUNDLE]
               [EXCLUDE_FROM_ALL]
               [source1] [source2 ...])
            
            # Imported Executables
            add_executable(<name> IMPORTED [GLOBAL])

            # Alias Executables
            add_executable(<name> ALIAS <target>)
            ```

            > [?]  添加要编译的可执行程序 `ADD_EXECUTABLE(hello main.c src1.c)`  # hello 为一个 target
            
            等效于在 makefile 里写

            ```shell
            hello: main.c src1.c
            gcc -o hello main.c src1.c
            ```

        5. #### [add\_subdirectory](https://cmake.org/cmake/help/v3.20/command/add_subdirectory.html)
        
            > [?]  指定子目录
            <br>由于子目录 src 下也有 CMakeLists.txt，是用来编译源代码的，如果不在主目录下的 CMakeLists 里面声明子目录，就不会包含这部分代码，因此主目录的文件中需要添加：`add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL])`
            <br>`add_subdirectory(${DIR})`

            - **src**: 将源文件存放的 src 目录加入工程，相对于主目录，也即`PROJECT_SOURCE_DIR`
            - **bin**: 将编译输出到 bin，若不指定则输出到 src 中。此处的 bin 是相对于编译时所在目录，也即`PROJECT_BINARY_DIR`
            - **exclude**: 编译时排除的目录
        
            注意：采用外部编译时，例如在 build 目录下面`camke ..`，那么输出目录就会以 `build/` 作为相对路径的参考，因此会在 build/ 下生成一个 bin/ 目录来存放二进制文件，若没有指定bin，则会生成 build/src 目录。

        6. #### [aux\_source\_directory](https://cmake.org/cmake/help/v3.20/command/aux_source_directory.html)

            > [?] 基本语法是：`aux_source_directory(<dir> <variable>)`
            <br><br>它的作用对象是一个目录和一个变量。它会在 CMake 配置阶段扫描指定目录下的所有源文件，把文件名列表存进你给的变量里。
            <br>*源文件* 是根据 project 中的 language-name 决定的，比如 project(name C CXX) 是不会选择 *.java 的。
            <br><br>只在配置阶段扫描一次：这是它最大的问题。如果你在目录里新增了一个 .c 文件，但没有修改任何 CMakeLists.txt，CMake 不会自动重新扫描，新文件就不会被编译进去，可能导致链接错误，而且没有任何警告。
            <br><br>不递归：只扫描指定目录本身，不会进入子目录。
            <br><br>CMake 官方文档本身就说明了这个命令主要是为了支持“显式模板实例化”这种特殊场景，并明确提醒“用它来避免手写源文件列表”虽然看似可行，但构建系统无法感知新增文件。

            ```cmake
            AUX_SOURCE_DIRECTORY(. SRC_LIST)
            ADD_EXECUTABLE(main ${SRC_LIST})

            # 如果想查看变量的值，可以通过如下方式：
            message(STATUS "SRC_LIST = ${SRC_LIST}")

            # CMakePrintHelpers
            include(CMakePrintHelpers)
            # cmake_print_variables(SRC_LIST)
            foreach(file IN LISTS SRC_LIST)
                # message(STATUS "  ${f}")
                cmake_print_variables("	" file)
            endforeach()

            # 如果希望这个变量能在 CMakeCache.txt 里看到，aux_source_directory 本身不支持直接写入缓存，所以需要用 set(... CACHE ...) 把它转成缓存变量。如下：
            set(VARIABLE_NAME "${SRC_LIST}" CACHE STRING "COMMENT" FORCE)
            ```

            你也可以通过后面提到的 FOREACH 指令来处理这个 LIST

        7. #### [target\_sources](https://cmake.org/cmake/help/v3.20/command/target_sources.html)

            > [?] 是 CMake 中用于向已存在的目标（如可执行文件或库）添加源文件的命令。它的核心优势是能精细控制源文件的“作用域”（PRIVATE/PUBLIC/INTERFACE），决定这些文件仅用于构建当前目标，还是也会传递给依赖该目标的其他目标。
            target_sources 的参数可以使用语法为`$<...>`的“生成器表达式”。
            <br><br>一个目标必须事先通过 add_executable()、add_library() 或 add_custom_target() 创建好。你可以对同一个目标多次调用 target_sources()，源文件会按调用顺序追加。

            ```cmake
            target_sources(<target>
                <INTERFACE|PUBLIC|PRIVATE> [items1...]
                [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])

            # PRIVATE：  源文件仅用于构建当前目标本身，不会传递给依赖方。
            # PUBLIC：   源文件既用于构建当前目标，也会传递给依赖方（它们会被添加到目标的 INTERFACE_SOURCES 属性中）。
            # INTERFACE：源文件不用于构建当前目标，仅传递给依赖方。

            # 基本示例

            # 假设已创建库目标 MyLib
            add_library(MyLib)

            # 私有源文件：仅用于编译 MyLib 本身
            target_sources(MyLib PRIVATE src/internal.cpp)

            # 公共源文件：编译 MyLib，同时依赖 MyLib 的目标也会用到（例如头文件中的模板实现）
            target_sources(MyLib PUBLIC include/public_header.h)
            ```
        8. #### [target\_include\_directories / include\_directories](https://cmake.org/cmake/help/v3.20/command/include_directories.html)

        10. #### [target\_link\_directories / link\_directories](https://cmake.org/cmake/help/v3.20/command/link_directories.html)

        12. #### [target\_link\_libraries / link\_libraries](https://cmake.org/cmake/help/v3.20/command/link_libraries.html)

        10. #### [find\_package](https://cmake.org/cmake/help/v3.20/command/find_package.html)

        11. #### [install](https://cmake.org/cmake/help/v3.20/command/install.html)

        7. #### 更改二进制输出目录
        
            > [?] 除了用命令指定二进制文件输出目录，还可以通过更改路径变量来指定目录。指定可执行文件或共享库输出目录的变量分别是

            - **EXECUTABLE_OUTPUT_PATH**
            - **LIBRARY_OUTPUT_PATH**
        
            使用SET命令来更改他们：

            ```cmake
            SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
            SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)
            ```

        8. #### 指定安装目录
        
            * (1) makefile 中如何写安装目录

                ```shell
                DESTDIR=                                            # 安装目录
                PREFIX=/usr                                         # 目录前缀
                install:                                            # 指定安装时要执行的代码
                    mkdir -p $(DESTDIR)/$(PREFIX)/bin               # 创建安装目录
                    install -m 755 hello $(DESTDIR)/$(PREFIX)/bin   # 将hello安装到指定目录，并修改权限。
                ```

                常见到命令行中运行 `./configure -prefix=/usr`，其实就是修改 `PREFIX`

            * (2) Cmake 中如何写安装目录

                **CMAKE_INSTALL_PREFIX**：安装目录的默认前缀，作用类似于 PREFIX，常见的用法如：`cmake -CMAKE_INSTALL_PREFIX=/usr`，只要安装目录不是以 / 开头，则会默认是以该目录为前缀。
                <br>如果未定义，则默认为`/usr/local`。在主目录的 CMakeLists.txt 中添加SET语句来设置它，也可以执行 cmake 时设置。

                **INSTALL**：指令用于定义安装规则，安装的内容包括：二进制，动态库，静态库及文件，目录，脚本等，使的格式为：

                + 安装二进制文件
                
                    ```cmake
                    # 每个参数对包括：大写的参数名如TARGETS，小写的指定参数值，省略号代表多个值
                    INSTALL(TARGETS target1 ...   #二进制目标名，也就是add_executable或add_library添加的，不用带路径
                        [ 	# target1的设置
                        [ARCHIVE|LIBRARY|RUNTIME]	# targets的类型，静态库|动态库|可执行二进制
                        [DESTINATION <dir>]		# 安装目录，默认前缀如前所述
                        [PERMISSIONS permissions...]  # 安装后权限设置
                        [CONFIGURATIONS [Debug|Release|...]]
                        [COMPONENT <component>]
                        [OPTIONAL]
                        ] [ # target2的设置...
                        ...
                        ])
                    ```
                    例子：
                    ```cmake
                    INSTALL(TARGETS myrun mylib mystatic
                        RUNTIME DESTINATION bin   # 可执行二进制 myrun 安装到 前缀/bin
                        LIBRARY DESTINATION lib   # 动态库 mylib 安装到 前缀/lib
                        ARCHIVE DESTINATION libstatic   # 静态库 mystatic 安装到 前缀/libstatic
                        )
                    ```

                + 普通文件的安装（其实就是mv+chmod）

                    ```cmake
                    INSTALL(FILES files...  DESTINATION <dir>  # 把TARGETS改为FILES同上  
                        [PERMISSIONS permissions...]  # 权限，默认为664
                        [CONFIGURATIONS [Debug|Release|...]]
                        [COMPONENT <component>]
                        [RENAME <name>] [OPTIONAL])
                    ```

                + 非目标文件的可执行程序（脚本）安装

                    ```cmake
                    INSTALL(PROGRAMS files... DESTINATION <dir>  # 改为PROGRAMS
                        [PERMISSIONS permissions...]  # 默认权限755
                        [CONFIGURATIONS [Debug|Release|...]]
                        [COMPONENT <component>]
                        [RENAME <name>] [OPTIONAL])
                    ```

                + 目录的安装

                    ```cmake
                    INSTALL(DIRECTORY dirs... # DIRECTORY 要移动的相对路径，注意dir和dir/有区别，一个是整个目录，另一个是dir/下的文件
                        DESTINATION <dir>  # DESTINATION 后面接的是要移动到的相对路径
                        [FILE_PERMISSIONS permissions...]
                        [DIRECTORY_PERMISSIONS permissions...]
                        [USE_SOURCE_PERMISSIONS]
                        [CONFIGURATIONS [Debug|Release|...]]
                        [COMPONENT <component>]
                        [   # 过滤，可以用全文匹配或正则匹配，对匹配上的文件做一些额外处理（排除|权限）
                        [PATTERN <pattern> | REGEX <regex>]
                        [EXCLUDE] [PERMISSIONS permissions...]
                        ] [...])
                    ```
                    例子：
                    ```cmake
                    INSTALL(DIRECTORY icons scripts/ # 安装icons整个目录和scripts的下属文件
                        DESTINATION share/myproj
                        PATTERN "CVS" EXCLUDE  # 排除CVS目录
                        PATTERN "scripts/*" PERMISSIONS OWNER_EXECUTE 
                        OWNER_WRITE OWNER_READ GROUP_EXECUTE GROUP_READ  
                        # 改变scripts下属文件的权限
                        )
                    ```

                + 安装时CMake脚本的执行

                    ```cmake
                    INSTALL([[SCRIPT <file>] [CODE <code>]] [...])
                    # <file> 是安装时调用的cmake脚本文件，如file.cmake
                    # <code> 是要执行cmake指令，必须用双引号，比如：
                    INSTALL(CODE " MESSAGE(\"Sample install message.\") ") # 输出提示
                    ```

    - ### 常用变量

        + #### 一、cmake变量的引用方式
        
            > [!COMMENT] 使用`${}`进行变量的引用。在 IF 等语句中，是直接使用变量名而不通过`${}`取值

        + #### 二、cmake常用变量
        
            > [!COMMENT] `CMAKE_BINARY_DIR| PROJECT_BINARY_DIR| <projectname\>_BINARY_DIR`
            <br>这三个变量指代的内容是一致的，如果是 in source 编译，指得就是工程顶层目录，如果是 out-of-source 编译，指的是工程编译发生的目录。PROJECT_BINARY_DIR 跟其他指令稍有区别，现在，你可以理解为他们是一致的。

            > [!COMMENT] `CMAKE_SOURCE_DIR|PROJECT_SOURCE_DIR|<projectname\>_SOURCE_DIR`
            <br>这三个变量指代的内容是一致的，不论采用何种编译方式，都是工程顶层目录。
            <br>也就是在 in source 编译时，他跟 CMAKE_BINARY_DIR 等变量一致。
            PROJECT_SOURCE_DIR 跟其他指令稍有区别，现在，你可以理解为他们是一致的。

            > [!COMMENT] `CMAKE_CURRENT_SOURCE_DIR` </br>
            指的是当前处理的 CMakeLists.txt 所在的路径，比如上面我们提到的 src 子目录。

            > [!COMMENT] `CMAKE_CURRRENT_BINARY_DIR` </br>
            如果是 in-source 编译，它跟 CMAKE_CURRENT_SOURCE_DIR 一致，如果是 out-of-source 编译，他指的是 target 输出目录。</br>
            使用我们上面提到的 ADD_SUBDIRECTORY(src bin)可以更改这个变量的值。</br>
            使用 SET(EXECUTABLE_OUTPUT_PATH <新路径>)并不会对这个变量造成影响，它仅仅修改了最终目标文件存放的路径。

            > [!COMMENT] `CMAKE_CURRENT_LIST_FILE` </br>
            调用这个变量的 CMakeLists.txt 的完整路径 

            > [!COMMENT] `CMAKE_CURRENT_LIST_LINE` </br>
            输出这个变量所在的行

            > [!COMMENT] `CMAKE_MODULE_PATH` </br>
            这个变量用来定义自己的 cmake 模块所在的路径。如果你的工程比较复杂，有可能会自己编写一些 cmake 模块，这些 cmake 模块是随你的工程发布的，为了让 cmake 在处理 CMakeLists.txt 时找到这些模块，你需要通过 SET 指令，将自己的 cmake 模块路径设置一下。</br>
            比如`SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)`，这时候你就可以通过 INCLUDE 指令来调用自己的模块了。

            > [!COMMENT] `EXECUTABLE_OUTPUT_PATH | LIBRARY_OUTPUT_PATH`</br>
            分别用来重新定义最终结果的存放目录，前面我们已经提到了这两个变量。

            > [!COMMENT] `PROJECT_NAME`</br>
            返回通过 PROJECT 指令定义的项目名称。

        * #### 三、cmake 调用环境变量的方式
        
            使用`$ENV{NAME}`指令就可以调用系统的环境变量了。
            <br>设置环境变量的方式是: `SET(ENV{变量名} 值)`

            1. CMAKE_INCLUDE_CURRENT_DIR
            
                自动添加 CMAKE_CURRENT_BINARY_DIR 和 CMAKE_CURRENT_SOURCE_DIR 到当前处理 </br>
                的 CMakeLists.txt。相当于在每个 CMakeLists.txt 加入:
                ```cmake
                INCLUDE_DIRECTORIES(${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_CURRENT_SOURCE_DIR})
                ```

            2. CMAKE_INCLUDE_DIRECTORIES_PROJECT_BEFORE

                将工程提供的头文件目录始终至于系统头文件目录的前面，当你定义的头文件确实跟系统发生冲突时可以提供一些帮助。

            3. CMAKE_INCLUDE_PATH 和 CMAKE_LIBRARY_PATH 
            
                我们在上一节已经提及。

        * #### 四、系统信息

            列出一些系统相关信息

            1. `CMAKE_MAJOR_VERSION`：CMAKE 主版本号，比如 2.4.6 中的 2
            2. `CMAKE_MINOR_VERSION`：CMAKE 次版本号，比如 2.4.6 中的 4
            3. `CMAKE_PATCH_VERSION`：CMAKE 补丁等级，比如 2.4.6 中的 6
            4. `CMAKE_SYSTEM`：系统名称，比如 Linux-2.6.22
            5. `CMAKE_SYSTEM_NAME`：不包含版本的系统名，比如 Linux
            6. `CMAKE_SYSTEM_VERSION`：系统版本，比如 2.6.22
            7. `CMAKE_SYSTEM_PROCESSOR`：处理器名称，比如 i686.
            8. `UNIX`：在所有的类 UNIX 平台为 TRUE，包括 OSX 和 cygwin
            9. `WIN32`：在所有的 win32 平台为 TRUE，包括 cygwin

        * #### 五、主要的开关选项

            1. `CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS`：用来控制 IF ELSE 语句的书写方式，在下一节语法部分会讲到。
            2. `BUILD_SHARED_LIBS`：这个开关用来控制默认的库编译方式，如果不进行设置，使用 ADD_LIBRARY 并没有指定库类型的情况下，默认编译生成的库都是静态库。如果 SET(BUILD_SHARED_LIBS ON) 后，默认生成的为动态库。
            3. `CMAKE_C_FLAGS`：设置 C 编译选项，也可以通过指令 ADD_DEFINITIONS() 添加。
            4. `CMAKE_CXX_FLAGS`：设置 C++编译选项，也可以通过指令 ADD_DEFINITIONS() 添加。

    - ### 常用指令

        + #### 一、基本指令

            1. ###### ADD_DEFINITIONS
                
                > [?] 向 C/C++编译器添加-D 定义，比如: `ADD_DEFINITIONS(-DENABLE_DEBUG  -DABC)`  # 参数之间用空格分割。
                <br>如果你的代码中定义了这个，则`#ifdef ENABLE_DEBUG #endif`代码块就会生效。
                <br>如果要添加其他的编译器开关，可以通过 CMAKE_C_FLAGS 变量和 CMAKE_CXX_FLAGS 变量设置。

            2. ###### ADD_DEPENDENCIES
            
                > [?] 定义 target 依赖的其他 target，确保在编译本 target 之前，其他的 target 已经被构建。
                <br>`ADD_DEPENDENCIES(target-name depend-target1 depend-target2 ...)`

            3. ###### ADD_EXECUTABLE、ADD_LIBRARY、ADD_SUBDIRECTORY
            
                > [?] 添加可执行程序，库和子目录

            4. ###### ADD_TEST、ENABLE_TESTING

                > [?] `ENABLE_TESTING` 指令用来控制 Makefile 是否构建 test 目标，涉及工程所有目录。语法很简单，没有任何参数`ENABLE_TESTING()`，一般情况这个指令放在工程的主CMakeLists.txt 中。
                <br><br>`ADD_TEST` 指令的语法是：`ADD_TEST(testname Exename arg1 arg2 ...)`
                <br>testname 是自定义的 test 名称，Exename 可以是构建的目标文件也可以是外部脚本等等。后面连接传递给可执行文件的参数。
                <br>如果没有在同一个 CMakeLists.txt 中打开 ENABLE_TESTING() 指令，任何 ADD_TEST 都是无效的。
                <br>比如我们前面的 Helloworld 例子，可以在工程主 CMakeLists.txt 中添加 `ADD_TEST(mytest ${PROJECT_BINARY_DIR}/bin/main)`，`ENABLE_TESTING()`生成 Makefile 后，就可以运行 make test 来执行测试了。

            5. ###### EXEC_PROGRAM

                > [?] 在 CMakeLists.txt 处理过程中执行命令，并不会在生成的 Makefile 中执行。具体语法为:

                ```cmake
                EXEC_PROGRAM(Executable [directory in which to run]
                                [ARGS <arguments to executable>]
                                [OUTPUT_VARIABLE <var>]
                                [RETURN_VALUE <var>])
                ```

                用于在指定的目录运行某个程序，通过 ARGS 添加参数，如果要获取输出和返回值，可通过OUTPUT_VARIABLE 和 RETURN_VALUE 分别定义两个变量。
                <br>这个指令可以帮助你在 CMakeLists.txt 处理过程中支持任何命令，比如根据系统情况去修改代码文件等等。
                <br>举个简单的例子，我们要在 src 目录执行 ls 命令，并把结果和返回值存下来。
                <br>可以直接在 src/CMakeLists.txt 中添加:

                ```cmake
                EXEC_PROGRAM(ls ARGS "*.c" OUTPUT_VARIABLE LS_OUTPUT RETURN_VALUE
                LS_RVALUE)
                IF(not LS_RVALUE)
                MESSAGE(STATUS "ls result: " ${LS_OUTPUT})
                ENDIF(not LS_RVALUE)
                ```
                在 cmake 生成 Makefile 的过程中，就会执行 ls 命令，如果返回 0，则说明成功执行，那么就输出 ls *.c 的结果。关于 IF 语句，后面的控制指令会提到。

            6. ###### FILE 指令

                > [?] 文件操作指令，基本语法为:

                ```cmake
                FILE(WRITE filename "message to write"... )
                FILE(APPEND filename "message to write"... )
                FILE(READ filename variable)
                FILE(GLOB variable [RELATIVE path] [globbing expression_r_rs]...)
                FILE(GLOB_RECURSE variable [RELATIVE path] [globbing expression_r_rs]...)
                FILE(REMOVE [directory]...)
                FILE(REMOVE_RECURSE [directory]...)
                FILE(MAKE_DIRECTORY [directory]...)
                FILE(RELATIVE_PATH variable directory file)
                FILE(TO_CMAKE_PATH path result)
                FILE(TO_NATIVE_PATH path result)
                ```
                
                这里的语法都比较简单，不在展开介绍了。

            7. ###### INCLUDE 指令
            
                > [?] 从文件或模块加载并运行 CMake 代码。

                ```cmake
                include(<file|module> [OPTIONAL] [RESULT_VARIABLE <var>]
                      [NO_POLICY_SCOPE])

                # 从指定文件加载并运行 CMake 代码。变量的读写操作访问的是调用者的作用域（动态作用域）。
                #   如果 OPTIONAL 指定了变量，则当文件不存在时不会引发错误。
                #   如果 RESULT_VARIABLE 指定了变量，则该变量 <var> 将被设置为已包含的完整文件名，如果失败则为 NOTFOUND。

                # 如果指定的是模块而不是文件，则首先在模块变量 CMAKE_MODULE_PATH 路径下查找 `<modulename>.cmake` 文件。然后查找 CMake module 目录`${CMAKE_ROOT}/Modules/`。
                # 但有一种例外情况：如果调用该文件的文件 include() 本身位于 CMake 内置模块目录中，首先搜索 CMake module 内置模块目录，然后是 CMAKE_MODULE_PATH。
                ```

            8. ###### FIND_<\>指令

                > [?] FIND_系列指令主要包含以下指令:

                ```cmake
                FIND_FILE(<VAR> name1 path1 path2 ...)
                # VAR 变量代表找到的文件全路径，包含文件名
                FIND_LIBRARY(<VAR> name1 path1 path2 ...)
                # VAR 变量表示找到的库全路径，包含库文件名
                FIND_PATH(<VAR> name1 path1 path2 ...)
                # VAR 变量代表包含这个文件的路径。
                FIND_PROGRAM(<VAR> name1 path1 path2 ...)
                # VAR 变量代表包含这个程序的全路径。
                FIND_PACKAGE(<name> [major.minor] [QUIET] [NO_MODULE]
                                [[REQUIRED|COMPONENTS] [componets...]])
                ```

                用来调用预定义在 CMAKE_MODULE_PATH 下的 Find<name\>.cmake 模块，你也可以自己定义 Find<name\>模块，通过 SET(CMAKE_MODULE_PATH dir)将其放入工程的某个目录中供工程使用，我们在后面的章节会详细介绍 FIND_PACKAGE 的使用方法和 Find 模块的编写。
                <br>FIND_LIBRARY 示例:

                ```cmake
                FIND_LIBRARY(libX X11 /usr/lib)
                IF(NOT libX)
                MESSAGE(FATAL_ERROR “libX not found”)
                ENDIF(NOT libX)
                ```

        + #### 二、控制指令:

            - ##### IF 指令
            
                (1) 基本语法为：

                ```cmake
                IF(expression_r_r)
                # THEN section.
                COMMAND1(ARGS ...)
                COMMAND2(ARGS ...)
                ...
                ELSE(expression_r_r)
                # ELSE section.
                COMMAND1(ARGS ...)
                COMMAND2(ARGS ...)
                ...
                ENDIF(expression_r_r)
                ```

                另外一个指令是 `ELSEIF`，总体把握一个原则，凡是出现 IF 的地方一定要有对应的ENDIF.出现 ELSEIF 的地方，ENDIF 是可选的。

                (2) 表达式的使用方法如下：

                ```cmake
                IF(var)  # 如果变量不是:空，0，N， NO， OFF， FALSE， NOTFOUND 或<var>_NOTFOUND 时，表达式为真。
                IF(NOT var)  # 与上述条件相反。
                IF(var1 AND var2)  # 当两个变量都为真是为真。
                IF(var1 OR var2)  # 当两个变量其中一个为真时为真。
                IF(COMMAND cmd)  # 当给定的 cmd 确实是命令并可以调用是为真。
                IF(EXISTS dir)、IF(EXISTS file)  # 当目录名或者文件名存在时为真。
                IF(file1 IS_NEWER_THAN file2)  # 当 file1 比 file2 新，或者 file1/file2 其中有一个不存在时为真，文件名请使用完整路径。
                IF(IS_DIRECTORY dirname)  # 当 dirname 是目录时，为真。
                IF(DEFINED variable)  # 如果变量被定义，为真。
                ```

                正则表达式

                ```cmake
                IF(variable MATCHES regex)、IF(string MATCHES regex)
                # 当给定的变量或者字符串能够匹配正则表达式 regex 时为真。比如:
                IF("hello" MATCHES "ell")
                MESSAGE("true")
                ENDIF("hello" MATCHES "ell")
                ```

                数字比较表达式

                ```cmake
                IF(variable LESS number)
                IF(string LESS number)
                IF(variable GREATER number)
                IF(string GREATER number)
                IF(variable EQUAL number)
                IF(string EQUAL number)
                ```

                按照字母序的排列进行比较

                ```cmake
                IF(variable STRLESS string)
                IF(string STRLESS string)
                IF(variable STRGREATER string)
                IF(string STRGREATER string)
                IF(variable STREQUAL string)
                IF(string STREQUAL string)
                ```

                一个小例子，用来判断平台差异:

                ```cmake
                IF(WIN32)
                MESSAGE(STATUS “This is windows.”)
                #作一些 Windows 相关的操作
                ELSE(WIN32)
                MESSAGE(STATUS “This is not windows”)
                #作一些非 Windows 相关的操作
                ENDIF(WIN32)

                ### 上述代码用来控制在不同的平台进行不同的控制，但是，阅读起来却并不是那么舒服，
                ## ELSE(WIN32)之类的语句很容易引起歧义。
                ## 这就用到了我们在“常用变量”一节提到的 CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS 开关:
                SET(CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS ON)
                # 这时候就可以写成:
                IF(WIN32)
                ELSE()
                ENDIF()

                ### 如果配合 ELSEIF 使用，可能的写法是这样:
                IF(WIN32)
                #do something related to WIN32
                ELSEIF(UNIX)
                #do something related to UNIX
                ELSEIF(APPLE)
                #do something related to APPLE
                ENDIF(WIN32)
                ```

            - ##### WHILE 指令

                WHILE 指令的语法是:

                ```cmake
                WHILE(condition)
                    COMMAND1(ARGS ...)
                    COMMAND2(ARGS ...)
                    ...
                ENDWHILE(condition)
                ```

                其真假判断条件可以参考 IF 指令。

            - ##### FOREACH 指令

                FOREACH 指令的使用方法有三种形式:
                
                - (1) 列表

                    ```cmake
                    FOREACH(loop_var arg1 arg2 ...)
                        COMMAND1(ARGS ...)
                        COMMAND2(ARGS ...)
                        ...
                    ENDFOREACH(loop_var)
                    ```
                    像我们前面使用的 AUX_SOURCE_DIRECTORY 的例子

                    ```cmake
                    AUX_SOURCE_DIRECTORY(. SRC_LIST)
                    FOREACH(F ${SRC_LIST})
                        MESSAGE(${F})
                    ENDFOREACH(F)
                    ```

                - (2) 范围

                    ```cmake
                    FOREACH(loop_var RANGE total)
                        ...
                    ENDFOREACH(loop_var)
                    ```

                    从 0 到 total 以 1 为步进，举例如下:

                    ```cmake
                    FOREACH(VAR RANGE 3)
                        MESSAGE(${VAR})
                    ENDFOREACH(VAR)

                    最终得到的输出是:
                    0
                    1
                    2
                    3
                    ```

                - (3) 范围和步进

                    ```cmake
                    FOREACH(loop_var RANGE start stop [step])
                        ...
                    ENDFOREACH(loop_var)
                    ```

                    从 start 开始到 stop 结束，以 step 为步进，举例如下:

                    ```cmake
                    FOREACH(A RANGE 5 15 3)
                        MESSAGE(${A})
                    ENDFOREACH(A)

                    最终得到的结果是:
                    5
                    8
                    11
                    14
                    ```

                    这个指令需要注意的是，直到遇到 ENDFOREACH 指令，整个语句块才会得到真正的执行。

* ## 样例

    <!-- tabs:start -->
    ###### **demo_gdb**
    ```cmake
    # demo_gdb
    
    cmake_minimum_required(VERSION 3.18)
    project(demo_gdb C)

    if(CMAKE_C_COMPILER_ID MATCHES "GNU|Clang")
        add_compile_options($$<$$<COMPILE_LANGUAGE:C>:-gdwarf-4>)
    endif()

    set(CMAKE_C_STANDARD 99)

    include_directories(${PROJECT_SOURCE_DIR}/include)
    file(GLOB SRC_LIST "${CMAKE_CURRENT_SOURCE_DIR}/src/*.c")

    add_executable(demo_gdb main.c ${SRC_LIST})
    ```

    ###### **practise_demo**
    ```cmake
    cmake_minimum_required(VERSION 3.10)

    project(practise_demo C)
    set(CMAKE_C_STANDARD 11)

    # subdirectory
    add_subdirectory(macosx_system_call)

    # head file
    include_directories(
        "${PROJECT_SOURCE_DIR}/external/cJson"
        "${PROJECT_SOURCE_DIR}/macosx_system_call/include"
    )

    # static _lib
    #link_directories(${PROJECT_SOURCE_DIR}/external/cJson/static)
    #link_libraries(cjson cjson_utils)

    # shared _lib
    link_directories(${PROJECT_SOURCE_DIR}/external/cJson/shared)


    add_executable(app main.c)

    target_link_libraries(app
        # install_name_tool -id ../external/cJson/shared/libcjson.dylib ../external/cJson/shared/libcjson.dylib
        # install_name_tool -id ../external/cJson/shared/libcjson_utils.dylib ../external/cJson/shared/libcjson_utils.dylib
        cjson cjson_utils
        proxy
    )
    ```
    <!-- tabs:start -->
    ###### **macosx_system_call**
    ```cmake
    cmake_minimum_required(VERSION 3.10)
    project(MSC C)

    include_directories(${PROJECT_SOURCE_DIR}/include)

    add_library(proxy SHARED src/proxy.c)

    target_link_libraries(proxy
            "-framework CoreFoundation" "-framework SystemConfiguration"
    )
    ```
    
    ###### **v4**
    ```cmake
    cmake_minimum_required(VERSION 2.8.0)

    project(CALC C)

    include_directories(${PROJECT_SOURCE_DIR}/include)
    file(GLOB SRC_LIST "${CMAKE_CURRENT_SOURCE_DIR}/src/*.c")
    set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
    add_library(calc SHARED ${SRC_LIST})
    ```

    ###### **v5**
    ```cmake
    cmake_minimum_required(VERSION 2.8.0)

    project(CALC C)
    set(CMAKE_VERBOSE_MAKEFILE ON)

    include_directories(${PROJECT_SOURCE_DIR}/include)

    # set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/_lib)
    # add_library(calc STATIC ${SRC_LIST})
    link_directories(
        ${PROJECT_SOURCE_DIR}/external/cJson/shared
        ${PROJECT_SOURCE_DIR}/darts-clone/shared
    )
    add_executable(v5 main.c)

    # install_name_tool -id libhello.dylib libhello.dylib
    # install_name_tool 需要注意路径
    # rpath
    # linux
    #   patchelf --set-soname libhello.so ../external/cJson/shared/libhello.so
    #   readelf -d ../external/cJson/shared/libhello.so
    target_link_libraries(v5 hello darts)
    ```
    <!-- tabs:end -->

    ###### **my_learning_project**
    ```cmake
    # https://github.com/12302-bak/my-learning-project/blob/gtest/CMakeLists.txt

    cmake_minimum_required(VERSION 3.20)
    project(my_learning_project C CXX)

    set(CMAKE_C_STANDARD 99)
    set(CMAKE_CXX_STANDARD 14)
    set(CMAKE_BUILD_TYPE Debug)
    # add_compile_options(-g)
    # add_compile_options("-isystem" "/usr/lib/gcc/x86_64-linux-gnu/11/include")

    find_package(GTest REQUIRED)
    enable_testing()

    # file(GLOB SRC_LIST "${CMAKE_CURRENT_SOURCE_DIR}/src/*.c")
    file(GLOB SUB_DIRS RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "[0-9]*")

    # 2. 遍历扫描到的目录
    foreach(DIR ${SUB_DIRS})
        # 确保它是一个文件夹，并且里面包含 CMakeLists.txt，防止误导入普通文件
        if(IS_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/${DIR} AND EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/${DIR}/CMakeLists.txt)
            # message(STATUS "自动发现并导入大章节目录: ${DIR}")
            add_subdirectory(${DIR})
        endif()
    endforeach()

    # 3. 指定测试目录
    add_subdirectory(tests)
    ```
    <!-- tabs:start -->
    ###### **mult_exec_template.cmake**
    ```cmake
    file(GLOB ALL_C_FILES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "*.c")

    # 2. 循环处理每一个文件
    foreach(FILE_PATH ${ALL_C_FILES})

        # NAME_WE (Name Without Extension), NAME, EXT, ABSOLUTE
        get_filename_component(TARGET_NAME ${FILE_PATH} NAME_WE)
        
        # 过滤掉不需要独立编译的文件（比如公共函数库 utils.c，如果没有则删掉这几行）
        if(NOT "${TARGET_NAME}" STREQUAL "utils") 
            # 自动注册可执行文件
            add_executable(${TARGET_NAME} ${FILE_PATH})
            
            # 如果需要链接公共的 utils.c，可以在这里统一链接
            # target_sources(${TARGET_NAME} PRIVATE utils.c)
        endif()
    endforeach()

    file(GLOB SUB_DIRS RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "*")
    foreach(DIR ${SUB_DIRS})
        if(IS_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/${DIR} AND EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/${DIR}/CMakeLists.txt)
            # message(STATUS "自动发现并导入大章节目录: ${DIR}")
            add_subdirectory(${DIR})
        endif()
    endforeach()
    ```

    ###### **sing_proj_template.cmake**
    ```cmake
    set(LIBRARY_OUTPUT_PATH ${CMAKE_CURRENT_SOURCE_DIR}/lib)

    include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)
    file(GLOB SRC_LIST "${CMAKE_CURRENT_SOURCE_DIR}/src/*.c")
    file(GLOB MAINC RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "*.c")
    get_filename_component(TARGET_NAME  ${MAINC} NAME_WE)

    # add_library(calc SHARED ${SRC_LIST})
    add_executable(${TARGET_NAME} ${MAINC} ${SRC_LIST})
    ```
    ###### **tests**
    ```cmake
    set(SING_PROJ_DIR "94_sing_proj")

    add_executable(test_calc
        test_calc.cpp
        ${CMAKE_SOURCE_DIR}/${SING_PROJ_DIR}/src/add.c
        ${CMAKE_SOURCE_DIR}/${SING_PROJ_DIR}/src/sub.c
        ${CMAKE_SOURCE_DIR}/${SING_PROJ_DIR}/src/mult.c
        ${CMAKE_SOURCE_DIR}/${SING_PROJ_DIR}/src/div.c
    )
    target_include_directories(test_calc PRIVATE ${CMAKE_SOURCE_DIR}/${SING_PROJ_DIR}/include)
    target_link_libraries(test_calc PRIVATE GTest::gtest_main)
    include(GoogleTest)
    gtest_discover_tests(test_calc)
    ```
    <!-- tabs:end -->
    <!-- tabs:end -->

* ## 补充

    ```shell
    # CLION 编译命令
    /Applications/CLion.app/Contents/bin/cmake/mac/bin/cmake \
        --debug-server-port 55847 \
        --debug-token-path /private/var/folders/g8/1p6zv3xs00x2b9h8fd6h8nr80000gn/T/cmake-debugtoken \
        -S /Users/mac/Documents/clion-projects/cmake-3.27.1-tutorial-source/Step1 \
        -B /Users/mac/Documents/clion-projects/cmake-3.27.1-tutorial-source/Step1/cmake-build-debug-cus-gdb \
        -G "CodeBlocks - Unix Makefiles" \
        -DCMAKE_BUILD_TYPE=Debug \
        -DCMAKE_MAKE_PROGRAM=/usr/bin/make \
        -DCMAKE_C_COMPILER=/usr/bin/gcc \
        -DCMAKE_CXX_COMPILER=/usr/bin/g++

    # 通用项目
    # 1. 编写 CMakeLists.txt ，基本要素 cmake_minimum_required(VERSION 3.10)， project(step1 CXX)， add_executable(tutorial tutorial.cxx)
    # 2. 统计目录创建build 文件夹，用来处理构建过程中的中间文件
    # 3. cd build 中执行， 通过-D 更新缓存文件 CMakeCache.txt 中的变量值。并在上级目录按照CMakeLists.txt中定义的构建项目。
    cmake -DCMAKE_CXX_COMPILER=/usr/bin/g++ ../
    # 4. 根据刚才的构建信息。执行下面命令执行真正的编译动作
    cmake --build .
    ```

* ## Reference

    + https://cmake.org/cmake/help/latest/
    + https://cmake.org/cmake/help/latest/guide/tutorial/index.html
    + https://cmake.org/cmake/help/latest/command/project.html#command:project
    + https://cmake.org/cmake/help/latest/manual/cmake-variables.7.html
    + https://www.gnu.org/software/make/manual/make.html
    + https://github.com/Liuyvjin/notebook/blob/master/Cmake/Cmake%E5%9F%BA%E7%A1%80.md
    + https://github.com/Liuyvjin/notebook/blob/master/Cmake/Cmake%E5%B8%B8%E7%94%A8%E5%8F%98%E9%87%8F.md
    + https://github.com/12302-bak/practise-demo  <span style='padding-left:1em'>练习DEMO(整合三方lib以及子模块依赖lib)</span>