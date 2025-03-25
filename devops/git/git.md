---
weight: 60
---

* ## 凭据管理

	* ### 1.基本操作

		> [!CAUTION] **用来取回和存储用户凭据**。
		</br>`git credential [fill|approve|reject]`
		</br>`credential-helper`系统的根命令是`git credential`,也可以说是credential最基本的命令。实际上调用的是`git-credential` 可执行文件。

		```shell
		# 查找可执行文件位置
		find / -name "git-credential" 2> /dev/null
		/Library/Developer/CommandLineTools/usr/libexec/git-core/git-credential

		# 验证`git credential`等价`/path/to/git-credential`,以下两条命令执行结果相同
		git credential -h
		/Library/Developer/CommandLineTools/usr/libexec/git-core/git-credential -h
		```
		> [?] 查看git credential 帮助信息`git help credential`

		| option | des |
		|-- | -- |
		| `fill`   	| 根据标准输入获取的数据顺序匹配所有helper,匹配到立马返回，如何没有匹配到，会要求用户输入并打印 |
		| `approve` 	| 将标准输入发送到所有helper保存起来，方便后续使用 |
		| `reject`	| 将标准输入匹配到的所有helper中的描述行删除 |

		> [?] 此处的三个选项不一定每个helper实现，有的helper可能没有删除选择。还得看具体实现，比如官方文档中给出的ruby脚本就没有删除选项。但是务必遵守approve和reject没有响应输出。

	* ### 2.内置的基本helper
		```shell
		# 可以通过命令进行查看 
		$ find /Library/Developer/CommandLineTools/usr/libexec/git-core -name "git-credential*"
		/Library/Developer/CommandLineTools/usr/libexec/git-core/git-credential-store
		/Library/Developer/CommandLineTools/usr/libexec/git-core/git-credential-cache
		/Library/Developer/CommandLineTools/usr/libexec/git-core/git-credential
		/Library/Developer/CommandLineTools/usr/libexec/git-core/git-credential-cache--daemon
		/Library/Developer/CommandLineTools/usr/libexec/git-core/git-credential-osxkeychain

		# store 私有选项
		git config --global credential.helper 'store --file ~/.my-credentials'
		# cache 私有选项
		cache --timeout 30000

		# 查看当前git系统中的配置情况
		$ git config --global -e
		[user]
			name = 12302
			email = 12302#gmail.com
		[core]
			autocrlf = input
		[credential]

			# 默认情况下，git不认为URI部分对helper匹配生效，可以通过 useHttpPath 来控制这种行为。
			# 比如 对于'https://example.com/foo.git' 的 helper 同样可以用于 'https://example.com/bar.git'
			# @see: https://git-scm.com/docs/gitcredentials#Documentation/gitcredentials.txt-useHttpPath
			# useHttpPath = true

			helper = osxkeychain
			helper = store --file ~/.git-credentials
			helper = shell
			# helper = cache --timeout 900   // 900 min
		```

		> [?] **git-credential-shell**

		```shell
		#!/bin/bash

		# 显示帮助
		Usage()
		{
		echo 'Usage:'
		cat <<EOF
			echo 'hello world'
			bet <input> <output> [options]
			# -d debug (do not delte temporary intermediate images)
		EOF
			exit 1
		}

		nargs=$#
		set -- `getopt a:b:c: $*`

		result=$?
		if [ $nargs -eq 0 ] || [ $result != 0 ] ; then
			Usage
		fi

		param_len=$#
		command=${!param_len}

		# while [ -n "$1" ]
		# do
			# case "$1" in
				# -a) echo "param = $2"
					# shift ;;
				# -b) echo "param = $2"
					# shift ;;
				# -c) echo "param = $2"
					# shift ;;
				# --) ;;
				# *)  echo "what's this?"
					# break ;;
			# esac
			# shift
		# done

		if [ $command = 'get' ] ; then
		cat << EOF
		password=demo12345
		username=test12302
		EOF
		fi
		```
		
	* ### 3.helper配置格式

		> [!WARNING|style:flat] 上文之所以说`git credential`是根命令，最基本命令，是因为具体干活的是其他的，比如`store、cache`等，至于哪一个工具，就看配置了哪些

		| conf	| des	|
		| --	| -- 	|
		| `foo`	| Runs git-credential-foo |
		| `foo -a --opt=bcd`	| Runs git-credential-foo -a --opt=bcd |
		| `/absolute/path/foo -xyz`	| Runs /absolute/path/foo -xyz | 
		| `!f() { echo "password=s3cre7"; }; f`	| Code after ! evaluated in shell |

	* ### 4.具体helper使用格式
	
		> [?] `git-credential-foo [args] <action>`
		<br><br>action 包括（get，store，erase）,这三个action 对应git-credential命令的 fill,approve,reject. </br>
		也就是执行 git credential fill 的时候会在配置的credential helper中找到每个具体实现依次执行他们的get. </br>
		git credential approve 依次执行每个具体helper的 store，reject -> erase同理。

	* ### 5.自定义helper

		> [?] 针对不同的场景可以自己编写程序，例如，多人协作，使用一个共享的credential配置。方式有ruby,java,python,shell等。可以参考官方文档。

