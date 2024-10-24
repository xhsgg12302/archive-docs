## AUTO 

> [!] 使用密码自动登录

### /usr/local/bin/ssh
```shell
# 先安装 sshpass 
brew install hudochenkov/sshpass/sshpass
```

```shell
#!/bin/zsh

host=$1
#password=`awk "/#Password/ && inhost { print \\\$2 } /Host/ { inhost=0 } /Host $host/ { inhost=1 }" ~/.ssh/config`
password=`awk "/#Password/ && inhost { print \\\$2 } /Host / { inhost = 0 } /Host $host/ { inhost=1 }" ~/.ssh/config`

if [[ -z "$password" ]]; then
  /usr/bin/ssh $*
else
  sshpass -p $password /usr/bin/ssh $*
fi
```

```shell
Host local
    HostName 127.0.0.1
    User root
    Port 23

Host rein
    HostName 47.98.226.149
    User rein
    Port 13322
    IdentityFile ~/.ssh/rein_id
    #Password 1***********0

Host meta
    HostName 10.28.4.2
    User root
    Port 22
    IdentityFile ~/.ssh/meta-just.pem

Host msb1v1
    HostName m.meishubao.com
    User wtf
    Port 2220
    #Password ********!
    # ProxyCommand sshpass -p *******! ssh -t -p %p %r@%h
```


## Reference
* https://askubuntu.com/questions/87956/can-you-set-passwords-in-ssh-config-to-allow-automatic-login
* https://stackoverflow.com/questions/32255660/how-to-install-sshpass-on-mac
* https://www.ohse.de/uwe/software/lrzsz.html
* https://github.com/trzsz/trzsz
* https://github.com/trzsz/trzsz/tree/main/trzsz-iterm2