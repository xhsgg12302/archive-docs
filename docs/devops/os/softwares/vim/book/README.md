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
        <br><span style='padding-left:2.8em' />反向删除`dbx` , `. == x`
        <br><span style='padding-left:2.8em' />正向删除`bdw` , `. == dw`
        <br><span style='padding-left:2.8em' />删除整的单词`daw` , `. == daw` ✅
        <br>`10).` 使用`<C-a>,[count]<C-x>`对光标下|后的数字进行加减，默认八进制，通过配置文件设置`set nrformats=`修改成十进制
        <br>`11).` 能重复就别用次数
        <br>`12).` 操作符待决策模式`g`：`g~`,`gu`,`gU`,`gUgU | gUU`,`>`,`>>`,`<`,`=`,

    + ### 第三章 插入模式

        > [?]
        <br>`13).` 插入模式即使更正错误：即使在bash shell中，也可以使用。【`<C-h>`:删除前一个字符(同退格), `<C-w>`:删除前一个单词, `<C-u>`:删除至行首】
        <br>`15).` 不离开插入模式，粘贴寄存器中的文本。将第一行的书名粘到第二行末，光标在第一个字符。`yt,jA␣<C-r>0.<Esc>`
        <br><span style='padding-left:3.5em' />![](/.images/devops/os/softwares/vim/vim-b1-015.png ':size=30%')
        <br>`16).` 随时随地做运算：在插入模式中，使用`<C-r>=`调用表达式寄存器，输入表达式`6*35<CR>`做运算，将结果插入到当前光标下。
        <br>`17).` 用字符编码插入非常用字符: `<C-v>u{四位十六进制 unicode}`：例`<C-v>u00bf`-`¿`。查看贯标下的字符使用：`ga`。
        <br>`18).` 插入以二合字母{char1}{char2}表示的字符</span>`<C-k>{char}{char}`,例如`<C-k>12`-`½`，`<C-k>?I`-`¿`，使用命令查看二合字母`:digraphs`,`:h digraph-table`。`:h digraphs-default`

    + ### 第四章 可视模式

        > [?]
        <br>`20).` 深入理解可视模式: 使用：`<C-g>`在 visual 模式和 select 模式切换，选择模式相当于普通编辑器中选择后直接输入替换那种效果。
        <br>`21).` 选择高亮选区: 【`v`:面向字符,`V`:面向行,`<C-v>`:面相列块,`gv`:重选上次高亮区域】，重复会取消选择，比如连着按两次`v`。<span>
        <br>`22).` 重复执行面向行的可视命令: `Vj>.`,`Vj2>`
        <br>`23).` 只要可能，最好用操作符命令，而不是可视命令: `vitU`可是模式中会影响部分（等长），`gUit`普通模式在的操作符刚好。
        <br><span style='padding-left:3.5em' />![](/.images/devops/os/softwares/vim/vim-b1-023.png ':size=30%')<span style='padding-left:1em' />![](/.images/devops/os/softwares/vim/vim-b1-023-02.png ':size=30%')
        <br>`24).` 用面向列块的可视模式编辑表格数据: 减少列之间的间距并以竖线分割，`<C-v>3jx...gvr|`,添加横线`yypVr-`
        <br><span style='padding-left:3.5em' />![](/.images/devops/os/softwares/vim/vim-b1-024.png ':size=25%')

    + ### 第五章 命令行模式

        > [?]
        <br>`30).` 在指定范围上执行普通模式命令：使用可视模式选择后 摁`:`冒号 输入 `normal A;` 执行每行追加`;`命令。

    + ### 第十一章 宏

        > [?]
        <br>`70).` 录制宏插入可变数值。比如在每一行前面追加 1）2）... 等。`:let i = 1, qa, I<C-r>=i<CR>)<Esc>, :let i += 1, q`。

* ## Reference
    - [Vim实用技巧_高清_中文版.pdf](./README.md)