---

* ## 子模块

	* ### 基本概念

		> [!TIP] 将一个仓库关联到另一个仓库
		<br>https://git-scm.com/docs/gitmodules <span style='padding-left:4.0em'/>定义子模块属性
		<br>https://git-scm.com/docs/gitsubmodules
		<br>https://git-scm.com/docs/git-submodule <span style='padding-left:2.5em'/>子模块操作命令

		<!-- panels:start -->
		<!-- div:left-panel-50 -->
		> [?] **.gitmodules**
		```shell
		[submodule "helloworld"]
			path = helloworld
			url = https://github.com/xhsgg12302/helloworld.git
		[submodule "demo"]
			path = demo
			url = https://github.com/xhsgg12302/uniscan.git
		```
		<!-- div:left-panel-50 -->
		> [?] **.git/config**
		```shell
		[submodule "helloworld"]
			active = true
			url = https://github.com/xhsgg12302/helloworld.git
		[submodule "demo"]
			url = https://github.com/xhsgg12302/uniscan.git
			active = true
		```
		<!-- panels:end -->

	* ### 使用场景

		> [?] **至少有以下两种使用场景**：
		<br>`1).` 使用另一个项目的同时保持独立的历史记录。
		<br><span style='padding-left:2.7em'/>子模块允许你在项目中使用其他git项目，同时保存历史记录隔离。所以子模块可以更新(固定)到任意版本，而且这个子模块可以单独开发，并不会影响当前项目。
		<br><span style='padding-left:2.7em'/>而且当父工程想更新它的时候，可以随时升级到最新版本。同理，回退也是可以的。
		<br><br>`2).` 物理隔离项目到多个仓库，也可以用来克服git实现的限制，实现更细粒度的访问控制。
		<br><span style='padding-left:2.7em'/>比如`Size of the Git repository`、`Transfer size`、`Access control`。

	* ### 基本操作

		```shell
		# https://git-scm.com/docs/git-submodule#Documentation/git-submodule.txt-init--ltpathgt82308203
		# 根据暂存区中记录的子模块进行初始化，主要是在配置文件 '.git/config' 中添加一个模块记录。并不会在 pathspec 有内容生成或更新。
		git submodule init demo

		# 正好与上述init相反。需要注意的是：如果是新添加的模块，则在deinit的时候因为在父工程暂存区中有记录，需要加'-f'才可以取消init，不然会报错
		git submodule deinit demo

		# 新增一个子模块 （会自动初始化）
		git submodule add https://github.com/xhsgg12302/helloworld.git [NAME]

		# 更新一个子模块 （如果是克隆的时候没有使用递归选项，则子模块是一个空目录，需要update命令进行检出父工程index中提交）
		# 		--init     		如果子模块没有初始化，进行初始化
		# 		--recursive		递归执行它里面的子模块	
		# 		--remote   		从远程拉取检出，没有的话就是从本地仓库检出
		git submodule update helloworld
		# 更新子模块的 url
		git submodule set-url .images https://github.com/xhsgg12302/archive-assets.git
		
		# 下述貌似不可解决远程更新后直接指向指定分支，比如master，有待学习
		# 执行 git submodule update --remote 默认情况下会将模块的远程 HEAD 指定的提交节点 hash 值。
		# 可以通过指定 submodule.<name>.branch 来改变这种行为，比如让追踪子模块 master
		# 有一个特殊的值 `.` 表示追踪子模块的分支名和当前项目的分支名一样。
		# 配置方式在 `.gitmodules` 和 `.git/config` 均可，但是后者优先级更高。
		git submodule update --remote

		# 删除一个子模块 see:https://git-scm.com/docs/gitsubmodules#_forms
		git rm <moduleName>
		rm -rf '$GIT_DIR/modules/<name>'

		# 克隆的时候通过递归克隆当前项目中的子模块 see: https://git-scm.com/docs/git-clone/2.32.0#Documentation/git-clone.txt---recurse-submodulesltpathspecgt
		# 1. 如果没有指定 pathspec，则克隆所有子模块
		# 2. recursive 是 recurse-submodules 的别名
		git clone [--recurse-submodules[=<pathspec>] | --recursive[=<pathspec>]] https://github.com/12302-bak/shell-scripts.git
		```

