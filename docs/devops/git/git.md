## 凭据管理
* ### `git credential [fill|approve|reject]`
	> 用来取回和存储用户凭据。
	>
	> `credential-helper`系统的根命令是`git credential`,也可以说是credential最基本的命令。实际上调用的是`git-credential` 可执行文件。
	```shell
	# 查找可执行文件位置
	$ find / -name "git-credential" 2> /dev/null
	/Library/Developer/CommandLineTools/usr/libexec/git-core/git-credential

	# 验证`git credential`等价`/path/to/git-credential`,以下两条命令执行结果相同
	$ git credential -h
	$ /Library/Developer/CommandLineTools/usr/libexec/git-core/git-credential -h
	```
	> 查看git credential 帮助信息`git help credential`

	| option | des |
	|-- | -- |
	| fill   	| 根据标准输入获取的数据顺序匹配所有helper,匹配到立马返回，如何没有匹配到，会要求用户输入并打印 |
	| approve 	| 将标准输入发送到所有helper保存起来，方便后续使用 |
	| reject	| 将标准输入匹配到的所有helper中的描述行删除 |

	> 此处的三个选项不一定每个helper实现，有的helper可能没有删除选择。还得看具体实现，比如官方文档中给出的ruby脚本就没有删除选项。但是务必遵守approve和reject没有响应输出。

* ### 内置的两种基本helper(`store`,`cache`)
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
		name = XHS-12302
		email = xhsgg12302@gmail.com
	[core]
		autocrlf = input
	[credential]
		# useHttpPath = true
		helper = osxkeychain
		helper = store --file ~/.git-credentials
		helper = shell
		# helper = cache --timeout 900   // 900 min
	```
	> git-credential-shell
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
	
* ### helper配置格式
	> 上文之所以说git credential是根命令，最基本命令，是因为具体干活的是其他的，比如store，cache等，至于哪一个工具，就看配置了哪些
	| conf	| des	|
	| --	| -- 	|
	| foo	| Runs git-credential-foo |
	| foo -a --opt=bcd	| Runs git-credential-foo -a --opt=bcd |
	| /absolute/path/foo -xyz	| Runs /absolute/path/foo -xyz | 
	| !f() { echo "password=s3cre7"; }; f	| Code after ! evaluated in shell |
* ### 具体helper使用格式：`git-credential-foo [args] <action>`
	> action 包括（get，store，erase）,这三个action 对应git-credential命令的 fill,approve,reject.
	>
	> 也就是执行 git credential fill 的时候会在配置的credential helper中找到每个具体实现依次执行他们的get.
	>
	> git credential approve 依次执行每个具体helper的 store，reject -> erase同理。

* ### 自定义helper
	> 针对不同的场景可以自己编写程序，例如，多人协作，使用一个共享的credential配置。方式有ruby,java,python,shell等。可以参考官方文档。


## GIT仓库迁移
```shell
$ git clone --bare https://github.com/xhsgg12302/helloworld.git
$ cd helloworld.git
$ git push --mirror https://github.com/new.git
```

## GIT忽略文件提交
```shell
# 多人开发时,会出现明明在gitignore中忽略了.idea文件夹,但是提交时仍旧会出现.idea内文件变动的情况
# 原因
# .idea已经被git跟踪，之后再加入.gitignore后是没有作用的
# 解决办法
# 清除.idea的git缓存
git rm -r --cached .idea

