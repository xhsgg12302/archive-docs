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

        ?> SSH 除了登录服务器，还有一大用途，就是作为加密通信的中介，充当两台服务器之间的通信加密跳板，使得原本不加密的通信变成加密通信。这个功能称为端口转发（port forwarding），又称 SSH 隧道（tunnel）。
        <br><br>端口转发有两个主要作用：
        <br>（1）将不加密的数据放在 SSH 安全连接里面传输，使得原本不安全的网络服务增加了安全性，比如通过端口转发访问 Telnet、FTP 等明文服务，数据传输就都会加密。
        <br>（2）作为数据通信的加密跳板，绕过网络防火墙。
        <br>端口转发有三种使用方法：`动态转发`，`本地转发`，`远程转发`。下面逐一介绍。

        1. #### 动态转发

            ![](/.images/devops/network/ssh/ssh-tunnel-dynamic-00.png ':size=90%')

            > [!NOTE]动态转发指的是，本机与 SSH 服务器之间创建了一个加密连接，然后本机内部针对某个端口的通信，都通过这个加密连接转发。它的一个使用场景就是，访问所有外部网站，都通过 SSH 转发。
            <br>动态转发需要把本地端口绑定到 SSH 服务器。至于 SSH 服务器要去访问哪一个网站，完全是动态的，取决于原始通信，所以叫做动态转发。
            <br><br>比如：`ssh -D local-port tunnel-host -N`
            <br>上面命令中，`-D`表示动态转发，`local-port`是本地端口，`tunnel-host`是 SSH 服务器，`-N`表示这个 SSH 连接只进行端口转发，不登录远程 Shell，不能执行远程命令，只能充当隧道。
            <br><br>举例来说，如果本地端口是2121，那么动态转发的命令就是下面这样。`ssh -D 12302 12302 -N`。此时程序hang住，监听12302端口，后面的12302是我本地配置的主机名。
            <br>端口转发使用`curl -v -x socks5://localhost:12302 https://wtfu.site/robots.txt`
            
            > [!ATTENTION|style:flat]***注意，这种转发采用了 `SOCKS5` 协议。访问外部网站时，需要把 HTTP 请求转成 SOCKS5 协议，才能把本地端口的请求转发出去。***

            <!-- panels:start -->
            <!-- div:left-panel-55 -->
            ![](/.images/devops/network/ssh/ssh-tunnel-dynamic-01.png ':size=100%')
            <!-- div:right-panel-45 -->
            ![](/.images/devops/network/ssh/ssh-tunnel-dynamic-02.png ':size=90%')
            <!-- panels:end -->

        2. #### 本地转发

            ![](/.images/devops/network/ssh/ssh-tunnel-local-00.png ':size=90%')

            > [!NOTE]本地转发（local forwarding）指的是，创建一个本地端口，将发往该端口的所有通信都通过 SSH 服务器，转发到指定的远程服务器的端口。这种情况下，SSH 服务器只是一个作为跳板的中介，**用于连接本地计算机无法直接连接的远程服务器**。本地转发是在本地计算机建立的转发规则。
            <br><br>它的语法如下，其中会指定本地端口（local-port）、SSH 服务器（tunnel-host）、远程服务器（target-host）和远程端口（target-port）。
            <br>`ssh -L -N -f local-port:target-host:target-port tunnel-host`。
            <br>`-L`：转发本地端口。`-N`：不发送任何命令，只用来建立连接。没有这个参数，会在 SSH 服务器打开一个 Shell。`-f`：将 SSH 连接放到后台。没有这个参数，暂时不用 SSH 连接时，终端会失去响应。
            <br>***如果经常使用本地转发，可以将设置写入 SSH 客户端的用户个人配置文件(~/.ssh/config):*** `LocalForward client-IP:client-port server-IP:server-port`
            <br><br>比如：将本地端口 **12302** 的流量通过ssh跳板 **'12302'** 传输到目标 **wtfu.site:443** 。`ssh -L 12302:wtfu.site:443 12302 -N`
            <br>然后使用curl进行如下连接.`curl -v -x '' -k -H 'Host: wtfu.site' https://localhost:12302/robots.txt`

            <!-- panels:start -->
            <!-- div:left-panel-55 -->
            ![](/.images/devops/network/ssh/ssh-tunnel-local-01.png ':size=100%')
            <!-- div:right-panel-45 -->
            ![](/.images/devops/network/ssh/ssh-tunnel-local-02.png ':size=90%')
            <!-- panels:end -->

        3. #### 远程转发

            ![](/.images/devops/network/ssh/ssh-tunnel-remote-00.png ':size=90%')

            > [!NOTE]远程转发指的是在远程 SSH 服务器建立的转发规则。它跟本地转发正好反过来。建立本地计算机到远程 SSH 服务器的隧道以后，本地转发是通过本地计算机访问远程 SSH 服务器，而远程转发则是通过远程 SSH 服务器访问本地计算机。它的命令格式如下。
            <br>`ssh -R remote-port:target-host:target-port remotehost -N`
            <br>上面命令中，`-R`参数表示远程端口转发，`remote-port`是远程 SSH 服务器的端口，`target-host`和`target-port`是目标服务器及其端口，`remotehost`是远程 SSH 服务器。
            <br><br>远程转发主要针对内网的情况。下面举两个例子。
            <br>1. 第一个例子是内网某台服务器localhost在 3000 端口开了一个docosify服务，可以通过远程转发将这个 3000 端口，映射到具有公网 IP 地址的`'12302'`服务器的`13402`端口，使得访问**wtfu.site:13402**这个地址，就可以访问到那台内网服务器的 3000 端口。
            <br>具体命令：`ssh -R 13402:localhost:3000 12302 -N`

            > [!ATTENTION|style:flat]
            ***1. 一般来说服务器在公网上可以访问的话，可能会有防火墙或者访问策略组之类的东西，我们需要的端口`13402`权限得放开***
            <br>***2. 远程sshd服务一般对于`GatewayPorts`此选项的默认值为`on`, 要想可以在远程服务器上监听所有网络接口(interface)，在sshd配置文件`/etc/ssh/sshd_config`中必须启用`GatewayPorts yes`***

            * ##### 参考

                2.1 **ssh -R options:**
                By default, TCP listening sockets on the server will be bound to the loopback interface only.  This may be overridden by specifying a bind_address.  An empty bind_address, or the address '*', indicates that the remote socket should listen on all interfaces.  Specifying a remote bind_address will only succeed if the server's GatewayPorts option is enabled (see sshd_config(5)).

                2.2 **sshd_config:**
                `GatewayPorts`: Specifies whether remote hosts are allowed to connect to local forwarded ports.  By default, ssh(1) binds local port forwardings to the loopback address.  This prevents other remote hosts from connecting to forwarded ports.GatewayPorts can be used to specify that ssh should bind local port forwardings to the wildcard address, thus allowing remote hosts to connect to forwarded ports.  The argument must be yes or no (the default).

         
            <!-- panels:start -->
            <!-- div:left-panel-55 -->
            ![](/.images/devops/network/ssh/ssh-tunnel-remote-01.png ':size=100%')
            <!-- div:right-panel-45 -->
            ![](/.images/devops/network/ssh/ssh-tunnel-remote-02.png ':size=90%')
            <!-- panels:end -->

* ## Reference

    + https://wangdoc.com/ssh/port-forwarding
    + https://drive.google.com/file/d/129rEQvCHsYuwKsEcUkuZY-L44-jptuEy/view?usp=sharing