---

* ## GIT基本操作

	> [!NOTE] ~获取第一次提交记录~：`git log --reverse --format='%H' | head -n 1`
	<br>~获取文件的首次提交时间~：`git log --reverse --format="%ad" --date=short -- index.html | head -n 1`
	<br>~获取文件的最新更新时间~：`git log --format="%ad" --date=short -- index.html | head -n 1`
	<br>指定時間進行提交：`git commit -S -m 'update something' --date="2023-03-15 10:24:06"`
	<br><br>获取文件首次创建的时间：`git log --follow --diff-filter=A -- devops/git/git.md`
	<br>获取文件最新的更新时间：`git log --follow --diff-filter=M -- devops/build/gcc/library.md | head -n 1`
	
	> [!TIP] git 删除未追踪的文件，对于分支切换后遗留的文件或者编译后的文件。主要选项如下：
	<br>`-d`：递归删除，当指定 **pathspec** 的时候 -d 失效。`-f`：强制。`-e`：排除 pattern。`-i`：交互删除。`[-n | --dry-run]`：空转。`-x`：包括忽略文件。`-X`：只在忽略文件中移除。
	<br><br>查看追踪的文件：`git ls-files | awk -F'/' '{print $1}' | sort -u`，`git ls-files | cut -d'/' -f1 | sort -u`。
	<br>最佳删除：`git clean -e '.idea' -dx -n`。✅
	<br><br>![](/.images/devops/git/git-clean-01.png ':size=50%')

	* ### learngitbranching常用命令

		> [?] [help,levels,level move1, objective,undo,reset,show solution, show goal,hide goal]
	
	* ### 文件查看
		> `git ls-files` show information about files in the index and the working tree <br>
		`git ls-tree ` list the contents of a tree object
		
		1. `ls-files` 常用参数
			| index | explain|
			|--|--|
			|`--cached(-c)`|显示暂存区中文件，`git ls-files`命令默认的参数|
			|`--delete(-d)`|显示删除的文件|
			|`--modified(-m)`|显示修改过的文件|
			|`--other(-o)`|显示没有被git跟踪的文件|
			|`--stage(-s)`|显示mode以及文件对应的Blob对象，进而可以获取文件内容|

		2. 实例
			```shell
			# 查看暂存区中有哪些文件
			git ls-files

			# 查看file对应的Blob对象
			git ls-files -s -- FILE

			# 查看本地仓库文件Blob属性
			git ls-tree --full-tree HEAD
			git ls-tree --full-tree -r HEAD -- cherry-pick.txt

			# 通过Blob属性，查看文件内容
			git cat-file -p BLOB

			# show 方式
			git show master:FILE   # 查看本地仓库文件
			git show :cherry-pick.txt # 查看暂存区文件
			git show cherry:cherry-pick.txt
			git show 61ea06c341fcfe80ddd10cbabdf8e9283851753b:cherry-pick.txt
			``` 

	* ### 本地

		* #### 分支  &nbsp;[doc](https://git-scm.com/docs/git-branch)
			```shell
			# 检出一个分支
			git checkout -b <branch>  # 创建并检出分支，-B 存在覆盖

			# 创建分支
			git branch prod
			git branch newBranch [origin/main] # [可以同时设置追踪的远程分支] | 也可参见HEAD 分离中的 `在任意位置创建分支`
			git branch newBranch [commit]      # ❌ [无效分支点]

			# 查看分支
			# https://stackoverflow.com/questions/171550/find-out-which-remote-branch-a-local-branch-is-tracking
			git branch [-vv | -v | -r | -a | --list] [-vv 包括upstream info]  
			
			# 分支重命名
			git branch -m prod prod-rename
			
			# 删除分支
			git branch -d prod

			# 关联上游
			git branch --set-upstream-to=origin/newb prod
			git branch -u                origin/main foo      # 重置分支上游，且分支（foo）必须存在。不明确指定重置分支，则默认使用当前 HEAD 所在分支。
			# 断联上游
			git branch --unset-upstream prod

			# 推送分支
			git push origin prod-test    // prod-test -> prod-test
			git push origin newb:newb-fake [-u | --set-upstream]

			# 删除远程分支
			git push origin :prod-test
			git push --delete origin prod-test

			```

		* #### 标签
			```shell
			# 列出本地标签
			git tag
			# 验证tag
			git tag -v tagName

			# 新建标签
			# -a 创建一个无符号、带注释的标记对象 默认打开vim提交message。也可以通过 -m 一次性传递参数给message
			git tag [-f(强制覆盖)] -a tagName -m 'COMMENT'
			git tag 1.0.2-PRE C3

			# 删除本地标签
			git tag -d tagName

			# 推送标签
			git push origin tagName

			# 删除远程标签
			git push origin :[refs/tags/tagNme | tagName]
			git push --delete origin tagName

			# git describe
			git describe <ref> # <ref> 可以是任何能被 Git 识别成提交记录的引用，如果你没有指定的话，Git 会使用你目前所在的位置（HEAD）
			output: <tag>_<numCommits>_g<hash>  # tag表示的是离ref最近的标签,numCommits表示相差有多少个提交记录,hash表示提交记录hash值。 如果ref上有标签，则只输出标签名。
			```

		* #### HEAD分离 
			> [?] 通常来说，HEAD一般指向某个分支(比如main)。而HEAD分离指的是HEAD没有指向任何分支，而是指向某个commit(或者远程分支)。
			<br><br>如果在分离状态下提交commit，则需要在提交之后(`f178964`)新建分支与其关联`git branch <新分支名> f178964`,否则会丢失提交。
			<br>或者直接新建分支`git branch detached_3`,但是此时仍是分离状态，可以通过`git checkout detached_3`将HEAD指向新建分支。退出分离状态。
			<br>或者在新版中：如果您想要通过创建分支来保留在此状态下所做的提交，您可以通过在 switch 命令中添加参数 -c(创建并切换一个新分支) 来实现（现在或稍后）。例如：`git switch -c <新分支名>`

			```shell
			# HEAD 指向当前工作的commit 节点。不一定指向分支（比如分离状态下）

				*main $ git chekcout Cx # 将HEAD从main上分离   由原来的 HEAD->main->Cx  变为 HEAD->Cx

			# 相对引用

				# 使用 ^ 向上移动 1 个提交记录
				# 使用 ~<num> 向上移动多个提交记录，如 ~3
				git checkout main^  # 标识移动至 main 指向提交记录的上一个提交

			# 强制修改分支位置

				git branch -f main HEAD~3  # 将 main 分支强制指向 HEAD 的第 3 级父提交
				git branch -f bugFix C6  # 如果 HEAD 在 bugFix,则 HEAD也会跟随移动

			# 回退

				# reset
				git reset HEAD^ # 将当前分支和HEAD 都回退至上一个节点。

				# revert
				# 例如 C1 -- C2 -- C3  git revert C3 ==> C3' ,其实 C3' = C2
				git revert HEAD

			# 强制回退远程分支(在别人未拉取最新远程之前可以。之后的话，如果对方处理不好，则又会将上一个版本的东西带入当前分支)

				git reset --hard rebs^
				git push -f origin rebs:rebs 

			# 在任意位置创建分支

				git checkout f41a7ad45714adac3ba898e94b1728de212d3cf9 #将HEAD分离到 f41a节点
				git checkout -b newBranchOfHead # 在这个节点上创建并切换分支

			# comment

				git -c core.quotepath=false -c log.showSignature=false push --progress --porcelain origin refs/heads/rebs:rebs    [local:remote]

			# HEAD位置
			
				git cherry-pick      # 命令后，HEAD 在当前新节点上。
										# 因为需要往*所指的节点或者分支上pick,所以HEAD在当前分支或最新节点上面。例如下面`git cherry-pick C2 C4`
										# git cherry-pick ...,  head还指向当前分支或节点。
				git rebase           # 命令后，HEAD 在当前新节点上。 
										# bugFix(*),main, git rebase bugFix main ==> main(*)
										# bugFix(*),Cx, git rebase bugFix Cx ==> Cx(*), 例如下面`git rebase main C3`
										# git rebase base [other] , head 在 other上。
				git merge            # 命令后，HEAD 在当前新节点上。
			```

		* #### 乾坤大挪移

			```shell {15-20,29-34,46-52}
			# 修改commit message
			git commit --amend  # 其实类似于重新做了一次提交

			# 使用本地仓库的文件覆盖 暂存区
			$ git restore --staged cherry-pick.txt
			# 
			$ git restore <file>... to discard changes in working directory

			# 移动提交记录

				# cherry-pick [level move1]
				# 将一些提交记录复制到HEAD指向的节点 (注意:提交记录不包含HEAD上游节点)
				*main $ git cherry-pick C2 C4

				      main(*)                                                                  main(*)
				      C5                                                            C5---C2'---C4'
				      /                        git cherry-pick C2 C4                /
				C0---C1---C2---C3---C4        ----------------------->        C0---C1---C2---C3---C4	
				                     \                                                             \
				                      side                                                          side

				# git rebase [commit | branch]
				# 将当前分支或者节点 与目标分支或节点 不一致的节点复制到目标分支节点上。
				*bugFix $ git rebase main  # 会将bugFix上与main分之差异节点复制到main分之上。
				# 指定节点rebase. 将指定节点或分支 与 main的差异复制到main 上。
				*bugFix $ git rebase main bugFix
				*bugFix $ git rebase main C3 # 下面的视图在编辑的时候出现错位，以网站展示为准[已修复，由于tab缩进导致，改成空格即可]

				      main                                                          main       (*)
				      C5                                                            C5---C2'---C3'
				      /                          git rebase main C3                 /
				C0---C1---C2---C3---C4        ----------------------->        C0---C1---C2---C3---C4 
				                     \                                                             \
				                      bugFix(*)                                                     bugFix

				⭕️ main*
				|
				⭕️ bugFix
				* main $ git rebase bugFix # 会将main分之直接移动到bugFix,由于继承关系。fast-forward

				# git merge [commit | branch]{1}
				# 将当前分支或者节点 与目标分支或节点 生成共同的 merge 节点，并使用当前分支指向，被合并的分支毫无影响。
				*bugFix $ git merge main  # 生成一个新共同节点并将当前分支指向新节点。
				*bugFix $ git merge C5    # 如下所示，将 bugFix 与 C3 的差异合并，生成一个新节点 C7。

				           main                                                          main
				      C5---C6                                                       C5---C6
				      /                                                             / \
					 |                             git merge C5                    |   ﹉﹉﹉﹉﹉﹉﹉﹉﹉\
				C0---C1---C2---C3---C4        ----------------------->        C0---C1---C2---C3---C4---C7
				                     \                                                                  \
				                      bugFix(*)                                                          bugFix(*)

				# 交互式rebase -i
				# Rebase 1c6ae4f..f795a91 onto 1c6ae4f (5 commands)
				#
				# Commands:
				# p, pick <commit> = use commit
				# r, reword <commit> = use commit, but edit the commit message
				# e, edit <commit> = use commit, but stop for amending
				# s, squash <commit> = use commit, but meld into previous commit
				# f, fixup <commit> = like "squash", but discard this commit's log message
				# x, exec <command> = run command (the rest of the line) using shell
				# b, break = stop here (continue rebase later with 'git rebase --continue')
				# d, drop <commit> = remove commit
				# l, label <label> = label current HEAD with a name
				# t, reset <label> = reset HEAD to a label
				# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
				# .       create a merge commit using the original merge commit's
				# .       message (or the oneline, if no original merge commit was
				# .       specified). Use -c <commit> to reword the commit message.
				#
				# These lines can be re-ordered; they are executed from top to bottom.
			```
		
		* #### 提交技巧[#1|#2]
			![skill 1](/.images/devops/git/mixed2.png ':size=48%') 
			![skill 2](/.images/devops/git/mixed3.png ':size=48%')
			```shell
			# 如果在main分支上新建分支newImage并做提交C2，并且在newImage分支上创建分支caption并做提交C3。
			# 假如需要将C2修复 比如 修改提交信息。则可以如下尝试
			# #1
			git rebase -i  # 将提交重新排序，把需要修改的记录挪到最前
			git commit --amend # 进行修改
			git rebase -i  # 调回原来的顺序
			# 最后将main 移动到修改的最前端。

			# #2
			# 通过cherry-pick
			git checkout main
			git cherry-pick C2
			git commit --amend
			git cherry-pick C3
			```
		* #### 高级
			![](/.images/devops/git/advanced1.png ':size=36%') 
			![](/.images/devops/git/advanced2.png ':size=33%') 
			![](/.images/devops/git/advanced3.png ':size=27%')
			```shell
			# 多分支rebase
			git rebase main bugFix
			git rebase bugFix side
			git rebase side
			git rebase another main

			# 两个父节点
			# 操作符 ^ 与 ~ 符一样，后面也可以跟一个数字。但是该操作符后面的数字与 ~ 后面的不同，并不是用来指定向上返回几代，而是指定合并提交记录的某个父提交
			git branch bugWork HEAD~^2~1

			# 纠缠不清的分支 cherry-pick
			git checkout one
			git cherry-pick C4 C3 C2
			git checkout two
			git cherry-pick C5 C4 C3 C2
			git branch -f three C2
			```

	* ### 远程

		* #### 同步远程
		```shell
		git fetch; git rebase origin/main; git push
		git pull --rebase; git push

		git fetch; git merge origin/main; git push
		git pull; git push
		```
		![](/.images/devops/git/remote-rebase-01.gif)
		![](/.images/devops/git/remote-merge-01.gif)

		* #### 受保护的main分支
		```shell
		# 由于 main分支的保护特性，一般不会直接让开发人员推送到 main分支，需要用其他分支提交push到远程，再创建pull request请求合并。
		# 例如在main分支上做了本地提交，则应当
		git checkout -b feature  # 在当前节点创建并拉取新分支
		git push origin feature	 # 提交当前分支到远程， 此时可以创建PR
		git branch -f main origin/main  # 回退main分支。与远程保持一致。
		```
		* #### 推送主分支
		```shell
		# 多分支合并
		git fetch
		git rebase o/main side1
		git rebase side1 side2
		git rebase side2 side3
		git rebase side3 main
		git push

		git checkout main
		git pull
		git merge side1
		git merge side2
		git merge side3
		git push
		```
		* #### 远程追踪
		```shell
		# 方式1：自己在创建分支时指定
		git checkout -b totallyNotMain o/main
		# 方式2：对于已经创建的分支指定(如果在当前foo分支，则可以省略：`git branch -u o/main`)
		git branch -u o/main foo
		```
		![](/.images/devops/git/remote-track-01.gif)
		![](/.images/devops/git/remote-track-02.gif)

		* #### push 参数
		```shell
		# git push 
		# 默认推送HEAD指向的分支。如果HEAD分离，则无法推送。
		# git push origin main, HEAD无关，推向main分支关联的upstream.
		# 如果默认远程和本地分支名不一致，则使用 [:] ,git push origin <source>:<destination> ,参数实际上是 refspec, 比如：git push origin foo^:bar
		# 如果推送的远程分支不存在，则会在远程创建一个。 git push origin main:notexist
		# 如果省略source, 则表示删除远程分支
		```
		![](/.images/devops/git/remote-push-01.gif)

		* #### fetch 参数
		```shell
		# 与push 类似，只是方向相反 git fetch origin <remote>:<source>
		# 一般用法`git fetch origin foo`,直接更新o/foo远程分支。本地分支不会变动。
		# 一般不会直接更新本地分支，比如 git fetch origin foo~1:bar。如果在后面通过':'追加本地分支了，则会更新本地分支。
		# 不存在本地，新建更新。
		# 没有参数，直接 git fetch，会更新远程所有分支
		```
		![](/.images/devops/git/remote-fetch-01.gif)
		![](/.images/devops/git/remote-fetch-02.gif)
		![](/.images/devops/git/remote-fetch-03.gif)

		* #### 没有source
		```shell
		git push origin :main # 删除远程分支
		git fetch origin :bar # 创建本地分支
		```
		![](/.images/devops/git/remote-nosource-01.gif)
		![](/.images/devops/git/remote-nosource-02.gif)

		* #### pull 参数
		```shell
		# 下面两者等价（需要注意合并的对象，因为fetch默认不会更新本地分支，所以，第一个是 merge o/foo, 而第二个指明了 更新本地分支，所以会 merge bugFix）
		git pull origin foo
		git fetch origin foo; git merge o/foo

		git pull origin bar~1:bugFix
		git fetch origin bar~1:bugFix; git merge bugFix

		$ *bar: git pull origin main       # 将远程main分支 更新到本地main分支，并且与bar分支进行merge。
		$ *bar: git pull origin main:foo   # 将远程main分支 更新到本地foo分支，并且与bar分支进行merge。
		```
		![](/.images/devops/git/remote-pull-01.gif)
		![](/.images/devops/git/remote-pull-02.gif)

