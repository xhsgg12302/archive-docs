## CMAKE

- ### 基础
    *  #### 简介
    ?> gcc </br>gcc是用来编译c文件的编译器。简单来说就是给他输入一个（或几个）c文件，它可以输出一个可执行文件。当你的程序只有一个源文件时，直接就可以用gcc命令编译它。但是当你的程序包含很多个源文件时，用gcc命令逐个去编译时，容易混乱而且工作量大。

        
    ?> make </br> 因此出现了make工具。它本身没有编译和链接功能。但是它可以按照`Makefile`文件中的命令调用gcc来编译工程。有了make，我们就不用一个一个文件去编译，只要把他们的编译指令都写在Makefile中，然后make就可以一键完成。

     
    ?> cmake </br> 为了进一步简化Makefile的书写，出现了`cmake`。cmake根据CMakeLists.txt文件生成项目的makefile，而且写法也大大简化。总的来说，CMake，make，gcc组成一个工具链，让我们在构建项目时只需要编写源文件以及CmakeLists.txt即可。它的流程如下：
    ```mermaid
    graph LR
    A(编写CMakeLists.txt)-->B((Cmake))
    B-->C(Makefile等配置文件)
    C-->D((make))
    D-->F((gcc))
    E(源文件)-->F
    F-->G(可执行程序)
    ```

    * #### 基本规则
        1. ##### 变量
        使用`${}`方式取值，**变量名区分大小写**（指令名不区分大小写）</br>
        定义一个变量可以用**SET**指令：</br>
        ```shell
        SET(valname main.c src1.c src2.c)
        ```
        这条语句就是定一个一个名字叫`valname`的变量，它的值是`main.c src1.c src2.c`;相当于是`valname=main.c src1.c src2.c`
         
        > **系统预定义的变量**
        >
        > 在我们使用PROJECT指令指定工程名后，CMake会自动生成两个变量;
        >    * ***_BINARY_DIR***: 可执行程序目录，其实就是执行cmake命令时的当前目录，比如在build/外部编译，那么它就是build的绝对路径 </br>
        >    * ***_SOURCE_DIR***: 源文件目录，就是主CmakeLists.txt所在的绝对路径。
        > 
        > 我们可以使用他们。同时系统还会帮我们预定义两个与工程名无关的变量，他们的内容与上述两个变量相同，但是好处是变量名不会因为工程名改变而改变，因此推荐使用：</br>
        >    * ___PROJECT_BINARY_DIR___ : 可执行程序目录</br>
        >    * ***PROJECT_SOURCE_DIR*** : 源文件目录

        2. ##### 指令
        ?> 指令就是CMake里的函数。它的名称不区分大小写，调用的格式如下:
            * 指令名（参数1，参数2，参数3） 
        参数之间用空格或者分号隔开。
        如果参数中包含括号，可以使用双引号将参数分开，如下所示：
        ```shell
        SET(val fu n.c)     # 指令会认为是'fu n.c'是两个参数
        SET(val "fu n.c")   # 使用双引号避免错误
        ```

        3. ##### 使用
        ?> CMake命令用于生成中间文件，使用时要传入CmakeLists.txt文件所在的目录。如果在主目录下直接生成配置文件，**内部构建**，这样做的坏处是使代码文件与中间文件相互混淆，因此最好的做法时**外部构件**。<br>
        如下所示：在主目录下
        ```shell
        mkdir build && cd build     # 用于存放中间文件
        cmake ..                    # 因为CmakeLists.txt再上一层目录中
        make
        ```

        4. 工程目录结构
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
        ?> 最终安装工程时，如下操作：
        ```shell
        # 把bin/下文件和runhello.sh撞到可执行文件目录下面
        mv bin/* runhello.sh -t /usr/bin
        # 把doc/*，README，CopyRight装到/usr/share/doc/hello下面
        sudo mkdir /usr/share/doc/hell
        mv doc/* README CopyRight -t /usr/share/doc/hell
        ```

    <!-- * #### 具体指令
    ?> 1. 最低CMake版本（每个CmakeLists.txt文件都必须有）</br>
    `CMAKE_MINIMUM_PREQUIRED(VERSION 2.6)` </br> 2. 指定工程名 </br>
    `PROJECT(PRJNAME)` <br> 3. 输出提示信息 </br>
    `MESSAGE(STATUS "This is binary dir" ${PROJECT_BINARY_DIR})` -->

    * #### 具体指令
        1. ?> 最低CMake版本（每个CmakeLists.txt文件都必须有）
        
        `CMAKE_MINIMUM_PREQUIRED(VERSION 2.6)`
        2. ?> 指定工程名
        
        `PROJECT(PRJNAME)`
        3. ?> 输出提示信息

        `MESSAGE(STATUS "This is binary dir" ${PROJECT_BINARY_DIR})`

        第一个参数表示输出的信息类型，有三中选项：
            * **STATUS**: 输出前缀为--的信息。
            * **SEND_ERROR**: 产生错误，生成过程中被跳过
            * **FATAL_ERROR**: 重要错误，CMake进城终止。
        
        4. ?> 添加要编译的可执行程序

        `ADD_EXECUTABLE(hello main.c src1.c)`  # hello为一个target
        等效于在makefile里写
        ```shell
        hello: main.c src1.c
            gcc -o hello main.c src1.c
        ```

        5. ?> 指定子目录

        由于子目录src下也有CMakeLists.txt，是用来编译源代码的，如果不在主目录下的cmakelists里面声明子目录，就不会包含这部分代码，因此主目录的文件中需要添加：

        `ADD_SUBDIRECTORY(src bin exclude)`
            * **src**: 将源文件存放的src目录加入工程，相对于主目录，也既`PROJECT_SOURCE_DIR`
            * **bin**: 将编译输出到bin,若不指定则输出到src中。此处的bin是相对于编译时所在目录，也即`PROJECT_BINARY_DIR`
            * **exclude**: 编译时排出的目录
        
        注意：采用外部编译时，例如在build目录下面`camke ..`,那么输出目录就会以 `build/` 作为相对路径的参考，因此会在build/下生成一个bin/目录来存放二进制文件，若没有指定bin，则会生成build/src目录。

        6. ?> 更改二进制输出目录

        除了用命令指定二进制文件输出目录，还可以通过更改路径变量来指定目录。指定可执行文件或共享库输出目录的变量分别是
            * **EXECUTABLE_OUTPUT_PATH**
            * **LIBRARY_OUTPUT_PATH**
        
        使用SET命令来更改他们：
        ```shell
        SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
        SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)
        ```

        7. ?> 指定安装目录

            * (1)makefile中如何写安装目录
                ```shell
                DESTDIR=    # 安装目录
                PREFIX=/usr  # 目录前缀
                install:    # 指定安装时要执行的代码
                    mkdir -p $(DESTDIR)/$(PREFIX)/bin  # 创建安装目录
                    install -m 755 hello $(DESTDIR)/$(PREFIX)/bin  # 将hello安装到指定目录，并修改权限。
                ```
                常见到命令行中运行 ./configure -prefix=/usr，其实就是修改 `PREFIX`

            * (2)Cmake中如何写安装目录

                **CMAKE_INSTALL_PREFIX**：安装目录的默认前缀，作用类似于 PREFIX，常见的用法如：`cmake -CMAKE_INSTALL_PREFIX=/usr`，只要安装目录不是以/开头，则会默认是以该目录为前缀。如果未定义，则默认为`/usr/local`。在主目录的CMakeLists.txt中添加SET语句来设置它，也可以执行cmake时设置。

                **INSTALL**:指令用于定义安装规则，安装的内容包括：二进制，动态库，静态库及文件，目录，脚本等，使的格式为：
                + 安装二进制文件
                ```shell
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
                ```shell
                INSTALL(TARGETS myrun mylib mystatic
                    RUNTIME DESTINATION bin   # 可执行二进制 myrun 安装到 前缀/bin
                    LIBRARY DESTINATION lib   # 动态库 mylib 安装到 前缀/lib
                    ARCHIVE DESTINATION libstatic   # 静态库 mystatic 安装到 前缀/libstatic
                    )
                ```

                + 普通文件的安装（其实就是mv+chmod）
                ```shell
                INSTALL(FILES files...  DESTINATION <dir>  # 把TARGETS改为FILES同上  
                    [PERMISSIONS permissions...]  # 权限，默认为664
                    [CONFIGURATIONS [Debug|Release|...]]
                    [COMPONENT <component>]
                    [RENAME <name>] [OPTIONAL])
                ```

                + 非目标文件的可执行程序（脚本）安装
                ```shell
                INSTALL(PROGRAMS files... DESTINATION <dir>  # 改为PROGRAMS
                    [PERMISSIONS permissions...]  # 默认权限755
                    [CONFIGURATIONS [Debug|Release|...]]
                    [COMPONENT <component>]
                    [RENAME <name>] [OPTIONAL])
                ```

                + 目录的安装
                ```shell
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
                ```shell
                INSTALL(DIRECTORY icons scripts/ # 安装icons整个目录和scripts的下属文件
                    DESTINATION share/myproj
                    PATTERN "CVS" EXCLUDE  # 排除CVS目录
                    PATTERN "scripts/*" PERMISSIONS OWNER_EXECUTE 
                    OWNER_WRITE OWNER_READ GROUP_EXECUTE GROUP_READ  
                    # 改变scripts下属文件的权限
                    )
                ```

                + 安装时CMake脚本的执行
                ```shell
                INSTALL([[SCRIPT <file>] [CODE <code>]] [...])
                # <file> 是安装时调用的cmake脚本文件，如file.cmake
                # <code> 是要执行cmake指令，必须用双引号，比如：
                INSTALL(CODE " MESSAGE(\"Sample install message.\") ") # 输出提示
                ```

