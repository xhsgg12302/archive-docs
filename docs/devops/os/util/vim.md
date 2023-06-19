## 百度搜索技巧

| 格式                      | 意思                                      |
| ------------------------- | ----------------------------------------- |
| `""`                      | 双引号，精确限定                          |
| `-`                       | 去掉-后面某个关键字(比特币 -比特币交易网) |
| `|`                       | 或 （坚果Pro \| 香蕉）前后都有空格        |
| `intitle: allintitle:`    | 标题中包含                                |
| `inurl:() 关键字`         | url中包含gov的如(inurl:gov 先进制造业)    |
| `filetype:xml,pdf 关键字` | 搜索pdf,xml格式                           |
| `site:关键字`             |                                           |


## vim 基础知识

> vim 的全局配置文件一般放在 `/etc/vim/vimrc`(run commands)(run control)(run config)或者`/etc/vimrc`,对所有用户生效，用户个人配置在`~/.vimrc`

##### 显示行号

> _单双引号表示行注释,查询某个配置项是打开还是关闭，可以在命令模式下，输入该配置，并在后面加上问号。`:set number?`_

* `:set number`

* ```
  " 打开 set number
  " 关闭 set nonumber
  ```

## 基本配置

| 命令                                          | 解释                                                         |
| --------------------------------------------- | ------------------------------------------------------------ |
| 基本配置                                      |                                                              |
| `set nocompatible`                            | 不与VI兼容（采用vim自己的操作命令）                          |
| `syntax on`                                   | 打开语法高亮。自动识别代码，多种颜色显示                     |
| `set showmode`                                | 在底部显示编辑模式                                           |
| `set showcmd`                                 | 命令模式底部显示键入指令                                     |
| `set mouse=a`                                 | 支持使用鼠标                                                 |
| `set encoding=utf-8`                          | 使用utf-8编码                                                |
| `set t_Co=256`                                | 启用256色                                                    |
| `filetype indent on`                          | 开启文件类型检查                                             |
|                                               |                                                              |
| 缩进                                          |                                                              |
| `set autoindet`                               | 按下回车，自动缩进，跟上一行保持一致                         |
| `set tabstop=2`                               | 按下Tab键，vim显示的空格数                                   |
| `set shiftwidth=4`                            | \>> << ==                                                    |
| `set expandtab`                               | 将TAB 转为空格                                               |
| `set softtabstop=2`                           | Tab转为多个空格                                              |
|                                               |                                                              |
| 外观                                          |                                                              |
| `set number`                                  | 显示行号                                                     |
| `set relativenumber`                          | 其他行为对应该行的相对行号                                   |
| `set cursorline`                              | 光标所在行高亮                                               |
| `set textwidth=80`                            | 设置行宽，一行显示多少个字符                                 |
| `set wrap`  `set nowrap`                      | 自动拆行，即太长的行分成几行                                 |
| `set linebreak`                               | 折行不会发生在单词内部                                       |
| `set wrapmargin=2`                            | 折行与窗口边缘的空出字符                                     |
| `set scrolloff=5`                             | 垂直滚动时，光标距离顶部/底部的位置                          |
| `set sidescrolloff=15`                        | 水平滚动时                                                   |
| `set laststatus=2`                            | 是否显示状态栏                                               |
| `set ruler`                                   | 在状态栏显示光标的位置                                       |
|                                               |                                                              |
| 搜索                                          |                                                              |
| `set showmatch`                               | 自动高亮另半个括号                                           |
| `set hlsearch`                                | 搜索时，高亮显示匹配结果                                     |
| `set incsearch`                               | 自动高亮第一匹配的结果                                       |
| `set ignorecase`                              | 搜索时忽略大小写                                             |
| `set smartcase`                               | 同时打开ignorecase                                           |
|                                               |                                                              |
| 编辑                                          |                                                              |
| `set spell spelllang=en_us`                   | 打开英语单词的拼写检查                                       |
| `set nobackup`                                | 不创建备份文件                                               |
| `set noswapfile`                              | 不创建交换文件                                               |
| `set undofile`                                | 保留撤销历史                                                 |
| `set backupdir=~/.vim/.backup`                |                                                              |
| `set directory=`                              | 设置备份文件保存位置                                         |
| `set undodir`                                 |                                                              |
| `set autochdir`                               | 自动切换工作目录                                             |
| `set noerrorbells`                            | 出错时，不要发出响声                                         |
| `set visualbell`                              | 出错时，发出视觉提示                                         |
| `set history`                                 | vim 需要记住多少次历史操作                                   |
| `set autoread`                                | 打开文件监视                                                 |
| `set listchars=tab:»■,trail:■ set list`       | 该配置将让多余空格显示成可见的小方块。                       |
| `set wildmenu set wildmode=longest:list,full` | 命令模式下，底部操作指令按下 Tab 键自动补全。第一次按下 Tab，会显示所有匹配的操作指令的清单；第二次按下 Tab，会依次选择各个指令。 |



