!> GPG
## 安装
```shell
# 1. 源码安装
# https://www.gnupg.org/download/index.en.html
./configure
make && make install

# 2. debian/ubuntu
sudo apt-get install gnupg

# 3. Fedora/centos
yum install gnupg
```


## 使用

### operation
```shell
# 1. 查看公匙和私匙
gpg -k/-K [name]

# 2. 生成公私匙
gpg --gen-key

# 3. 输出密匙
gpg --armor -o xx.txt --export [name|userID]
gpg --armor -o xx.txt --export-secret-keys [name|userID]

# 4. 查看公匙指纹
gpg --fingerprint [name|userID]

# 5. 上传公匙
gpg --keyserver hkp://www.example.com --send-keys [name|userID]
gpg --keyserver hkp://www.example.com --recv-keys [name|userID]

# 6. 导入公匙
gpg --import [file]
```

### openvpn 样例
```
-- 导入openvpn公匙
wget -O security-openvpn-net.asc https://keys.openpgp.org/vks/v1/by-fingerprint/F554A3687412CFFEBDEFE0A312F5F7B42F2B01E7
gpg --import security-openvpn-net.asc

-- 下载签名文件
wget -c https://swupdate.openvpn.org/community/releases/openvpn-2.5.5.tar.gz.asc

-- 下载源文件
wget -c https://swupdate.openvpn.org/community/releases/openvpn-2.5.5.tar.gz

-- 验证
gpg --verify openvpn-2.5.5.tar.gz.asc openvpn-2.5.5.tar.gz
```

## 公用服务器
* http://keyserver.ubuntu.com/
* https://keys.openpgp.org/

## Reference
* [GPG入门教程 | ruanyifeng ](http://www.ruanyifeng.com/blog/2013/07/gpg.html)
* [openvpn | GnuPG Public Key](https://openvpn.net/community-resources/sig/ 'hello')
* [python3.10.6 gpg](https://www.python.org/downloads/release/python-3106/)