* ## Intro(VIM)

    - ### __colon__wq 和 __colon__x 的区别

        > [?] 作用和`:wq`相同，但是`:x`<span style='color: blue'>只有文件内容发生改变的时候才会发生写入操作</span>。[参考](https://stackoverflow.com/questions/13844098/difference-between-wq-and-x-in-vi)
        <br>而`:wq`不管有没有改变，都会写入，所以即使没有修改文件内容，只要执行`:wq`，就会重新写入，文件属性发生改变。可以通过命令验证，比如执行命令`stat -f%Sm file`查看文件的修改时间。

    - ### map映射中<Leader>修饰

        > [?] `<Leader>`是一个特殊的键，它是 Vim 中用户定义的前缀键。你可以通过设置 **mapleader** 变量来自定义`<Leader>`键。例如：`let mapleader = ","`
        <br>默认情况下，`<Leader>`是反斜杠`\`。
        <br><br>作用：当你在定义快捷键时使用leader修饰符，表示这个快捷键是基于你定义的前缀键的。这样可以避免与默认快捷键发生冲突，并使你的快捷键更具可读性和组织性。
        <br>其他定义关键字可以参考[这个](https://github.com/lymslive/vimllearn/blob/master/z/20170818_2.md)

* ## Reference

    - https://github.com/oldratlee/vim-practice