## ANNOTATION
| annotation | result（effect）| 
--- | ---
| `word` | 字面意思，比如`<go!duto!school`> 不包括其他是三个单词 |
| `WORD` | 一长串中间没有空格的字符 |


## MOTION
| motion | result（effect）| 
      ---|---
| `iw` | inner word  without white space |
| `iW` | inner WORD  without white space |
| `aw` | a word  with white space |
| `aW` | a WORD  with white space |
| `it` | inner tag block <a>it</a> |

## TODO
| del() | result（effect） |
      ---|---
| `d` | 只是删除操作，并不带motion,一般为`d + motion`|
| `d$`,`D` | 删除光标到结尾的所有字符 |
| `dl`,`x` | 删除光标下的字符 |
| `dh`,`X` | 删除光标之前的字符 |
| `diw` | 删除 inner word 不包括空白字符 |
| `daw` | 删除 a word 包括空白字符 |
| `dw` | 删除光标后word 包括空白字符 |
|--|--|
| `c` | 只是删除并进入插入操作，并不带motion,一般为`d + motion`|
| `c$`,`C` | 删除光标到结尾的字符并进入插入模式|
| `cl`,`s` | 删除光标下的字符 && 进入插入模式|
| `cc`,`S` | 删除一行并进入插入模式|



| index | content | 
---|---
新建窗口 | `:(crtl + w) n`
分割窗口 | `:(ctrl + w) s`
垂直分割窗口 | `:(ctrl + w) v`
关闭当前窗口 | `:(ctrl + w) c`
关闭其它窗口 | `:(ctrl + w) o`
向上轮换窗口 | `:(ctrl + w) R`
向下轮换窗口 | `:(crtl + w) r`
使窗口等宽 | `:(crtl + w) =`
使窗口最小化 | `:(ctrl + w) 1_`
使窗口最大化 | `:(crtl + w) _`
使窗口向左最小化 | `:(crtl +w ) 1|`
使窗口向右最大化 | `:(crtl + w) |`
将缓冲区分割到一个窗口中 | `:(crtl + w) ^`

## 1. 打开多个窗口打开多个窗口的命令以下几个：
*   ### 横向切割窗口
    *   `:new+窗口名(保存后就是文件名)`
    *   `:split+窗口名，也可以简写为:sp+窗口名`
*   ### 纵向切割窗口名
    *   `:vsplit+窗口名，也可以简写为：vsp+窗口名`

## 2. 关闭多窗口可以用：
> `q!`，也可以使用`:close`，最后一个窗口不能使用close关闭。使用close只是暂时关闭窗口，其内容还在缓存中，只有使用`q!、w!或x`才能真能退出。`:tabc` 关闭当前窗口`:tabo` 关闭所有窗口

## 3. 窗口切换
> :ctrl+w+j/k，通过j/k可以上下切换，或者:ctrl+w加上下左右键，还可以通过快速双击ctrl+w依次切换窗口。

## 4. 窗口大小调整纵向调整

operator | moral
---|---
`:ctrl+w + ` | 纵向扩大（行数增加）
`:ctrl+w - ` | 纵向缩小 （行数减少）
`:res(ize) num `| 例如：:res 5，显示行数调整为5行
`:res(ize)+num `| 把当前窗口高度增加num行
`:res(ize)-num `| 把当前窗口高度减少num行
`:vertical res(ize) num ` | 指定当前窗口为num列
`:vertical res(ize)+num ` | 把当前窗口增加num列
`:vertical res(ize)-num ` | 把当前窗口减少num列

## 5. 给窗口重命名
`:f file`

## 6. vi打开多文件
> `vi a b c`
`:n` 跳至下一个文件，也可以直接指定要跳的文件，如`:n c`，可以直接跳到c文件
`:e#` 回到刚才编辑的文件

## 7. 文件浏览

operator | moral
---|---
`:Ex` | 开启目录浏览器，可以浏览当前目录下的所有文件，并可以选择
`:Sex` | 水平分割当前窗口，并在一个窗口中开启目录浏览器
`:ls` | 显示当前buffer情况

## 8. vi与shell切换

operator | moral
---|---
`:shell` |  可以在不关闭vi的情况下切换到shell命令行
`:exit` | 从shell回到vi

