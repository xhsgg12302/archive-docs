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
$ git clone --bare https://github.com/XHS-12302/helloworld.git
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
# git rm -r --cached .idea

# 同理，取消其他文件追踪也一样。
```

## GIT基本操作
* ### 文件查看
	1. 简介
	> `git ls-files` 命令是用来查看暂存区中文件信息
	
	2.  常用参数
		| index | explain|
		|--|--|
		|`--cached(-c)`|显示暂存区中文件，`git ls-files`命令默认的参数|
		|`--delete(-d)`|显示删除的文件|
		|`--modified(-m)`|显示修改过的文件|
		|`--other(-o)`|显示没有被git跟踪的文件|
		|`--stage(-s)`|显示mode以及文件对应的Blob对象，进而可以获取文件内容|
	3. 实例
		* 查看暂存区中有哪些文件
			`git ls-files`
		* 查看文件内容
			+ 查看file对应的Blob 对象，like this:
				`git ls-files -s -- FILE`

			+ 查看本地仓库文件
				`git ls-tree --full-tree HEAD`
				```
				git ls-tree --full-tree -r HEAD -- cherry-pick.txt
				git ls-files -s -- cherry-pick.txt
				```
			+ 然后通过Blob对象，查看里面的内容
				`git cat-file -p PREFIX`
    4. 本地仓库文件
     	1. 实例（未验证）
		>  `git show master:FILE`
		>  `git show :cherry-pick.txt`  查看 暂存区文件
		>  `git show cherry:cherry-pick.txt` 
		>  `git show 61ea06c341fcfe80ddd10cbabdf8e9283851753b:cherry-pick.txt` 

	5. 回退文件
		```
		# 使用本地仓库的文件覆盖 暂存区
		$ git restore --staged cherry-pick.txt
		# 
		$ git restore <file>..." to discard changes in working directory
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
	```

* ### 分支  &nbsp;[doc](https://git-scm.com/docs/git-branch)
	```shell
	# 创建分支
	git branch prod
	git branch -b newBranch # 创建并检出分支
	# 查看分支
	git branch -v  [-vv 包括upstream info]
	# 分支重命名
	git branch -m prod prod-rename
	# 删除分支
	git branch -d prod

	# 关联上游
	git branch --set-upstream-to=origin/newb prod
	# 断联上游
	git branch --unset-upstream prod

	```
	1. 创建分支
	2. 
	3. 本地分支改名
		`git branch -m test-branch learning`
	4. 推送新分支
		`git push origin learning`
	5. 删除远程分支（两种方式）
		* `git push origin :test-branch`
		* `git push --delete origin test-branch`

* ### 标签
	* 列出本地标签
		` git tag `
	* 新建标签(`-f 强制覆盖`)
		` git tag -a TAGNAME -m 'COMMENT'`
	* 删除本地标签
		` git tag -d TAGNAME`
	* 删除远程标签
		` git push origin :refs/tags/TAGNAME `
	* 推送标签
		` git push origin TAGNAME`

* ### 修复本地提交
	```shell
	# 修改commit message
	git commit --amend  # 其实类似于重新做了一次提交

	# cherry-pick
	# 将一些提交记录复制到当前所在位置
	*test $ git cherry-pick C2 C4

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

## Reference
* https://git-scm.com/book/en/v2/Git-Tools-Credential-Storage
* https://stackoverflow.com/questions/9550437/how-to-make-git-ignore-idea-files-created-by-rubymine
* https://learngitbranching.js.org/?locale=zh_CN
