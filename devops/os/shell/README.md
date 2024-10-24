* ## Intro(SHELL)

    + ### Shell环境

    > [?] Shell 变成跟JavaScript，php编程一样，只要有一个能编写代码的文本编辑器和一个能解释执行的脚本解释器就可以了。Linux下面的shell很多，常见的有：
    <br>查看当前使用的shell `echo $SHELL`;
    <br><br>一般约定为`#!`后面跟解释器路径
    <br>`#!/bin/sh`
    <br>`#!/bin/bash`
    <br>`#!/sbin/sh`
    
    | 名称               | 路径                       |
    | ------------------ | -------------------------- |
    | Bourne Shell       | `/usr/bin/sh 或者 /bin/sh` |
    | Bourne Again Shell | `/bin/bash`                |
    | C Shell            | `/usr/bin/csh`             |
    | K Shell            | `/usr/bin/ksh`             |
    | Shell for Root     | `/sbin/sh`                 |
    | ……                 | `……`                       |

    + ### Shell变量

        - #### 定义变量

            > [!COMMENT] 定义变量时，变量名不加美元符号（$) ，而且变量之间不能有空格。
            <br><br>变量命名遵循以下原则：
            <br><span style='padding-left: 2.0em'/> - 只能使用英文字母，数字和下划线，首字母不能数字开头
            <br><span style='padding-left: 2.0em'/> - 不能使用标点符号
            <br><span style='padding-left: 2.0em'/> - 不可使用关键字（help查看）

            **有效的shell变量名示例**
            ```shell
            RUNOOB="www.runoob.com"
            LD_LIBRARY_PATH="/bin/"
            _var="123"
            var2="abc"
            ```

            *除了显示赋值外，还可以使用语句给变量赋值*

            ```shell
            for file in `ls /etc` ;do  echo ${file}; done
            #或者
            for file in $(ls /etc); do echo $file; done
            ```

        - #### 变量类型

            > [!COMMENT] 运行shell时，会同时存在三种变量
            
            * 局部变量 ，再脚本或命令中定义，仅在当前shell实例中有效
            * 环境变量 ，所有程序，包括shell启动的程序
            * 特殊变量， 特殊变量： 有一些特殊变量在 Shell 中具有特殊含义，例如 $0 表示脚本的名称，$1, $2, 等表示脚本的参数。$#表示传递给脚本的参数数量，$? 表示上一个命令的退出状态等。

        - #### 使用变量

            > [!COMMENT] 使用一个定义过的变量，只要在变量名前面加美元符即可。变量名外的花括号是可选的，加不加都行，加是为了帮助解释器识别变量的边界。例：(*已定义的变量可以重新赋值*)

            ```shell
            name = "12302"
            echo $name
            name = "elizabeth"
            echo $name
            echo ${name}

            # 如果不给skill变量加花括号，写成echo "I am good at $skillScript"，解释器就会把$skillScript当成一个变量（其值为空），代码执行结果就不是我们期望的样子了。
            for skill in Ada Coffe Action Java; do
                echo "I am good at ${skill}Script"
            done
            ```

        - #### 只读变量

            > [!COMMENT] 使用`readonly`命令可以将变量定义为只读变量，只读变量的值不能被改变
            <br><br>修改只读变量会出现如下错误:
            <br>`bash: name: readonly variable`

            ```shell
            name=elizabeth
            readonly name
            ```

        - #### 删除变量

            > [!COMMENT] 使用`unset`命令可以删除变量，删除后变量不可再次使用，unset命令不能删除只读变量
            <br><br>(`unset -f name`)

            ```shell
            #!/bin/bash
            mySite="https://wtfo.gitee.io"
            unset mySite
            echo $mySite
            ```

    + ### Shell字符串

        > 字符床是shell编程中最常用最有用的数据类型，字符串可以用单引号，也可以用双引号，也可以不用引号。

        - #### 单引号

            > [?] 单引号里的任何字符都会原样输出，也就是单引号中的变量是无效的
            <br>单引号字符串中不能出现单独一个的单引号（转义后的也不行），但可以成对出现，作为字符串拼接使用

            ```shell
            str='this is a string'
            ```

        - #### 双引号

            > [?] 双引号里可以有变量
            <br>双引号里面可以出现转义字符

            ```shell
            name='elizabeth'
            str="Hello,I know you are \"$name\"! \n"
            echo -e $str

            #output
            Hello,I know you are "runoob"!
            ```

        - #### 字符串操作

            ```shell
            ##### 拼接字符串 ====================
            name="elizabeth"
            
            # 使用双引号拼接
            greeting="hello,"$name" !"
            greeting_1="hello,${name} !"
            echo $greeting  $greeting_1

            # 使用单引号拼接
            abc='hello,'$name' !'
            def='hello,${name} !'
            echo $abc $def

            #output
            hello,elizabeth ! hello,elizabeth !
            hello,elizabeth ! hello,${name}

            ##### 获取字符串长度 ====================
            string="abcd"
            echo ${#string}   # 输出 4
            # 变量为字符串时，${#string} 等价于 ${#string[0]}:
            echo ${#string[0]}   # 输出 4

            
            ##### 提取子字符串 ====================
            string="runoob is a great site"
            echo ${string:1:4} # 输出 unoo

            ##### 查找子字符串 ====================
            # 查找字符 i 或 o 的位置(哪个字母先出现就计算哪个)：
            string="runoob is a great site"
            echo `expr index "$string" io`  # 输出 4
            ```

    + ### Shell 中的中括号用法总结

        > [?] Shell 里面的中括号（包括单中括号与双中括号） 可用于一些条件的测试：通常可以代替 test命令。 

        * 算数比较： `[ $var -eq 0 ]`
        * 文件属性测试：`[ -e $var ]` `[ -d $var ]`
        * 字符串比较： `[[ $var = $var ]]`

        - #### 文件系统属性测试

        | 操作符 | 意义         |
        | ------ | ------------ |
        | [ -f ] | 文件         |
        | [ -d ] | 目录         |
        | [ -x ] | 可执行       |
        | [ -w ] | 可写         |
        | [ -r ] | 可读         |
        | [ -e ] | 文件或者目录 |
        | [ -c ] | 字符设备文件 |
        | [ -b ] | 块设备文件   |
        | [ -p ] | 命名管道     |
        | [ -s ] | 套接字       |
        | [ -L ] | 符号链接     |

        - #### 双括号（字符串比较）

        | 操作符                    | 意义       |
        | ------------------------- | ---------- |
        | [[ != ]] [[ = ]] [[ == ]] | 等于       |
        | [[ -z ]]                  | 空字符串   |
        | [[ -n ]]                  | 非空字符串 |

    + ### Shell参数传递

        > [?] 我们可以在执行 Shell 脚本时，向脚本传递参数，脚本内获取参数的格式为 \$n，n 代表一个数字，1 为执行脚本的第一个参数，2 为执行脚本的第二个参数。例如可以使用 \$1、\$2 等来引用传递给脚本的参数，其中 \$1 表示第一个参数，\$2 表示第二个参数，依此类推。
        <br><br>以下实例我们向脚本传递三个参数，并分别输出，其中 $0 为执行的文件名（包含文件路径）：

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```shell
        #!/bin/bash
        # author:菜鸟教程

        echo "Shell 传递参数实例！";
        echo "执行的文件名：$0";
        echo "第一个参数为：$1";
        echo "第二个参数为：$2";
        echo "第三个参数为：$3";
        ```
        <!-- div:right-panel-50 -->
        ```shell
        # 为脚本设置可执行权限，并执行脚本，输出结果如下所示：
        $ chmod +x test.sh 
        $ ./test.sh 1 2 3
        Shell 传递参数实例！
        执行的文件名：./test.sh
        第一个参数为：1
        第二个参数为：2
        第三个参数为：3
        ```
        <!-- panels:end -->

        - #### 特殊参数

            | 参数处理 | 说明 |
             -- | --
            | `$#` | 传递到脚本的参数个数
            | `$*` | 以一个单字符串显示所有向脚本传递的参数。如`"$*"`用「"」括起来的情况、以`"$1 $2 … $n"`的形式输出所有参数。
            | `$$` | 脚本运行的当前进程ID号
            | `$!` | 后台运行的最后一个进程的ID号
            | `$@` | 与`$*`相同，但是使用时加引号，并在引号中返回每个参数。如`"$@"`用「"」括起来的情况、以`"$1" "$2" … "$n"` 的形式输出所有参数。
            | `$-` | 显示Shell使用的当前选项，与set命令功能相同。
            | `$?` | 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。

            > [!CAUTION] `$*` 与 `$@` 区别：
            <br>`1).` 相同点：都是引用所有参数。
            <br>`2).` 不同点：只有在双引号中体现出来。假设在脚本运行时写了三个参数 1、2、3，则 " * " 等价于 "1 2 3"（传递了一个参数），而 "@" 等价于 "1" "2" "3"（传递了三个参数）。

            ```shell
            for i in "$*"; do echo $i done  
            # output: 1 2 3
            
            for i in "$@"; do echo $i done 
            # output: 
            # 1
            # 2
            # 3

            ```

