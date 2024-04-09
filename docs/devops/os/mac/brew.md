## brew

### 切换版本
> [!NOTE]
`brew unlink node@18`
<br>`brew link --overwrite --force node@14`

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
![](/.images/devops/os/mac/brew-search-01.png)

#### **2、自定义仓库**
!> brew安装
```shell
# 修改了 max-connection-per-server from 16 to 128,
# 参考： https://ntsd.dev/aria2-max-connections-per-server/
brew tap xhsgg12302/aria2 https://github.com/xhsgg12302/aria2.git
brew extract --version 1.36.0 aria2 xhsgg12302/aria2
# tar -czvf aria2.tar.gz aria2
# readlink -f aria2.tar.gz
# 计算sha256值
shasum -a 256 aria2-1.36.0.tar.xz
brew install xhsgg12302/aria2/aria2@1.36.0 --verbose --debug --build-from-source

# 使用原生的下载,即使使用128个单机并发，最多16个连接数
/usr/local/Cellar/aria2/1.36.0_2/bin/aria2c -s128 -k20M https://releases.ubuntu.com/22.04/ubuntu-22.04.2-desktop-amd64.iso
# 但是使用重新编译过后的,最大连接数可以达到128，至于速度问题，可能和当前网络环境，以及服务器带宽有关系。
# 这种针对服务器网络单个连接差的很有效。多起线程，聚少成多。
aria2c -s128 -k20M https://releases.ubuntu.com/22.04/ubuntu-22.04.2-desktop-amd64.iso
```

!> ubuntu安装
```shell
# Ubuntu20.04 install aria2c from source code
git clone https://github.com/aria2/aria2.git
cd aria2
vim src/OptionHandlerFactory.cc
    OptionHandler* op(new NumberOptionHandler(PREF_MAX_CONNECTION_PER_SERVER,
                                              TEXT_MAX_CONNECTION_PER_SERVER,
                                              "1", 1, 128, 'x'));
mkdir build && cd build
autoreconf -i
sudo apt-get install libssl-dev libssh2-1-dev
../configure --disable-dependency-tracking  --prefix=/usr/local --with-libssh2 --without-gnutls --without-libgmp --without-libnettle --without-libgcrypt --without-appletls --with-openssl
aria2c -h
```

![](/.images/devops/os/mac/brew-aria2c-01.png)
---
![](/.images/devops/os/mac/brew-aria2c-02.png)

##### Reference
* https://github.com/Homebrew/homebrew-core/blob/5348a1685b3b59079ba0a16a3ebd95af66115ed0/Formula/aria2.rb
* https://ntsd.dev/aria2-max-connections-per-server/
* https://jingsam.github.io/2018/05/23/edit-a-brew-formula.html
<!-- tabs:end -->

## Reference
* https://github.com/Homebrew
* https://stackoverflow.com/questions/62785290/installing-previous-versions-of-a-formula-with-brew-extract
* https://stackoverflow.com/questions/3987683/homebrew-install-specific-version-of-formula