- ### 常用变量
    + #### 一、cmake变量的引用方式
    ?> 使用${}进行变量的引用。在 IF 等语句中,是直接使用变量名而不通过${}取值

    + #### 二、cmake常用变量
    ?> `CMAKE_BINARY_DIR| PROJECT_BINARY_DIR| <projectname\>_BINARY_DIR`</br>
    这三个变量指代的内容是一致的,如果是 in source 编译,指得就是工程顶层目录,如果是 out-of-source 编译,指的是工程编译发生的目录。PROJECT_BINARY_DIR 跟其他指令稍有区别,现在,你可以理解为他们是一致的。

    ?> `CMAKE_SOURCE_DIR|PROJECT_SOURCE_DIR|<projectname\>_SOURCE_DIR`</br>
    这三个变量指代的内容是一致的,不论采用何种编译方式,都是工程顶层目录。
    也就是在 in source 编译时,他跟 CMAKE_BINARY_DIR 等变量一致。
    PROJECT_SOURCE_DIR 跟其他指令稍有区别,现在,你可以理解为他们是一致的。

    ?> `CMAKE_CURRENT_SOURCE_DIR` </br>
    指的是当前处理的 CMakeLists.txt 所在的路径,比如上面我们提到的 src 子目录。

    ?> `CMAKE_CURRRENT_BINARY_DIR` </br>
    如果是 in-source 编译,它跟 CMAKE_CURRENT_SOURCE_DIR 一致,如果是 out-of-source 编译,他指的是 target 输出目录。</br>
    使用我们上面提到的 ADD_SUBDIRECTORY(src bin)可以更改这个变量的值。</br>
    使用 SET(EXECUTABLE_OUTPUT_PATH <新路径>)并不会对这个变量造成影响,它仅仅修改了最终目标文件存放的路径。

    ?> `CMAKE_CURRENT_LIST_FILE` </br>
    调用这个变量的 CMakeLists.txt 的完整路径 

    ?> `CMAKE_CURRENT_LIST_LINE` </br>
    输出这个变量所在的行

    ?> `CMAKE_MODULE_PATH` </br>
    这个变量用来定义自己的 cmake 模块所在的路径。如果你的工程比较复杂,有可能会自己编写一些 cmake 模块,这些 cmake 模块是随你的工程发布的,为了让 cmake 在处理CMakeLists.txt 时找到这些模块,你需要通过 SET 指令,将自己的 cmake 模块路径设置一下。</br>
    比如
    ```shell
    SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
    ```
    这时候你就可以通过 INCLUDE 指令来调用自己的模块了。

    ?> `EXECUTABLE_OUTPUT_PATH | LIBRARY_OUTPUT_PATH`</br>
    分别用来重新定义最终结果的存放目录,前面我们已经提到了这两个变量。

    ?> `PROJECT_NAME`</br>
    返回通过 PROJECT 指令定义的项目名称。

    * #### 三、cmake调用环境变量的方式
    使用`$ENV{NAME}`指令就可以调用系统的环境变量了。</br>
    设置环境变量的方式是: `SET(ENV{变量名} 值)`
        1. CMAKE_INCLUDE_CURRENT_DIR
        自动添加 CMAKE_CURRENT_BINARY_DIR 和 CMAKE_CURRENT_SOURCE_DIR 到当前处理 </br>
        的 CMakeLists.txt。相当于在每个 CMakeLists.txt 加入:
        ```shell
        INCLUDE_DIRECTORIES(${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_CURRENT_SOURCE_DIR})
        ```
        2. CMAKE_INCLUDE_DIRECTORIES_PROJECT_BEFORE
        将工程提供的头文件目录始终至于系统头文件目录的前面,当你定义的头文件确实跟系统发生冲突时可以提供一些帮助。
        3. CMAKE_INCLUDE_PATH 和 CMAKE_LIBRARY_PATH 我们在上一节已经提及。

    * #### 四、系统信息
        1. CMAKE_MAJOR_VERSION,CMAKE 主版本号,比如 2.4.6 中的 2
        2. CMAKE_MINOR_VERSION,CMAKE 次版本号,比如 2.4.6 中的 4
        3. CMAKE_PATCH_VERSION,CMAKE 补丁等级,比如 2.4.6 中的 6
        4. CMAKE_SYSTEM,系统名称,比如 Linux-2.6.22
        5. CMAKE_SYSTEM_NAME,不包含版本的系统名,比如 Linux
        6. CMAKE_SYSTEM_VERSION,系统版本,比如 2.6.22
        7. CMAKE_SYSTEM_PROCESSOR,处理器名称,比如 i686.
        8. UNIX,在所有的类 UNIX 平台为 TRUE,包括 OS X 和 cygwin
        9. WIN32,在所有的 win32 平台为 TRUE,包括 cygwin

    * #### 五、主要的开关选项
        1. CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS 用来控制 IF ELSE 语句的书写方式,在下一节语法部分会讲到。
        2. BUILD_SHARED_LIBS:这个开关用来控制默认的库编译方式,如果不进行设置,使用 ADD_LIBRARY 并没有指定库类型的情况下,默认编译生成的库都是静态库。如果 SET(BUILD_SHARED_LIBS ON)后,默认生成的为动态库。
        3. CMAKE_C_FLAGS: 设置 C 编译选项,也可以通过指令 ADD_DEFINITIONS()添加。
        4. CMAKE_CXX_FLAGS: 设置 C++编译选项,也可以通过指令 ADD_DEFINITIONS()添加。

