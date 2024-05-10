* ## Intro(SSH)

    + ### SSH登录

        - #### 准备步骤

            1. 生成公私匙: `ssh-keygen -t rsa -b 4096 -C 12302@example.com`
            2. 复制公匙到主机:`cat ~/.ssh/id_virmach_ras.pub | ssh root@107.174.101.187 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"`

         
            ![](/.images/devops/network/ssh/ssh-keygen-01.png ':size=65%')

            * ##### ssh-copy-id使用

                ?> **(ssh-copy-id)** 是一个可执行的脚本文件，可以把本地的`ssh公钥文件`安装到远程主机对应的账户下。也就是将你的公共密钥填充到一个远程机器上的`authorized_keys`文件中。如果远程机器上存在`authorizedkeys`文件，且`authorizedkeys`有内容，则`ssh-copy-id` 可以将待传公钥 _追加_ 到`authorizedkeys`文件里。它也能够改变远程用户名的权限，如`~/.ssh`和`~/.ssh/authorized_keys`删除其写的权限，所以一般`/.ssh`给予`700`，`/.ssh/authorized_keys`给予`600`权限。
                <br><br>查看帮助：`ssh-copy-id -h`
                <br>复制命令：`ssh-copy-id [-i [identity_file]] [user@]machine`

                ![](/.images/devops/network/ssh/ssh-copy-id-01.png ':size=100%')

        - #### 登录原理

            > [!WARNING|style:flat] 1). 发送登录请求到远程主机，identity文件中包含一些信息。
            <br>2). 远程服务器利用这个账户下的公匙随机生成字符串然后发送给对方。
            <br>3). 对方利用私匙将字符串解密后发送给远程服务器。
            <br>4). 远程服务器验证通过后，准许对方登录。

    + ### SSH隧道

* ## Reference

    + https://wangdoc.com/ssh/port-forwarding