* ## GIT仓库迁移
	> [?] 1、git clone --bare https://github.com/xhsgg12302/helloworld.git
	<br>2、cd helloworld.git
	<br>3、git push --mirror https://github.com/new.git

* ## GIT管理项目
	> [?] `1).` 在github创建仓库 *https://github.com/user/repo.git*
	<br>`2).` 本地项目`git init`
	<br>`3).` 追踪本地资源`git add README.md`
	<br>`4).` 设置远程信息`git remote add origin https://github.com/user/repo.git`
	<br>`5).` 推送本地提交`git push -u origin main`

* ## GIT忽略文件提交
	```shell
	# 多人开发时,会出现明明在gitignore中忽略了.idea文件夹,但是提交时仍旧会出现.idea内文件变动的情况
	# 原因
	# .idea已经被git跟踪，之后再加入.gitignore后是没有作用的
	# 解决办法
	# 清除.idea的git缓存
	git rm -r --cached .idea

	# 同理，取消其他文件追踪也一样。
	```

* ## GIT推送缓冲区

	> [?] 在推送数据时会使用一个称为“对象数据库”（object database）的概念来存储对象。在推送过程中，Git 会将这些对象通过网络传输到远程仓库。
	<br>对于缓冲区大小的配置，Git 本身并没有直接的设置选项。但是，你可以通过一些间接的方式来影响推送过程中的数据传输效率，比如使用压缩算法来减小数据量。
	<br><br>例如，你可以通过以下命令来查看 Git 的配置：`git config --get http.postBuffer`.如果没有设置 http.postBuffer，那么 Git 将使用默认值，通常是 1 MiB（1048576 字节）。
	<br>这个值可以通过以下命令设置：`git config --global http.postBuffer 52428800`.这里设置的 http.postBuffer 是以字节为单位的，表示 Git 在执行推送操作时可以使用的最大缓冲区大小。
	<br>例如，上面的命令设置了 50 MB `(52428800 = 50 * 1024 * 1024)`的缓冲区大小。
	<br>![](/.images/devops/git/git-push-01.png ':size=47%') ![](/.images/devops/git/git-push-02.png ':size=46%')

