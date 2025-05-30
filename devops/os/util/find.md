* ## Intro(find)

    + ### syntax

        ```shell
        # 1，在home目录下查找txt 结尾或者 sh 结尾的文件
        find /home -name "*.txt" -o -name "*.sh"
        # 2，查找路径，文件名中包含 lib 的文件
        find /home -path "*lib*"
        # 3，表达式匹配
        find /home -regex ".*(\.txt | \.sh)$"
        # 4，否定参数
        find /home ! -name "*.txt"
        # 5，文件类型
        find /home -type [f普通文件 | l符号连接 | d 目录 | c 字符设备| b块设备 | s套接字 | p Fifo]
        # 6，目录深度搜索
        find /home -maxdepth 3
        # 7，文件大小
        find /home -size [+10k | -10k | 10K]
        # 8，过滤权限拒绝的文件
        find /home [-perm 777 | -user tom | -group git]
        # 9，exec | ok
        find /home -size 10k -[exec | ok] ls -l {} \; 
        ```

    + ### 样例

        1. `find / -name "*.jar" -a -path "*jetbrains*" 2>/dev/null `

        2. 备份文件到另外一个地方

            ```shell
            # mac 语法，先筛选符合的文件
            find ./ -type f -and -d 1 -and ! -name 'venvs' -and ! -name '.DS_Store' -exec ls -l {} \;
            # 然后使用cp 命令移动
            find ./ -type f -and -d 1 -and ! -name 'venvs' -and ! -name '.DS_Store' -exec cp {} ~/Desktop/configure-files/conf/dockerfile \;
            ```

        3. mac

            ```shell
            # 查找当前目录在的所有jar包，除了./jdk 中的
            find . -type d \( -path "./jdk" -prune \) -o -type f -name "*.jar" -print

            # 查找路径中存在target 并且以后缀.jar结尾的文件。
            find . -type d -name "target" -exec find {} -name "*.jar" \; | xargs -n 1 -I {} ls -l {}

            # 模糊查找pdf文件，比如 忽略大小写 带mysql关键字的pdf文件
            find / -name '*.pdf' 2>/dev/null | grep -i 'mysql'

            # 删除  number 开头或 emoji 开头的文件。
            find cmake-build-debug/bin/build \( -name "number*" -or -name "emoji*" \) -exec rm -rf {} \;
            ```

* ## Reference

    + https://www.gnu.org/software/findutils/manual/html_node/find_html/Shell-Pattern-Matching.html