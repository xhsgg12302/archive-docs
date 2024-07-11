## 命令记录


```shell
#!/bin/bash
# 首例打印数字权限
ls -l | awk 'NR>1{cmd="stat --printf \"%a\t\" "$9;system(cmd) ;print $0}'
```

```shell
#!/bin/bash
# 批量删除统一后缀文件
find . -name "*.txt" -type f -delete
```

```shell
#!/bin/bash
# 文件最好用""包起来，另外"使用\转义
# 批量文件重命名
ls -l | awk 'NR>1{origin=substr($0,57);now=substr($0,105);cmd="mv " "\"" origin "\"" " "  "\"" now "\"";system(cmd);}'
# awk 制定截取字符
ls -l | grep '小鱼儿' | awk '{origin=substr($0,index($0,$9),match($0,/\.mp3/));now="_1_"origin;cmd="mv "  "\""origin"\"" " " "\""now"\"";print cmd}'
```

```shell
#!/bin/bash
# curl 返回状态码
curl https://www.baidu.com -I -m 10 -o /dev/null -s -w "%{http_code}\n"
```

```shell
#!/bin/bash
# 删除100分钟内刚猜解压的文件夹及文件，注意这次包含当前目录，需要排除 [.]
find . \( -path "./WEB-INF" -prune -o  -cmin -100 \) ! -path './.DS_Store' -ok rm -rf {} \;
```

```shell
#!/bin/bash
# curl ftp 上传
curl -u wang: -T /Users/stevenobelia/Downloads/temporary-download/阳光电影www.ygdy8.com.拆弹专家2.HD.1080p.国语中字.mkv/阳光电影www.ygdy8.com.拆弹专家2.HD.1080p.国语中 字.mkv  ftp://192.168.2.2:2121/Download/
```

```shell
#!/bin/bash
# xargs 连接多个命令
echo 'obase=10;ibase=16;00020000' | bc | xargs -I % bash -c 'echo "%/2014/2014" | bc '
```

```shell
#!/bin/bash
# 将当前目录下的文件移动到当前目录新建目录中
ls ./ | grep -v 'docs' | xargs -n 1 -I {} mv {} docs
```

```shell
#!bin/bash
# 批量修改问价后缀
rename 's/\.jpg$/.webp/' *.jpg
```

> [?] 批量转换 webm 到gif
<br>`find webps/ -type f -name "*.webp" -print0 | xargs -0 -I{} sh -c 'ffmpeg -y -loglevel quiet  -i "{}" -pix_fmt rgb24 "webps-gif/$(basename {}).gif"'`

> [?] 将'download 下 each/gif 中的gif文件复制到 all/gif下。比如 ./download/each/gif/abc.gif -> ./download/all/gif/abc.gif'
<br>`find . -maxdepth 1 -mindepth 1 -type d ! -name 'all' -exec sh -c 'find "$1" -name  "*.gif" -print ' _ {} \; | xargs -n 1 -I {} cp {} all/gif`