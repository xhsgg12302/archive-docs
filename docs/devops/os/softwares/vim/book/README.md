* ## Intro(Vim实用技巧_高清_中文版.pdf)

    + ### 第一章 vim解决问题的方式

        > [?] 
        `1).` 重复命令：`.`
        <br>`2).` 复合命令：`(C:c$, s:cl, S:^c, I:^i, A:$a, o:A<CR>, O:ko)`
        <br>`3).` 将 + 号前后追加空格，采用删1加3的方式：使用`s`命令删除当前光标下的+，然后输入空格+空格。
        <br>`4).` 重复，回退命令，edit：{`.`,`u`}，f/t：{`;`,`,`}，F/T：{`;`,`,`}，/pattern：{`n`,`N`}，?pattern：{`n`,`N`}，:s：{`&`,`u`}，macro：{`@x`,`u`}
        <br>`5).` 使用`*`查找光标下的单词，{`n`,`N`}上下。

    + ### 第二章 普通模式

        > [?]
        <br>`7).` 停顿时请移开画笔，想象在作画，经常`<C-[>`。
        <br>`8).` 需要换行尽量使用`<Esc>o`,把修改切成最小粒度。使用`<up>,<down>,<left>,<right>`会产生新的撤销块。
        <br>`9).` 构造可重复的修改，比如删除【The end is nig<span style='color: blue;text-decoration: underline;'>h</span>】中的 nigh 。可以使用如下，也可以在[vimgolf](https://www.vimgolf.com/)中进行判决
        <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 反向删除`dbx` , `. == x`
        <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 正向删除`bdw` , `. == dw`
        <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 删除整的单词`daw` , `. == daw` ✅
        <br>`10).` 使用`<C-a>,[count]<C-x>`对光标下|后的数字进行加减，默认八进制，通过配置文件设置`set nrformats=`修改成十进制
        <br>`11).` 能重复就别用次数
        <br>`12).` 操作符待决策模式`g`：`g~`,`gu`,`gU`,`gUgU | gUU`,`>`,`>>`,`<`,`=`,

    + ### 第三章 插入模式

        > [?]
        <br>`16).` 随时随地做运算：在插入模式中，使用`<C-r>=`调用表达式寄存器，输入表达式`6*35`做运算，将结果插入到当前光标下。

    + ### 第五章 命令行模式

        > [?]
        <br>`30).` 在指定范围上执行普通模式命令：使用可视模式选择后 摁`:`冒号 输入 `normal A;` 执行每行追加`;`命令。

    + ### 第十一章 宏

        > [?]
        <br>`70).` 录制宏插入可变数值。比如在每一行前面追加 1）2）... 等。`:let i = 1, qa, I<C-r>=i<CR>)<Esc>, :let i += 1, q`。

* ## Reference
    - [Vim实用技巧_高清_中文版.pdf](./README.md)