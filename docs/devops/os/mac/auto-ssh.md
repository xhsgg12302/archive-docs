## AUTO 

!> 使用密码自动登录

```shell
#!/bin/zsh

# > /usr/local/bin/ssh

host=$1
#password=`awk "/#Password/ && inhost { print \\\$2 } /Host/ { inhost=0 } /Host $host/ { inhost=1 }" ~/.ssh/config`
password=`awk "/#Password/ && inhost { print \\\$2 } /Host / { inhost = 0 } /Host $host/ { inhost=1 }" ~/.ssh/config`

if [[ -z "$password" ]]; then
  /usr/bin/ssh $*
else
  sshpass -p $password /usr/bin/ssh $*
fi
```


## Reference
* https://askubuntu.com/questions/87956/can-you-set-passwords-in-ssh-config-to-allow-automatic-login
* https://stackoverflow.com/questions/32255660/how-to-install-sshpass-on-mac