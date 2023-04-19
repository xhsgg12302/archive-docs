## 简介
```shell
grep(global regular expression print):
grep 主要用来查找，过滤，grep家族总共有三个：grep，egrep，fgrep。egrep = grep -E 扩展的正则表达式。

1、基本的正则表达式（Basic Regular Expression 又叫 Basic RegEx  简称 BREs）
2、扩展的正则表达式（Extended Regular Expression 又叫 Extended RegEx 简称 EREs）
3、Perl 的正则表达式（Perl Regular Expression 又叫 Perl RegEx 简称 PREs）

***********************************************************
    grep 默认 BREs, -E EREs, -P PREs
    egrep 默认 EREs, -P PREs
    sed 默认 BREs,-r EREs
    awk 默认 EREs
***********************************************************
```
## 常用选项

```shell
# -E : 开启扩展的正则表达式
# -i : 忽略大小写（ignore case）
# -v : 反向选择（invert）
# -n : 显示行号
# -o : 只显示匹配到的内容
# -w : 被匹配的文本只能是单词，不是某一部分，`like` 不会匹配liker
# -c : 显示匹配到多少行，与-cv使用表示反向匹配行数
# --color : 将匹配到的内容以颜色高亮显示
# -A n : 显示字符所在行及其后n行，after
# -B n : 显示字符所在行及其前n行，before
# -C n : 显示字符前后n行，context

grep "root" /etc/passwd
grep -n "root" /etc/passwd
grep -cv "root" /etc/passwd
grep -o "root" /etc/passwd
grep -A 2 "core id" /proc/cpuinfo
```

## 模式部分
    1. 直接输入要匹配的字符串，可以使用fgrep(fast grep)代替来提高查找速度
    2. 使用基本正则表达式
        * 匹配字符
            . :任意字符
            [abc] : 表示匹配一个字符，这个字符必须是abc中的一个。
            [^123] : 匹配一个除了123以外的字符
            [a-zA-Z] : 等价于[[:alpha:]]
            [0-9] : 等价于[[:digit:]]
            [a-zA-Z0-9] : 等价于 [[:alnum:]]
            tab,space : 等价于 [[:space:]]
            [A-Z] : [[:upper:]]
            [a-z] : [[:lower:]]
            标点符号 : [[:punct:]]
            
            # grep "hello." demo.c
            # grep "hello[[:upper:]]" demo.c
            # grep "hello[^[:upper:]][[:digit:]]" demo.c
            # grep "hell[a-z]" demo.c
            # grep "hell[a-z][[:punct:]]" demo.c
            
        * 匹配次数：
            \{m,n\} : 至少m,至多n次
            \? : 0 或 1 次
            * : 任意次
            
            # grep "/.*sh" /etc/passwd
            # grep "/.\{0,2\}sh" /etc/passwd
            # grep -w ".\{0,2\}sh" /etc/passwd
            
        * 位置锚定:
            ^ : 锚定行首
            $ : 锚定行尾： `^$`用于匹配空白行（没有空格）
            \b 或 \< : 单词词首
            \b 或 \> : 单词词尾
            \B : 与\b 相反 `beer/B` 不是以beer结尾
            
            # grep "h" /etc/passwd
            # grep "h$" /etc/passwd
            # grep "\<sh" /etc/passwd
            # grep "\Bsh\b" /etc/passwd
            
        * 分组及引用：
            \(string\) : 将string作为一个整体方便后面引用
            \n : 第几个引用，0表示本记录，从1开始
            
            # grep "^\(.\{1\}\).*\1$" /etc/passwd
            # grep "^\([[:alpha:]]\).*\1$" /etc/passwd
            
        * 零宽断言：
            (?=pattern)  正向先行断言（正前瞻）
            (?<=pattern) 正向后行断言（正后顾）
            (?!pattern)  负向先行断言（负前瞻）
            (?<!=pattern)负向后行断言（负后顾）
            
            # 我爱祖国，我是祖国的花朵
            # 祖国(?=的花朵)
            # (?<=我爱)祖国
            # 祖国(?!的花朵)
            # (?<!我爱)祖国

    3. 扩展正则表达式(-E | egrep)
        `?` `+` `|` `()` `{}`

## 外链
* ### 3.1 rubular.com外链
    * [祖国(?=的花朵)](https://rubular.com/r/N5s1264MOiJzgG)
    * [(?<=我爱)祖国](https://rubular.com/r/k2Aj5zYb8VsdRl)
    * [祖国(?!的花朵)](https://rubular.com/r/n2r84kmPHbapfm)
    * [(?<!我爱)祖国](https://rubular.com/r/knBZziOjn0XBVA)
    * [电话号码](https://rubular.com/r/2F8fmkJKc64yMw)

* ### 3.2 RegEx 学习记录
    ```shell
    3.2.1正则匹配同时包含两个字符，不分先后顺序,https://rubular.com/r/C8klIhBII08CXN
    ^(?=.*?test)(?=.*?bash).*$
        以下下列子可能有助于理解:如果需要匹配字符，需要定界。
        ^(?=.*?test)(?=.*?bash).{9,}$
        ^(?=.*?test).{0,}(?=bash)
        ^(?=.*test)(?=.*bash)
        ^(?=.*test)(?=.*bash).{2}
        
        
    3.2.2 正则不过滤但深色显示
    cat abcdef | grep "content\|$"

    3.2.3 网址之后一部分，或者没有网址的方式 (https://rubular.com/r/sXLihPuKPpJrCI)
    /(^.*\/|^)(.*)$/
    ```

## Reference
* [linux中grep命令的用法](https://www.cnblogs.com/flyor/p/6411140.html )
* [grep中使用"\d"匹配数字不成功的原因解决](https://blog.csdn.net/yufenghyc/article/details/51078107)