## brew

### 安装软件

<!-- tabs:start -->
#### **1、官网旧版本Formula**
?> gdb example
```shell
# ref from: https://stackoverflow.com/questions/62785290/installing-previous-versions-of-a-formula-with-brew-extract

# TAP=...     # <org>/<repo>, for example "my-org/homebrew-old"
# MODULE=...  # name of module you want to install, e.g. "hugo"
# VERS=...    # version of $MODULE you want to install, e.g., "0.80.0"

brew tap-new 12302/gdb
# 在最新的仓库中提取原来的版本到新建的tap.
brew extract --version 13.1 gdb 12302/gdb
# 运行安装命令（其实这个时候已经可以通过 brew search gdb 搜索到需要安装版本的软件了）
brew install 12302/gdb/gdb@13.1
```
![](../../../../.images/devops/os/mac/brew-search-01.png)

#### **2、自定义仓库**
?> aria2 example
```shell
# 修改了 max-connection-per-server from 16 to 128,
# 参考： https://ntsd.dev/aria2-max-connections-per-server/
brew tap xhsgg12302/aria2 https://github.com/xhsgg12302/aria2.git
brew extract --version 1.36.0 aria2 xhsgg12302/aria2
tar -czvf aria2.tar.gz aria2
readlink -f aria2.tar.gz
brew install xhsgg12302/aria2/aria2@1.36.0 --verbose --debug --build-from-source
```
##### Reference
* https://github.com/Homebrew/homebrew-core/blob/5348a1685b3b59079ba0a16a3ebd95af66115ed0/Formula/aria2.rb
* https://ntsd.dev/aria2-max-connections-per-server/
* https://jingsam.github.io/2018/05/23/edit-a-brew-formula.html
<!-- tabs:end -->

## Reference
* https://github.com/Homebrew
* https://stackoverflow.com/questions/62785290/installing-previous-versions-of-a-formula-with-brew-extract
* https://stackoverflow.com/questions/3987683/homebrew-install-specific-version-of-formula