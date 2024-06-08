* ## Intro(make)

    + ### demo

        <!-- panels:start -->
        <!-- div:left-panel-60 -->
        ```makefile
        # makefile 对空格相当挑剔，必须是制表符
        # https://stackoverflow.com/questions/24145650/makefile6-missing-separator-stop
        OBJS = main.o tool1.o tool2.o
        CC = gcc
        CFLAGS += -c -Wall -g

        mytool: $(OBJS)
            $(CC) $^ -o $@

        %.o: %.c
            $(CC) $^ $(CFLAGS) -o $@

        .PHONY: clean
        clean:
            # 禁用回显使用@，或者在外部 make -s clean
            # https://stackoverflow.com/questions/9967105/suppress-echo-of-command-invocation-in-makefile
            @$(RM) *.o mytool -r
        ```
        <!-- div:right-panel-40 -->
        ![](/.images/devops/build/make-process-01.png ':size=85%')
        <!-- panels:end -->

    + ### Spec

        - #### rule

            > [?] Makefile文件由一系列的规则(rules)组成。每条规则的形式如下： 
            <br>`<target> : <prerequisites>`
            <br>`[tab][commands]`
            <br><br>上面第一行冒号前面的部分，叫做"目标"（target），冒号后面的部分叫做"前置条件"（prerequisites）；第二行必须由一个tab键起首，后面跟着"命令"（commands）。
            <br>"目标"是必需的，不可省略；<span style='color: blue'>"前置条件"和"命令"都是可选的，但是两者之中必须至少存在一个</span>。每条规则就明确两件事：构建目标的前置条件是什么，以及如何构建。举例如下：
            <br><br>`a.txt: b.txt c.txt`
            <br>`   cat b.txt c.txt > a.txt`

        - #### target

            > [?] 一个目标（target）就构成一条规则。目标通常是文件名，指明Make命令所要构建的对象，比如上文的 a.txt 。目标可以是一个文件名，也可以是多个文件名，之间用空格分隔。
            <br>除了文件名，目标还可以是某个操作的名字，这称为`伪目标（phony target）`
            <br>`clean:`
            <br>`   rm *.o`
            <br><br>上面代码的目标是clean,它不是文件名，而是一个操作的名字，属于`伪目标`,作用是删除对象文件。
            <br><span style='color: blue'>但是，如果当前目录中，正好有一个文件叫作clean，那么这个命令不会执行。因为make发现clean文件已经存在，就认为没有必要重新构建了，就不会执行指定的rm命令了</span>。为了避免这种情况，可以明确声明clean是伪目标，写法如下。
            <br>`.PHONY: clean`
            <br>`clean:`
            <br>`   rm *.0 temp`

            > [!CAUTION] 在使用 make 命令时，如果没有指定具体的目标，make 会默认执行 Makefile 中的第一个目标（target）。这个默认目标通常是项目的“所有”或“默认”目标（通常标记为 all），它代表了构建完整项目或程序所需执行的一系列步骤。
            <br><span style='color: blue'>如果 Makefile 中没有明确的 all 目标，make 会寻找第一个非特殊目标（如 .PHONY 目标除外）作为默认执行目标</span>。特殊目标是指那些没有文件关联，仅用于定义规则的伪目标，例如 .PHONY 用来定义不需要检查文件时间戳的目标。
        
        - #### prerequisites

            > [?] 前置条件通常是一组文件名，之间用空格分隔。它指定了"目标"是否重新构建的判断标准：只要有一个前置文件不存在，或者有过更新（前置文件的last-modification时间戳比目标的时间戳新），"目标"就需要重新构建。
            <br><br>前置条件可以是现有的文件，或者通过target加 command 构建出来的文件。例：
            <br>`source.txt:`
            <br>`   echo 'this is the source' > source.txt`
            <br><br>上面代码中，source.txt后面没有前置条件，就意味着它跟其他文件都无关。没有就构建，<span style='color: blue'>有的话，不会判断是否更改过，也就不会重新构建，因为没有可判断的时间戳</span>。
            <br>所以后面即使手动修改了`source.txt`，在一下次make的时候并不会重新参与构建。
            <br>![](/.images/devops/build/make-process-02.png ':size=50%')

        - #### commands

            > [?] 命令（commands）表示如何更新目标文件，由一行或多行的Shell命令组成。它是构建"目标"的具体指令，它的运行结果通常就是生成目标文件。每行命令之前必须有一个tab键。如果想用其他键，可以用内置变量`.RECIPEPREFIX`声明。
            <br>`.RECIPEPREFIX = >`
            <br>`all:`
            <br>`> echo Hello, world`
            <br>上面代码用`.RECIPEPREFIX`指定，大于号（>）替代tab键。所以，每一行命令的起首变成了大于号，而不是tab键。
            <br><br><span style='color: blue'>需要注意的是，每行命令在一个单独的shell中执行。这些Shell之间没有继承关系</span>。
            <br>`var-lost:`
            <br>`   export foo=bar`
            <br>`   echo "foo=[$$foo]"`
            <br><br>上面代码执行后（make var-lost），取不到foo的值。因为两行命令在两个不同的进程执行。
            <br>`①` 一个解决办法是将两行命令写在一行，中间用分号分隔。
            <br>`②` 另一个解决办法是在换行符前加反斜杠转义。
            <br>`③` 最后一个方法是加上`.ONESHELL:`命令。
            <br>![](/.images/devops/build/make-process-03.png ':size=50%')

            > [!CAUTION] `.ONESHELL:` 语法在`make 3.81`之后支持。相关参考[链接1](https://stackoverflow.com/questions/32153034/oneshell-not-working-properly-in-makefile)，[链接2](https://stackoverflow.com/questions/31911699/oneshell-target-a-phony-target-has-no-effect-on-makefile)

        - #### syntax

            > [?]
            `1).` 注释：井号（#）在Makefile中表示注释。
            <br><br>`2).` 回声（echoing）: 正常情况，make会打印命令，然后执行，这就叫做回声（echoing）。但是在命令的前面加上@，就可以关闭回声。
            <br><br>`3).` 通配符: 通配符（wildcard）用来指定一组符合条件的文件名。Makefile 的通配符与 Bash 一致，主要有星号（\*）、问号（?）和 [...] 。比如: \*.o 表示所有后缀名为o的文件。
            <br><br>`4).` 模式匹配(模板): Make命令允许对文件名，进行类似正则运算的匹配，主要用到的匹配符是%。比如，假定当前目录下有 f1.c 和 f2.c 两个源码文件，需要将它们编译为对应的对象文件。`%.o: %.c`
            <br><br>`5).` 变量和赋值符: Makefile 允许使用等号自定义变量。格式`key = value`，使用`$(key)`。调用shell变量，需要使用`$`转义：`@echo $$PATH`，
            <br>有时，变量的值可能指向另一个变量。
            <br>例：`v1 = $(v2)`，变量 v1 的值是另一个变量 v2。这时会产生一个问题，v1 的值到底在定义时扩展（静态扩展），还是在运行时扩展（动态扩展）？如果 v2 的值是动态的，这两种扩展方式的结果可能会差异很大。为了解决类似问题，Makefile一共提供了四个赋值运算符 （=、:=、？=、+=），它们的区别请看[StackOverflow](https://stackoverflow.com/questions/448910/what-is-the-difference-between-the-gnu-makefile-variable-assignments-a)。
            <br>`VARIABLE = value`: 在执行时扩展，允许递归扩展。`VARIABLE := value`: 在定义时扩展。 `VARIABLE ?= value`: 只有在该变量为空时才设置值。 `VARIABLE += value`": 将值追加到变量的尾端。
            <br><br>`6).` 内置变量（Implicit Variables）
            <br>Make命令提供一系列内置变量，比如，\$(CC) 指向当前使用的编译器，\$(MAKE) 指向当前使用的Make工具。这主要是为了跨平台的兼容性，详细的内置变量清单见[手册](https://www.gnu.org/software/make/manual/html_node/Implicit-Variables.html)。
            <br><br>`7).` 自动变量（Automatic Variables）
            <br>&nbsp;&nbsp;&nbsp;`7.1). $@`: \$@指代当前目标，就是Make命令当前构建的那个目标。比如，make foo的 \$@ 就指代foo。
            <br>&nbsp;&nbsp;&nbsp;`7.2). $<`: \$< 指代第一个前置条件。比如，规则为 t: p1 p2，那么\$< 就指代p1。
            <br>&nbsp;&nbsp;&nbsp;`7.3). $?`: \$? 指代比目标更新的所有前置条件，之间以空格分隔。比如，规则为 t: p1 p2，其中 p2 的时间戳比 t 新，\$?就指代p2。
            <br>&nbsp;&nbsp;&nbsp;`7.4). $^`: \$^ 指代所有前置条件，之间以空格分隔。比如，规则为 t: p1 p2，那么 \$^ 就指代 p1 p2 。
            <br>&nbsp;&nbsp;&nbsp;`7.5). $*`: \$\* 指代匹配符 % 匹配的部分， 比如% 匹配 f1.txt 中的f1 ，\$\* 就表示 f1。
            <br>&nbsp;&nbsp;&nbsp;`7.6). $(@D) 和 $(@F)`: \$(@D) 和 \$(@F) 分别指向 \$@ 的目录名和文件名。比如，\$@是 src/input.c，那么\$(@D) 的值为 src ，\$(@F) 的值为 input.c。
            <br>&nbsp;&nbsp;&nbsp;`7.7). $(<D) 和 $(<F)`: \$(<D) 和 \$(<F) 分别指向 \$< 的目录名和文件名。
            <br>所有的自动变量清单，请看手册。下面是自动变量的一个例子。
            <br>`dest/%.txt: src/%.txt`
            <br>`   @[ -d dest ] || mkdir dest`
            <br>`   cp $< $@`
            <br>上面代码将 src 目录下的 txt 文件，拷贝到 dest 目录下。首先判断 dest 目录是否存在，如果不存在就新建，然后，\$< 指代前置文件（src/%.txt）， \$@ 指代目标文件（dest/%.txt）。
            <br><br>`8).` 判断和循环: Makefile使用 Bash 语法，完成判断和循环。
            <br>![](/.images/devops/build/make-process-04.png ':size=60%')
            <br><br>`9).` 函数: 格式`$(function arguments)` 或`${function arguments}`，Makefile提供了许多[内置函数](http://www.gnu.org/software/make/manual/html_node/Functions.html)，可供调用。下面是几个常用的内置函数。
            <br>&nbsp;&nbsp;&nbsp;`9.1). shell`: shell 函数用来执行 shell 命令，`srcfiles := $(shell echo src/{00..99}.txt)`
            <br>&nbsp;&nbsp;&nbsp;`9.2). wildcard`: wildcard 函数用来在 Makefile 中，替换 Bash 的通配符。`srcfiles := $(wildcard src/*.txt)`
            <br>&nbsp;&nbsp;&nbsp;`9.3). subst`: subst 函数用来文本替换，格式如`$(subst from,to,text)`，将字符串"feet on the street"替换成"fEEt on the strEEt": `$(subst ee,EE,feet on the street)`
            <br>&nbsp;&nbsp;&nbsp;`9.4). patsubst`: patsubst 函数用于模式匹配的替换，格式：`$(patsubst pattern,replacement,text)`。将文件名"x.c.c bar.c"，替换成"x.c.o bar.o"：`$(patsubst %.c,%.o,x.c.c bar.c)`
            <br>&nbsp;&nbsp;&nbsp;`9.5). 替换后缀名`: 替换后缀名函数的写法是：变量名 + 冒号 + 后缀名替换规则。它实际上patsubst函数的一种简写形式。将变量OUTPUT中的后缀名 .js 全部替换成 .min.js: `min: $(OUTPUT:.js=.min.js)`

* ## Reference

    + https://www.gnu.org/software/make/manual/make.html
    + https://www.ruanyifeng.com/blog/2015/02/make.html
    + https://www.bilibili.com/video/BV1hE411N7BK/
    + https://stackoverflow.com/questions/2209827/why-is-no-one-using-make-for-java