* ## Extands

    + ### 检测是否存在某个文件的命令

        ```shell
        # test命令检测
        test -e ./shizi.jpg && echo 'found' || echo "not"
        # [] 方括号检测 *括号内前后都有空格
        [ -f ./shizi.jpg ] && echo  'found' || echo  'not'
        ```
    + ### 运行Shell 脚本的两种方法

        1. 作为可执行程序

        > 一定要写成`./test.sh`,而不是 test.sh ,运行其他二进制的程序也一样。如果直接写成 test.sh ，Linux系统会去 PATH 里寻找有没有叫 test.sh 的，而只有 /bin,/sbin,/usr/bin,/usr/sbin 等在 PATH 里面，当前目录通常不在 PATH 里，所以会找不到命令，要用`./`告诉系统在当前目录找。

        2. 作为解释器参数

        > `/bin/sh test.sh`
        <br>`/bin/bash test.sh`

    + ### Linux文件权限

        > [?] Linux 系统中每个文件的权限都有可读(`r`),可写(`w`),可执行(`x`)这三种权限，它们分别对应权限数值`4`，`2`，`1`.系统为每个文件和目录都设有默认的权限，每个文件中可分拥有者(`u`),同群组的用户(`g`)和其他用户(`o`)，所有用户用(`a`)。
        <br><br>对于每个文件，都有不同的组成信息，如图所示：
        <br>![](/.images/devops/os/shell/linux-file-attr-02.png ':size=45%') <span style='padding-left:5em'/> ![](/.images/devops/os/shell/linux-file-attr-01.png ':size=48%')
        
        **修改文件权限的命令**：
        
        `chmod [-cfvR] [--help]  [--version] mode file...`
        
        ```shell
        chmod ugo+r file.txt
        chmod a+r file txt
        chmod ug+w,o-w file.txt catalina.out
        chmod u+x catalina.sh
        chmod -R a+r *
        chmod 777 file.txt <==> chmod a+rwx file.txt
        ```
        
        **修改文件所属用户和组的命令**:(__一般只有root才可使用__)
        
        `chown [-cfhvR] [--help] [--version] user[:group] file...`
        
        ```shell
        chown wtfo:wtfo file.txt
        chown -R wtfo:wtfo *
        ```

    + ### Linux帮助命令help、man、info

        1. 实验内容

        使用帮助工具和文档

        2. 内建命令与外部命令之分

            * `内建命令`实际上是shell程序的一部分，其中包含的是一些比较简单的Linux命令，这些命令写在源码的builtins里面，由shell程序识别并在shell程序内部完成运行，通常在Linux系统加载运行时shell就被加载并驻留在系统内存中。而且解析内部命令shell不需要创建子进程，因此其执行速度比外部命令快。比如history，cd，exit等等
            * `外部命令`是Linux系统中的实用程序部分，因为实用程序的功能通常都比比较强大，所以其中包含的程序里那个也会很大，在系统加载时不会随着系统一起加载进内存中，而是在其需要的时候才调用加载。虽然其不包含在shell中，但是其执行过程是由shell程序控制的。外部命令是在bash之外额外安装的，通常放在/bin,/usr/bin,/sbin,/usr/bin等等。比如：ls,vi等等

            > 简单来说就是：一个天生自带的天赋技能，一个是后天得来的附加技能。可以使用type命令来区分内建还是外部。

            ```shell
            type exit
            #exit is a shell builtins
            type vim
            #vim is /usr/bin/vim
            ```

        3. help.man,info命令的使用及区别、

            1. **help用于内建命令，help ls 会出现错误，一般外部命令都会有 --help选项**

            2. **man命令左上角显示”LS(1),在这里，LS表示手册名称，而（1）表示该手册位于第一章节。这个章节用下表说明**

            | 章节数 | 说明                                            |
            | ------ | ----------------------------------------------- |
            | 1      | Standard commands (标注命令)                    |
            | 2      | System calls（系统调用）                        |
            | 3      | Libaray functions（库函数）                     |
            | 4      | Special devices（设备说明）                     |
            | 5      | File formats（文件格式）                        |
            | 6      | Games and toys（游戏和娱乐）                    |
            | 7      | Miscellaneout（杂项）                           |
            | 8      | Administrative Commands（管理员命令）           |
            | 9      | 其他（Linux特定的），用来存放内核例行程序的文档 |

            > 打开手册之后可以通过`pgup`,`pgdn`或者上下键来翻看，`q`表示退出

            3. **info命令来自自由软件基金会的GUN项目，是一个超文本帮助系统，可以显示很多信息。info和man像是两个集合，有交集，但是info显示的更多**


* ## Reference

    + https://www.runoob.com/linux/linux-shell.html
