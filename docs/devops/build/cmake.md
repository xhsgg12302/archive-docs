## CMAKE

- ### 基础
    *  #### 简介
    ?> gcc </br>gcc是用来编译c文件的编译器。简单来说就是给他输入一个（或几个）c文件，它可以输出一个可执行文件。当你的程序只有一个源文件时，直接就可以用gcc命令编译它。但是当你的程序包含很多个源文件时，用gcc命令逐个去编译时，容易混乱而且工作量大。

        
    ?> make </br> 因此出现了make工具。它本身没有编译和链接功能。但是它可以按照`Makefile`文件中的命令调用gcc来编译工程。有了make，我们就不用一个一个文件去编译，只要把他们的编译指令都卸载Makefile中，然后make就可以一键完成。

     
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

    * #### 具体指令

## Reference
* https://github.com/Liuyvjin/notebook/blob/master/Cmake/Cmake%E5%9F%BA%E7%A1%80.md