* ## GIT仓库瘦身

	> [?] 使用的工具是 [**bfg-repo-cleaner**](https://rtyley.github.io/bfg-repo-cleaner/)，可以参考官方 usage，example 等，下载连接在官网右侧，是一个jar包。
	<br><br>使用步骤：
	<br>`1). `: 克隆原仓库镜像：`git clone --mirror https://github.com/xhsgg12302/knownledges.git`
	<br>`2). `: 依照给出的样例结合自己的需求进行删除文件：比如文件夹：`java -jar ../bfg-1.14.0.jar --delete-folders .images`、文件：`java -jar ../bfg-1.14.0.jar --delete-files _sidebar.md`
	<br>`3). `: 陆续删除之后执行命令进行清理压缩等操作：`git reflog expire --expire=now --all && git gc --prune=now --aggressive`
	<br>`4). `: 推送到远程：`git push`
	<br>`5). `: 可以使用命令查看当前仓库中的状态：`git count-objects -vH`
	<br><br>实操背景：
	<br>原来的仓库 **knownledges** 里面使用的是 docsify 文档工具来记录的，有一些问题：并非静态文件(通过js实时渲染)，文档，图片资源跟框架本身在一起管理，如果说需要更换框架的话，比校费事儿的是怎么保留文档本身的历史提交记录和修改，另外想使用 git submodule 功能一次性将 markdown 文档和 images 资源文件分开管理，好处就是资源文件的提交记录不用参杂在 markdown 里面，因为提交记录对于二进制大文件来说并没有什么作用，我们只需要最终版的，所以也方便直接覆盖。
	<br><br>实际操作：
	<br>将原来的 **knownledges** 备份成三个仓库 **archive**，**archive-docs**，**archive-asserts**。
	<br>**archive** 用来整理文档框架比如(docsify, hugo ,...) 可以有多个分支，使用 submodule 的方式整合另外两个（docs,asserts）仓库。
	<br><span style='padding-left:2.7em'/>使用 **bfg** 删除`.images`、`docs`文件夹，保留对 docsify 框架相关的文件修改，比如 index.html 修改记录会存在。
	<br>**archive-docs** 用来保存实际的书写 markdown 文档。记录干净，纯文本，仓库体积小。
	<br><span style='padding-left:2.7em'/>使用 **bfg** 可以删除除`docs`文件夹之外的所有文件夹和文件。<span style='color:blue'>这个我们是需要保留 docs 文件夹中的所有文件提交记录的。也是最重要的</span>
	<br>**archive-asserts** 用来保存文档中用的二进制资源文件，比如图片，gif，视频之类的。只要文件，提交记录无所谓，仓库体积较大，方便随时覆盖。
	<br><span style='padding-left:2.7em'/>使用 **bfg** 删除所有文件：`java -jar ../bfg-1.14.0.jar --delete-files '*'`，这个不会删除当前仓库引用的物理文件的。一般会删除提交记录和原来已经过时，没用的文件。

* ## Reference
	+ https://git-scm.com/book/en/v2/Git-Tools-Credential-Storage
	+ https://stackoverflow.com/questions/9550437/how-to-make-git-ignore-idea-files-created-by-rubymine
	+ https://learngitbranching.js.org/?locale=zh_CN