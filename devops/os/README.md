---
weight: 30
---

<!-- varibale -->
[Readline]: https://en.wikipedia.org/wiki/GNU_Readline

> [!] 操作系统

* ## 终端

	> [!NOTE] 终端，有时候也被称为“伪终端”（Pseudo Terminal，简称 PTY），是因为现在基本很少看到真实的物理硬件终端了，大部分是通过软件模拟出来的逻辑终端设备对。
	<br><br>相对于伪终端来说，之前还存在物理终端（Physical Terminal）。
	<br>在早期大型机和类 Unix 系统时代，终端（Terminal）是实实在在的物理硬件——比如电传打字机（Teletypewriter，TTY）或带屏幕的显示终端，它们通过物理电缆（如串口线）连接到计算机主机，用于人机交互的纯输入输出硬件设备。它本身不具备独立的计算处理能力（不含CPU或只有极弱的控制芯片），仅仅负责把用户的按键等操作发送给主机，并把主机处理后的结果显示或打印出来。
	<br>_据说在 1960 年代末和 1970 年代初，贝尔实验室的 Ken Thompson 和 Dennis Ritchie 在开发早期 Unix 系统（最初运行在 PDP-7 和 PDP-11 小型机上）时，主要使用的输入输出设备就是电传打字机（Teletype，最著名的是 Teletype Model 33 ASR）_。
	<br><br>电传打印机（Teletypewriter, TTY）：像一台带有键盘的沉重打字机。它没有显示器，用户在键盘上敲一行字，机器会“哒哒哒”地在卷筒纸上把它打印出来；而计算机的回应，也是由这台机器直接在纸上打印出来的。
	<br><br>学习当中使用到的工具 tsc(tty serial control) 如下:

	<details><summary>tsc 工具代码及测试</summary>

	<span style='padding:2.3em'></span> ![](/.images/devops/os/os-tty-control-01.gif ':size=80%')

	```c [data-file:tsc.c,data-cc:340px]
	#include <stdio.h>
	#include <stdlib.h>
	#include <string.h>
	#include <unistd.h>
	#include <fcntl.h>
	#include <sys/ioctl.h>
	#include <errno.h>

	/*
	 * @args: --help
	 * 
	 * 有两个模式：write、ioctl。
	 *    因为通过 write 发送的东西会将字符发送到当前屏幕，控制光标，标题都可以，但是不能执行类似 Enter 按键的操作，
	 *    幸运的是，内核专门留了口子：TIOCSTI（Terminal I/O Control – STI，即 "Simulate Terminal Input"）最直接，注入输入队列。man 手册的官方描述就一句话：Insert the given byte in the input queue。
	 *    这个可以通过 ioctl 去操作，所以有两种模式。
	 *    TIOCSTI 会把参数 ch（一个 unsigned char）直接插入该 TTY 的输入缓冲区头部，仿佛用户刚按下了这个键——完全绕过键盘驱动、输入事件队列，甚至 getty/login 的读取逻辑，以最高优先级进入线路规程处理链。
	 *    TIOCSTI 在新内核被默认禁用（Linux ≥ 6.2 默认关闭 CONFIG_LEGACY_TIOCSTI），输入模式可能会报 EPERM；输出模式不受影响。
	 */

	static void usage(const char *prog)
	{
		printf(
			"用法: %1$s <tty> <字符串>\n"
			"  向指定终端写入普通字符或者控制命令\n"
			"\n"
			"选项:  \n"
			"  -m, --mode 模式: ioctl、write\n"
			"  -n, --raw  不解析转义序列\n"
			"  -h, --help 帮助\n"
			"\n"
			"示例:\n"
			"  %1$s /dev/ttys007 -m write -n '\\n'                     # 发送 \\n 字符串\n"
			"  %1$s /dev/ttys007 -m write    '\\n'                     # 通过 write 发送转义控制字符 \\n\n"
			"  %1$s /dev/ttys007             '\\n'                     # 通过 ioctl 发送转义控制字符 \\n ，模拟 Enter 按键\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[3A'                   # 上移 3 行\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[3B'                   # 下移 3 行\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[3C'                   # 右移 3 行\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[3D'                   # 左移 3 行\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[3;4H'                 # 定位到第 3 行第 4 列\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[H'                    # 回到左上角\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[2G'                   # 移到当前行第 2 列\n"
			"  %1$s /dev/ttys007 -m write '\\x08'                      # 退格一格（光标后退）\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[D'                    # 退格一格（光标后退）\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[1P'                   # 删除光标左边一个字符\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[K'                    # 清除光标到行尾\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[2J'                   # 清整个屏幕\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[31mFAILED\\x1b[0m'     # 红色前景\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[1mBOLD\\x1b[0m'        # 加粗\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[7mINV\\x1b[0m'         # 反显\n"
			"  %1$s /dev/ttys007 -m write '\\x1b]0;hi\\x07'             # 设置窗口标题为 hi\n"
			"  %1$s /dev/ttys007 -m write '\\x1b[12;40HHello\\x1b[13;40HWorld'      # 在第 12 行第 40 列写 Hello，第 13 行第 40 列写 World\n",
			prog);
	}

	/* 十六进制字符 -> 数值，非十六进制返回 -1 */
	static int hexval(int c)
	{
		if (c >= '0' && c <= '9') return c - '0';
		if (c >= 'a' && c <= 'f') return c - 'a' + 10;
		if (c >= 'A' && c <= 'F') return c - 'A' + 10;
		return -1;
	}

	/*
	 * 将 s 中的转义序列解析后写入 out 字符数组，返回转义后的字节数。
	 * out 需要足够大（至少 strlen(s) 字节即可，转义只会缩短或不变）。
	 */
	static size_t parse_escape_string(const char *s, char *out)
	{
		size_t n = strlen(s);
		size_t k = 0;

		for (size_t i = 0; i < n; i++) {
			unsigned char c;

			if (s[i] != '\\' || i + 1 >= n) {
				c = (unsigned char)s[i];            /* 普通字符 */
			} else {
				i++;                                /* 前进到反斜杠后的字符 */
				switch (s[i]) {
					case 'n': c = '\n'; break;
					case 'r': c = '\r'; break;
					case 't': c = '\t'; break;
					case 'b': c = '\b'; break;
					case 'a': c = '\a'; break;
					case 'f': c = '\f'; break;
					case 'v': c = '\v'; break;
					case '\\': c = '\\'; break;
					case 'x': {
						int v = 0, d = 0;           /* \xHH：最多 2 位十六进制 */
						while (i + 1 < n && d < 2) {
							int h = hexval(s[i + 1]);
							if (h < 0) break;
							v = v * 16 + h; i++; d++;
						}
						c = (d > 0) ? (unsigned char)v : 'x';
						break;
					}
					default:
						c = (unsigned char)s[i];    /* 未知转义，原样输出该字符 */
						break;
				}
			}
			fprintf(stderr, "%02x ", c);
			out[k++] = (char)c;
		}

		return k;
	}

	/*
	 * 完整写入 len 个字节，处理 write 只写一部分 / 被信号打断的情况。
	 * 同时把发送的字节以十六进制打印到 stderr（空格分隔，便于观察）。
	 * raw != 0 时直接写原始字符串；raw == 0 时写转义后的内容。
	 */
	static int write_ctrl(int fd, const char *s, int raw)
	{
		char *buf;
		size_t len = strlen(s);

		if (raw) {
			buf = (char *)s;
			for (size_t k = 0; k < len; k++) {
				fprintf(stderr, "%02x ", buf[k]);
			}
		} else {
			buf = malloc(len + 1);
			if (!buf) return -1;
			len = parse_escape_string(s, buf);
		}

		size_t off = 0;
		while (off < len) {
			ssize_t w = write(fd, buf + off, len - off);
			if (w < 0) {
				if (errno == EINTR) continue;
				if (!raw) free(buf);
				return -1;
			}
			off += (size_t)w;
		}

		if (!raw) free(buf);
		return 0;
	}

	/*
	 * 将 s 转义后逐字节通过 TIOCSTI 注入 fd。
	 * TIOCSTI 每次只能注入一个字符，故必须遍历转义后的字符数组。
	 */
	static int ioctl_ctrl(int fd, const char *s)
	{
		size_t len = strlen(s);
		char *buf = malloc(len + 1);
		if (!buf) return -1;

		len = parse_escape_string(s, buf);

		for (size_t i = 0; i < len; i++) {
			unsigned char c = (unsigned char)buf[i];
			if (ioctl(fd, TIOCSTI, &c) < 0) {
				fprintf(stderr, "\nTIOCSTI 失败(第 %zu 字节): %s\n", i, strerror(errno));
				free(buf);
				return -1;
			}
		}

		free(buf);
		return 0;
	}

	int main(int argc, char **argv)
	{
		const char *tty = NULL;
		const char *str = NULL;
		int raw = 0;
		int mode = 0; // ioctl_ctrl

		/* 解析参数 */
		for (int i = 1; i < argc; i++) {
			if (strcmp(argv[i], "-h") == 0 || strcmp(argv[i], "--help") == 0) {
				usage(argv[0]);
				return 0;
			} else if (strcmp(argv[i], "-n") == 0 || strcmp(argv[i], "--raw") == 0) {
				raw = 1;
			} else if (strcmp(argv[i], "-m") == 0 || strcmp(argv[i], "--mode") == 0) {
				if (i + 1 >= argc) {                       /* -m 在末尾，没有值 */
					fprintf(stderr, "-m/--mode 需要参数: write 或 ioctl\n");
					usage(argv[0]);
					return 2;
				}
				const char *m = argv[++i];
				if (strcmp(m, "write") == 0)  mode = 1;
			} else if (tty == NULL) {
				tty = argv[i];
			} else if (str == NULL) {
				str = argv[i];
			} else {
				fprintf(stderr, "多余的参数: %s\n", argv[i]);
				usage(argv[0]);
				return 2;
			}
		}

		/* 打开目标 tty */
		int fd = open(tty, O_WRONLY);
		if (fd < 0) {
			fprintf(stderr, "打开 %s 失败: %s\n", tty, strerror(errno));
			return 1;
		}

		int rc = mode ? write_ctrl(fd, str, raw) : ioctl_ctrl(fd, str);

		fprintf(stderr, "\n");
		close(fd);
		return rc < 0 ? 1 : 0;
	}
	```
	</details>

	+ ### 控制字符

		> [?] [控制字符](https://zh.wikipedia.org/wiki/控制字符)，在计算中，一个控制字符或非打印字符，所有输出在 32 以下的 ASCII 表都是这个类型。可以使用`ascii -x`查看前面 32 个控制字符的含义，一般结合 “Control”或“Ctrl”（有时也作“Cntl”）的键，即与其他字母或符号键一起按下。
		<br>通过这种方式使用控制键将生成同时按下的字符键的 ASCII 编码字节的 7 位中，左起的两个字符强制定为 0；<span style='color: #f909b6'>从而产生出 32 个 ASCII 控制码之一</span>。
		<br>也可以理解为使用 ctrl 修饰的话，相当于将与之组合的 ASCII 的值减去 0x40，以便取得前 32 个控制字符。
		<br>使用一个字节去控制的也叫 [C0 控制字符](https://zh.wikipedia.org/wiki/C0与C1控制字符#C0_(ASCII及其派生))，比如`0x7`(振铃)、两个字节的叫 [C1 控制字符](https://zh.wikipedia.org/wiki/C0与C1控制字符#C1控制字符集)，比如`0x1b [`([Control Sequence Introducer](https://zh.wikipedia.org/wiki/ANSI转义序列#CSI序列))、等等。
		<br><br>例如，按下 CTRL 和字母 G（十进制编码为 71，二进制为 01000111），产生编码 7（振铃符，十进制编码 7，或二进制 00000111 ）。`G[0x47] - 0x40 = BEL[0x7]`
		<br>所以在终端里面按`ctrl + G` 的时候会响一下。也可以通过命令模拟按键`sudo ./tsc /dev/ttys003 '\x07'`测试，效果一样。
		<br>同理还可以有其他的(在现代终端里，有些可能被 Shell（如 Bash、Zsh）或者行编辑器（如 [Readline][Readline]）重新定义了，比如`ctrl + C`、`ctrl + E`)：
		<br><span style='padding:2.3em'>`ctrl + A`	SOH（Start of Heading）：报头开始
		<br><span style='padding:2.3em'>`ctrl + B`	STX	(Start of Text)：正文开始
		<br><span style='padding:2.3em'>`ctrl + C`	ETX	(End of Text）：正文结束
		<br><span style='padding:2.3em'>`ctrl + D`	EOT	(End of Transmission）：传输结束
		<br><span style='padding:2.3em'>`ctrl + E`	ENQ	(Enquiry）：查询
		<br><span style='padding:2.3em'>`ctrl + F`	ACK	(Acknowledge）：确认
		<br><span style='padding:2.3em'>`ctrl + G`	BEL	(Bell）：振铃
		<br><span style='padding:2.3em'>`ctrl + H`	BS	(Backspace）：退格
		<br><span style='padding:2.3em'>`ctrl + I`	HT	(Horizontal Tab）：水平制表
		<br><span style='padding:2.3em'>`ctrl + J`	LF	(Line Feed）：馈行
		<br><span style='padding:2.3em'>`ctrl + K`	VT	(Vertical Tab）：垂直制表
		<br><span style='padding:2.3em'>`ctrl + L`	FF	(Form Feed）：馈页
		<br><span style='padding:2.3em'>`ctrl + M`	CR	(Carriage Return）：回车
		<br><span style='padding:2.3em'>`ctrl + [`	ESC	(Escape）：转义
		<br><br><span style='padding:1.0em'></span>![](/.images/devops/os/os-ascii-01.png ':size=95%')

	+ ### ANSI 转义序列

		> [?] [ANSI转义序列 (ANSI escape sequences)](https://zh.wikipedia.org/wiki/ANSI转义序列) 具有不同的长度。所有序列都以 ASCII 字符 ESC（27 / 十六进制 0x1b）开头，第二个字节则是0x40–0x5F（ASCII @A–Z[\]^_）范围内的字符。
		<br>ANSI 转义序列则是一种带内信令（in-band signaling）标准：在普通文本流里插入特定字节序列，终端把这些字节解释成指令而不是普通字符，用来控制光标位置、颜色和其他显示选项。
		<br>一句话：ANSI 转义序列是“用控制字符打头的一套带内指令协议”，控制字符是它的字母表，转义序列是用这套字母表写出来的句子（命令）。
		<br><br>而且固定都是`ESC`控制字符开头，除 ESC 之外的 C0 控制字符在输出时也会产生与某些转义序列相似的效果，
		<br>比如 `\n（\x0a）`和 `ESC E（\x1bE）`都实现换行并回到行首。它们是并行存在的两套机制，不是替代关系。可以使用`sudo ./tsc /dev/ttys003 -m write '\x1bE'`验证。

	+ ### MacOSX中 Terminal 和 iterm2 设置 Meta

		> [?] Meta 键（元键）是一个修饰键，本身没有独立功能，必须和其他键组合使用来触发快捷键。
		<br>Linux 语境里 Super 键和 Meta 键常被混用，都指 Win 键；但在 Space Cadet 键盘的原始设计里，Meta、Super、Hyper 是三个不同的修饰键。
		<br><br>在 macOS 中，默认的 Option 键是用来输入特殊符号的（比如按 Option + . 会打出 ≥，按 Option + f 会打出 ƒ）。而 Linux/Unix 终端里的 Alt / Meta 键，本质上就是“先发送一个 ESC（\x1b），紧接着发送你按下的那个字符”
		所以我们需要修改 Option 的默认配置，映射到`Alt/Meta` 上，生成`\x1b.`命令，但是这个按照第二个字节的规定不属于 [ANSI 转义序列](#ansi-转义序列)，终端本身不执行，所以给到 shell 按照诸如[Readline][Readline] 的逻辑执行，实现插入上一条命令最后一个参数的效果。
		<br>映射后的按键`Option + .` 等同于给指定终端发送了`\x1b.`，可以通过`sudo ./tsc /dev/ttys003 '\x1b.'` 验证。
		<br><br>两种终端配置如下图：
		<br>![](/.images/devops/os/os-terminal-meta-bind-01.png ':size=38%') ![](/.images/devops/os/os-terminal-meta-bind-02.png ':size=61%')

	+ ### Shell 终端技巧

		1. #### ctrl 修饰

			详细的可以参考 [Readline][Readline]
			<br><br>`ctrl + R`：(反向搜索) 调用包含指定字符的最后一个命令，再次 Ctrl+r 调用与搜索相对应的下一个更前的命令。
			<br>`ctrl + A`：将光标移动到行开头 (相当于 Home 键)。
			<br>`ctrl + E`：将光标移动到行的末尾 (相当于 End 键)。
			<br>`ctrl + B`：将光标后移一个字符 (相当于按下←键)。
			<br>`ctrl + F`：将光标向前移动一个字符 (相当于键→)。
			<br>`ctrl + W`：清除光标前的 word 并将其复制到剪贴板。
			<br>`ctrl + U`：清除光标前的行内容并将其复制到剪贴板。
			<br>`ctrl + Y`：(yank) 从光标位置添加剪贴板内容。
			<br>`ctrl + Z`：向当前任务发送 SIGTSTP 信号，该任务将其暂停。要在后台执行，可以输入 bg。要从后台或暂停中恢复，可以发出 fg[‘process name or job id’(前台)]。
			<br>` alt + B`：将光标向后移动一个 word。
			<br>` alt + F`：将光标向前移动一个 word。
			<br>` alt + D`：清除光标后的 word 并将其复制到剪贴板。
			<br>` alt + .`：将最后一个参数插入前一个命令 (前一个历史条目的最后一个词)。参考 [Meta 设置](#macosx中-terminal-和-iterm2-设置-meta)

		2. #### !!

			属于[History Expansion](https://www.gnu.org/software/bash/manual/html_node/History-Interaction.html#History-Expansion) (历史扩展) 中的一部分，也可以通过[命令](https://stackoverflow.com/a/24672743) `man  -P 'less -p ^HISTORY\ EXPANSION' bash` 查看描述。
			<br>bash shell 提供了用于操作历史信息的特性。
			<br>可以使用命令`set -H`开启，`set +H`关闭 bash 这种特性，-H Enable ‘!’ style history substitution (see History Expansion). This option is on by default for interactive shells.
			<br>对于 zsh `setopt BANG_HIST`、`unsetopt NO_BANG_HIST` 开启，`setopt NO_BANG_HIST`、`unsetopt BANG_HIST` 关闭。
			<br><br>示例：如果上一条命令执行了`cp /var/log/alf.log /tmp/backup/`
			<br><br><span style='padding:2.3em'>`!:1:h`、`!^:h`: 上一条命令第一个参数目录路径`/var/log`</span>
			<br><span style='padding:2.3em'>`!^:t:r`: 上一条命令第一个参数去除后缀的文件名`alf`</span>
			<br><span style='padding:2.3em'>`!cp:*`、`!cp*`: 调用历史命令中某条特定命令的所有参数`/var/log/alf.log /tmp/backup/`</span>
			<br><span style='padding:2.3em'>`!^:s/alf/fla/:p`: 替换参数中的部分字符并先预览`/var/log/fla.log`</span>

			<!-- panels:start -->
			<!-- div:left-panel-33 -->
			事件指示符（Event Designators，选定哪条历史命令）
			```shell {1-2} [data-file:Event Designators]
			 

			# 开始历史展开（除非后跟空格、换行、= 或 (）。
			`!`

			# 引用上一条执行的命令，等同于 !-1。常用场景如 sudo !!。 
			`!!`

			# 引用历史列表中编号为 n 的命令。
			`!n`

			# 引用倒数第 n 条命令。
			`!-n`

			# 引用最近一条以 string 开头的命令。
			`!string`

			# 引用最近一条包含 string 的命令。
			`!?string[?]`

			# 快速替换，重复上一条命令并将第一个 string1 替换为 string2。
			`^string1^string2^`

			# 引用当前正在输入的整行命令。
			`!#`

			 

			 

			 
			```
			<!-- div:left-panel-33 -->
			词指示符（Word Designators，选定命令中的哪个参数）
			```shell {1-2} [data-file:Word Designators]
			# 通常用冒号 : 与事件指示符隔开（若后接 ^、$、*、- 
			# 或 % 时可省略冒号）。词编号从 0 开始。
			# 第 0 个词，即命令本身。
			`0`

			# 第 n 个参数。
			`n`

			# 第一个参数（等同于 1）。
			`^`

			# 最后一个参数（如 !$ 表示上一条命令的最后一个参数）。
			`$`

			# 最近一次 ?string? 搜索匹配到的那个词。
			`%`

			# 从第 x 个到第 y 个词的范围（-y 等同于 0-y）。
			`x-y`

			# 所有参数，即 1-$（如果命令没有参数则返回空）。
			`*`

			# 从第 x 个到最后一个参数（等同于 x-$）。
			`x*`

			# 从第 x 个到倒数第二个参数（不包含最后一个参数 $）。
			`x-`

			 

			```
			<!-- div:left-panel-33 -->
			修饰符（Modifiers，对提取出的内容进行加工）
			```shell {1-2} [data-file:Modifiers]
			# 跟在词指示符之后，每个修饰符前都需加冒号 :。

			# 移除路径末尾的文件名，只保留目录头部（Head）。
			`h`

			# 移除路径前面的所有目录，只保留末尾文件名（Tail）。
			`t`

			# 移除 .xxx 形式的后缀名，保留主文件名。
			`r`

			# 只保留 .xxx 形式的后缀名。
			`e`

			# 只打印展开后的新命令而不实际执行，并将其存入历史记录。
			`p`

			# 给展开后的词加上引号，防止后续再次发生 Shell 展开。
			`q`

			# 与 q 类似，但按空格和换行符拆分成多个词分别加引号。
			`x`

			# 将第一个匹配的 old 替换为 new（分隔符 / 可换成任意字符）。
			`s/old/new/`

			# 重复上一次的替换操作。
			`&`

			# 配合 s 使用（如 :gs/old/new/），在整行范围内进行全局替换。
			`g / a`。
			```
			<!-- panels:end -->

		3. #### cd -
		4. #### sudo tee

			```shell
			sudo echo '10.0.0.5 db' >> /etc/hosts
			# bash: /etc/hosts: Permission denied

			# 可以使用 tee 去处理
			# https://man7.org/linux/man-pages/man1/tee.1.html
			echo '10.0.0.5 db' | sudo tee -a /etc/hosts
			```
		5. #### brace expansion

			```shell
			echo {1..5}
			echo {a..e}
			echo {1..100..10}
			touch file{1..10}.txt
			rm *.{png,jpg,pdf}
			```

* ## 硬盘

	> [?] VDA和SDA是Linux系统中的磁盘驱动器名称。SDA 是默认的硬盘驱动器名称。SDA 通常指第一块硬盘，而SDB指第二块硬盘以此类推。VDA 是虚拟磁盘驱动器的默认名称。当使用虚拟化技术如VMware或Xen时，主机系统会将虚拟机磁盘挂载为VDA， VDB等。总之，SDA 通常是真实硬盘驱动器，而 VDA 通常是虚拟磁盘驱动器。

	+ ### 1.查看文件和文件夹大小

		* du -h --max-depth=0 * （列出当前文件夹下所有文件包括文件夹的大小）
		* du -sh    （统计当前文件夹的总和）
		* du -sh ./* 统计当前目录各文件夹大小
		* free -h 查看内存大小
		* lsblk 查看分区和磁盘
		* df -h 查看空间使用情况
		* fdisk -l 分区工具查看分区信息
		* cfdisk /dev/sda 查看分区
		* blkid 查看硬盘label别名
		* cat /proc/cpuinfo | grep "cpu cores" |uniq 查看CPU核心数

	
		> [!CAUTION] <span style='color: blue'>macOS 查看文件实际占用字节：</span>
		<br><br>`stat -f%z record_format_demo.frm`
		<br>`wc -c record_format_demo.frm`

* ## 文件系统

	+ ### 磁盘分区表

		> [!NOTE] 主要分为两种`MBR(Master boot record)`、`GPT`.

		1. MBR

			> [?] 主要开机区（Master boot record ，MBR），占用 `446` bytes，分区表（partition table），占用 `64` bytes, 另外加 两个字节的 结束标志`0x55AA`。[参考](https://blog.csdn.net/zyqash/article/details/129982908)

	+ ### 特性
		
		> [?] 一般来说，操作系统会将一个文件分为两部分`文件属性`、`文件内容`并存放在不通的区块。
		<br><br>区块：
		<br>`super block`：记录整个文件系统的信息，包括`block`与`inode`的总量，已经使用的,中a 方楠

		* https://note.youdao.com/web/#/file/recent/note/9B8C7926FB434E0B950F0B333AD6EC70/?search=%E5%88%86%E5%8C%BA