- ### 常用指令
    + #### 一、基本指令
    ?> 1. `ADD_DEFINITIONS` </br>
    向 C/C++编译器添加-D 定义,比如:
    ```shell
    ADD_DEFINITIONS(-DENABLE_DEBUG  -DABC)  # 参数之间用空格分割。
    ```
    如果你的代码中定义了这个，则`#ifdef ENABLE_DEBUG #endif`代码块就会生效。</br>
    如果要添加其他的编译器开关,可以通过 CMAKE_C_FLAGS 变量和 CMAKE_CXX_FLAGS 变量设置。

    ?> 2. `ADD_DEPENDENCIES` </br>
    定义 target 依赖的其他 target,确保在编译本 target 之前,其他的 target 已经被构建。
    ```shell
    ADD_DEPENDENCIES(target-name depend-target1 depend-target2 ...)
    ```

    ?> 3. `ADD_EXECUTABLE、ADD_LIBRARY、ADD_SUBDIRECTORY` </br>
    添加可执行程序，库和子目录

    ?> 4. `ADD_TEST 与 ENABLE_TESTING` </br>
    `ENABLE_TESTING` 指令用来控制 Makefile 是否构建 test 目标,涉及工程所有目录。语法很简单,没有任何参数`ENABLE_TESTING()`,一般情况这个指令放在工程的主CMakeLists.txt 中。</br>
    `ADD_TEST` 指令的语法是:
    ```shell
    ADD_TEST(testname Exename arg1 arg2 ...)
    ```
    testname 是自定义的 test 名称,Exename 可以是构建的目标文件也可以是外部脚本等等。后面连接传递给可执行文件的参数。如果没有在同一个 CMakeLists.txt 中打开ENABLE_TESTING()指令,任何 ADD_TEST 都是无效的。 </br>
    比如我们前面的 Helloworld 例子,可以在工程主 CMakeLists.txt 中添加 </br>
    ADD_TEST(mytest ${PROJECT_BINARY_DIR}/bin/main) </br>
    ENABLE_TESTING()    </br>
    生成 Makefile 后,就可以运行 make test 来执行测试了。

    ?> 5. `AUX_SOURCE_DIRECTORY` </br>
    基本语法是:
    ```shell
    AUX_SOURCE_DIRECTORY(dir VARIABLE)
    ```
    作用是发现一个目录下所有的源代码文件并将列表存储在一个变量中,这个指令临时被用来自动构建源文件列表，因为目前 cmake 还不能自动发现新添加的源文件。</br>
    比如
    ```shell
    AUX_SOURCE_DIRECTORY(. SRC_LIST)
    ADD_EXECUTABLE(main ${SRC_LIST})
    ```
    你也可以通过后面提到的 FOREACH 指令来处理这个 LIST

    ?> 6. `CMAKE_MINIMUM_REQUIRED` </br>
    其语法为 CMAKE_MINIMUM_REQUIRED(VERSION versionNumber [FATAL_ERROR]) </br>
    比如 CMAKE_MINIMUM_REQUIRED(VERSION 2.5 FATAL_ERROR) </br>
    如果 cmake 版本小与 2.5,则出现严重错误,整个过程中止。

    ?> 7. `EXEC_PROGRAM` </br>
    在 CMakeLists.txt 处理过程中执行命令,并不会在生成的 Makefile 中执行。</br>
    具体语法为:
    ```shell
    EXEC_PROGRAM(Executable [directory in which to run]
                    [ARGS <arguments to executable>]
                    [OUTPUT_VARIABLE <var>]
                    [RETURN_VALUE <var>])
    ```
    用于在指定的目录运行某个程序,通过 ARGS 添加参数,如果要获取输出和返回值,可通过OUTPUT_VARIABLE 和 RETURN_VALUE 分别定义两个变量. </br>
    这个指令可以帮助你在 CMakeLists.txt 处理过程中支持任何命令,比如根据系统情况去修改代码文件等等。 </br>
    举个简单的例子,我们要在 src 目录执行 ls 命令,并把结果和返回值存下来。 </br>
    可以直接在 src/CMakeLists.txt 中添加: </br>
    ```shell
    EXEC_PROGRAM(ls ARGS "*.c" OUTPUT_VARIABLE LS_OUTPUT RETURN_VALUE
    LS_RVALUE)
    IF(not LS_RVALUE)
    MESSAGE(STATUS "ls result: " ${LS_OUTPUT})
    ENDIF(not LS_RVALUE)
    ```
    在 cmake 生成 Makefile 的过程中,就会执行 ls 命令,如果返回 0,则说明成功执行,那么就输出 ls *.c 的结果。关于 IF 语句,后面的控制指令会提到。

    ?> 8. `FILE 指令`
    文件操作指令,基本语法为:
    ```shell
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
    这里的语法都比较简单,不在展开介绍了。

    ?> 9. `INCLUDE 指令` </br>
    用来载入 CMakeLists.txt 文件,也用于载入预定义的 cmake 模块.
    ```shell
    INCLUDE(file1 [OPTIONAL])
    INCLUDE(module [OPTIONAL])
    ```
    OPTIONAL 参数的作用是文件不存在也不会产生错误。 </br>
    你可以指定载入一个文件,如果定义的是一个模块,那么将在 CMAKE_MODULE_PATH 中搜索这个模块并载入。 </br>
    载入的内容将在处理到 INCLUDE 语句时直接执行。</br>

    ?> 10. `FIND_<\>指令` </br>
    FIND_系列指令主要包含以下指令:
    ```shell
    FIND_FILE(<VAR> name1 path1 path2 ...)
    # VAR 变量代表找到的文件全路径,包含文件名
    FIND_LIBRARY(<VAR> name1 path1 path2 ...)
    # VAR 变量表示找到的库全路径,包含库文件名
    FIND_PATH(<VAR> name1 path1 path2 ...)
    # VAR 变量代表包含这个文件的路径。
    FIND_PROGRAM(<VAR> name1 path1 path2 ...)
    # VAR 变量代表包含这个程序的全路径。
    FIND_PACKAGE(<name> [major.minor] [QUIET] [NO_MODULE]
                    [[REQUIRED|COMPONENTS] [componets...]])
    ```
    用来调用预定义在 CMAKE_MODULE_PATH 下的 Find<name\>.cmake 模块,你也可以自己定义 Find<name\>模块,通过 SET(CMAKE_MODULE_PATH dir)将其放入工程的某个目录中供工程使用,我们在后面的章节会详细介绍 FIND_PACKAGE 的使用方法和 Find 模块的编写。 </br>
    FIND_LIBRARY 示例:
    ```shell
    FIND_LIBRARY(libX X11 /usr/lib)
    IF(NOT libX)
    MESSAGE(FATAL_ERROR “libX not found”)
    ENDIF(NOT libX)
    ```

    + #### 二、控制指令:
    ?> 1. `IF 指令`
        - ##### (1) 基本语法为：
            ```shell
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
            另外一个指令是 `ELSEIF`,总体把握一个原则,凡是出现 IF 的地方一定要有对应的ENDIF.出现 ELSEIF 的地方,ENDIF 是可选的。

        - ##### (2) 表达式的使用方法如下:
            ```shell
            IF(var)  # 如果变量不是:空,0,N, NO, OFF, FALSE, NOTFOUND 或<var>_NOTFOUND 时,表达式为真。
            IF(NOT var)  # 与上述条件相反。
            IF(var1 AND var2)  # 当两个变量都为真是为真。
            IF(var1 OR var2)  # 当两个变量其中一个为真时为真。
            IF(COMMAND cmd)  # 当给定的 cmd 确实是命令并可以调用是为真。
            IF(EXISTS dir)、IF(EXISTS file)  # 当目录名或者文件名存在时为真。
            IF(file1 IS_NEWER_THAN file2)  # 当 file1 比 file2 新,或者 file1/file2 其中有一个不存在时为真,文件名请使用完整路径。
            IF(IS_DIRECTORY dirname)  # 当 dirname 是目录时,为真。
            IF(DEFINED variable)  # 如果变量被定义,为真。
            ```
            正则表达式
            ```shell
            IF(variable MATCHES regex)、IF(string MATCHES regex)
            # 当给定的变量或者字符串能够匹配正则表达式 regex 时为真。比如:
            IF("hello" MATCHES "ell")
            MESSAGE("true")
            ENDIF("hello" MATCHES "ell")
            ```
            数字比较表达式
            ```shell
            IF(variable LESS number)
            IF(string LESS number)
            IF(variable GREATER number)
            IF(string GREATER number)
            IF(variable EQUAL number)
            IF(string EQUAL number)
            ```
            按照字母序的排列进行比较
            ```shell
            IF(variable STRLESS string)
            IF(string STRLESS string)
            IF(variable STRGREATER string)
            IF(string STRGREATER string)
            IF(variable STREQUAL string)
            IF(string STREQUAL string)
            ```
            一个小例子,用来判断平台差异:
            ```shell
            IF(WIN32)
            MESSAGE(STATUS “This is windows.”)
            #作一些 Windows 相关的操作
            ELSE(WIN32)
            MESSAGE(STATUS “This is not windows”)
            #作一些非 Windows 相关的操作
            ENDIF(WIN32)

            ### 上述代码用来控制在不同的平台进行不同的控制,但是,阅读起来却并不是那么舒服,
            ## ELSE(WIN32)之类的语句很容易引起歧义。
            ## 这就用到了我们在“常用变量”一节提到的 CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS 开关:
            SET(CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS ON)
            # 这时候就可以写成:
            IF(WIN32)
            ELSE()
            ENDIF()

            ### 如果配合 ELSEIF 使用,可能的写法是这样:
            IF(WIN32)
            #do something related to WIN32
            ELSEIF(UNIX)
            #do something related to UNIX
            ELSEIF(APPLE)
            #do something related to APPLE
            ENDIF(WIN32)
            ```

    ?> 2. `WHILE指令`</br>
    WHILE 指令的语法是:
    ```shell
    WHILE(condition)
        COMMAND1(ARGS ...)
        COMMAND2(ARGS ...)
        ...
    ENDWHILE(condition)
    ```
    其真假判断条件可以参考 IF 指令。

    ?> 3. `FOREACH指令`</br>
    FOREACH 指令的使用方法有三种形式:
        - ##### (1)列表
        ```shell
        FOREACH(loop_var arg1 arg2 ...)
            COMMAND1(ARGS ...)
            COMMAND2(ARGS ...)
            ...
        ENDFOREACH(loop_var)
        ```
        像我们前面使用的 AUX_SOURCE_DIRECTORY 的例子
        ```shell
        AUX_SOURCE_DIRECTORY(. SRC_LIST)
        FOREACH(F ${SRC_LIST})
            MESSAGE(${F})
        ENDFOREACH(F)
        ```
        - ##### (2)范围
        ```shell
        FOREACH(loop_var RANGE total)
            ...
        ENDFOREACH(loop_var)
        ```
        从 0 到 total 以1为步进
        举例如下:
        ```shell
        FOREACH(VAR RANGE 3)
            MESSAGE(${VAR})
        ENDFOREACH(VAR)
        ```
        最终得到的输出是:
        0
        1
        2
        3

        - ##### (3)范围和步进
        ```shell
        FOREACH(loop_var RANGE start stop [step])
            ...
        ENDFOREACH(loop_var)
        ```
        从 start 开始到 stop 结束,以 step 为步进,</br>
        举例如下:
        ```cmake
        FOREACH(A RANGE 5 15 3)
            MESSAGE(${A})
        ENDFOREACH(A)
        ```
        最终得到的结果是:
        5
        8
        11
        14
        这个指令需要注意的是,直到遇到 ENDFOREACH 指令,整个语句块才会得到真正的执行。

## 补充
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
# 1. 编写 CMakeLists.txt ,基本要素 cmake_minimum_required(VERSION 3.10), project(step1 CXX), add_executable(tutorial tutorial.cxx)
# 2. 统计目录创建build 文件夹，用来处理构建过程中的中间文件
# 3. cd build 中执行, 通过-D 更新缓存文件 CMakeCache.txt 中的变量值。并在上级目录按照CMakeLists.txt中定义的构建项目。
cmake -DCMAKE_CXX_COMPILER=/usr/bin/g++ ../
# 4. 根据刚才的构建信息。执行下面命令执行真正的编译动作
cmake --build .
```

## Reference
* https://cmake.org/cmake/help/latest/
* https://cmake.org/cmake/help/latest/command/project.html#command:project
* https://github.com/Liuyvjin/notebook/blob/master/Cmake/Cmake%E5%9F%BA%E7%A1%80.md
* https://github.com/Liuyvjin/notebook/blob/master/Cmake/Cmake%E5%B8%B8%E7%94%A8%E5%8F%98%E9%87%8F.md