# 同理，取消其他文件追踪也一样。
```

## GIT基本操作

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

* ### HEAD分离 
	```shell
	# HEAD 指向当前工作的commit 节点。不一定指向分支（比如分离状态下）

	*main $ git chekcout main # 将HEAD从main上分离   由原来的 HEAD->main->Cx  变为 HEAD->Cx

	# 相对引用
	# 使用 ^ 向上移动 1 个提交记录
	# 使用 ~<num> 向上移动多个提交记录，如 ~3
	git checkout main^  # 标识移动至 main 指向提交记录的上一个提交

	# 强制修改分支位置
	git branch -f main HEAD~3  # 将 main 分支强制指向 HEAD 的第 3 级父提交
	git branch -f bugFix C6  # 如果 HEAD 在 bugFix,则 HEAD也会跟随移动

	# reset
	git reset HEAD^ # 将当前分支和HEAD 都回退至上一个节点。

	# revert
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
	git cherry-pick 	# 命令后，HEAD 在当前新节点上。
	git rebase        	# 命令后，HEAD 在当前新节点上。
	git merge		  	# 命令后，HEAD 在当前新节点上。
	```

* ### 分支  &nbsp;[doc](https://git-scm.com/docs/git-branch)
	```shell
	# 创建分支
	git branch prod
	git branch -b newBranch [origin/main] # 创建并检出分支 [可以同时设置追踪的远程分支] | 也可参见HEAD 分离中的 `在任意位置创建分支`
	git branch -u origin/main foo # 如果当前在 foo 分支，则可以省略foo

	# 查看分支
	# https://stackoverflow.com/questions/171550/find-out-which-remote-branch-a-local-branch-is-tracking
	git branch [-vv | -v | -r | -a | --list] [-vv 包括upstream info]  
	
	# 分支重命名
	git branch -m prod prod-rename
	
	# 删除分支
	git branch -d prod

	# 关联上游
	git branch --set-upstream-to=origin/newb prod
	# 断联上游
	git branch --unset-upstream prod

	# 推送分支
	git push origin prod-test    // prod-test -> prod-test
	git push origin newb:newb-fake [-u | --set-upstream]

	# 删除远程分支
	git push origin :prod-test
	git push --delete origin prod-test

	```

* ### 标签
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

* ### 修复本地提交
	```shell
	# 修改commit message
	git commit --amend  # 其实类似于重新做了一次提交

	# 使用本地仓库的文件覆盖 暂存区
	$ git restore --staged cherry-pick.txt
	# 
	$ git restore <file>... to discard changes in working directory

	# cherry-pick
	# 将一些提交记录复制到HEAD指向的节点 (注意:提交记录不包含HEAD上游节点)
	*main $ git cherry-pick C2 C4
		   main 															  main
		   C5													   C5---C2'---C4'
		  /														/
	C0---C1---C2---C3---C4			$(git cherry-pick C2 C4)	C0---C1---C2---C3---C4	
						\														 \
						side													  side

	# git rebase [commit | branch]
	# 将当前分支或者节点 与目标分支或节点 不一致的节点复制到目标分支节点上。
	*bugFix $ git rebase main  # 会将bugFix上与main分之差异节点复制到main分之上。
	# 指定节点rebase. 将指定节点或分支 与 main的差异复制到main 上。
	*bugFix $ git rebase main bugFix
	*bugFix $ git rebase main C3 # 下面的视图在编辑的时候出现错位，以网站展示为准

		   main													     main      HEAD
		   C5										                   C5---C2'---C3'
		  /						git rebase main C3					/
	C0---C1---C2---C3---C4        ----------------------->             C0---C1---C2---C3---C4 
						\															 \
						bugFix														 bugFix
	⭕️ main*
	|
	⭕️ bugFix
	* main $ git rebase bugFix # 会将main分之直接移动到bugFix,由于继承关系。fast-forward

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
* ### 提交技巧
	![skill 1](../../../.images/git/mixed2.png ':size=48%') ![skill 2](../../../.images/git/mixed3.png ':size=48%')
	```shell
	# 如果在main分支上新建分支newImage并做提交C2，并且在newImage分支上创建分支caption并做提交C3。
	# 假如需要将C2修复 比如 修改提交信息。则可以如下尝试
	git rebase -i  # 将提交重新排序，把需要修改的记录挪到最前
	git commit --amend # 进行修改
	git rebase -i  # 调回原来的顺序
	# 最后将main 移动到修改的最前端。

	# 通过cherry-pick
	git checkout main
	git cherry-pick C2
	git commit --amend
	git cherry-pick C3
	```
* ### 高级
	![](../../../.images/git/advanced1.png ':size=33%') ![](../../../.images/git/advanced2.png ':size=33%') ![](../../../.images/git/advanced3.png ':size=33%')
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
	# 方式2：对于已经创建的分支指定
	git branch -u o/main foo
	```
	* #### push 参数
	```shell
	# git push 默认推送当前分支，如果不单独指定的话
	# 如果默认远程和本地分支名不一致，则使用 [:] ,git push origin <source>:<destination> ,参数实际上是 refspec, 比如：git push origin foo^:bar
	# 如果推送的远程分支不存在，则会在远程创建一个。 git push origin main:notexist
	# 如果省略source, 则表示删除远程分支
	```
	* #### fetch 参数
	```shell
	# 与push 类似，只是方向相反 git fetch origin <remote>:<source>
	# 一般不会直接更新本地分支，比如 git fetch origin foo~1:bar
	# 不存在本地，新建更新。
	# 没有参数，直接 git fetch，会更新远程所有分支
	```
	* #### 没有source
	```shell
	git push origin :main # 删除远程分支
	git fetch origin :bar # 创建本地分支
	```
	* #### pull 参数
	```shell
	# 下面两者等价（需要注意合并的对象，因为fetch默认不会更新本地分支，所以，第一个是 merge o/foo, 而第二个指明了 更新本地分支，所以会 merge bugFix）
	git pull origin foo
	git fetch origin foo; git merge o/foo

	git pull origin bar~1:bugFix
	git fetch origin bar~1:bugFix; git merge bugFix
	```
	* #### 下载并合并
	```shell
	*main : git pull origin bar:foo # 将远程bar分支 更新到本地foo分支，并且与main分支进行merge。
	```

## Reference
* https://git-scm.com/book/en/v2/Git-Tools-Credential-Storage
* https://stackoverflow.com/questions/9550437/how-to-make-git-ignore-idea-files-created-by-rubymine
* https://learngitbranching.js.org/?locale=zh_CN
