## Linux 免密登录

> *远程登录过程：*
>
> 1. 利用工具生成公私匙  `identity` `identity.pub`
>
> 2. 将`identity.pub `里面的内容上传至/home/user/.ssh/authorized_keys里面
>
> 3. 利用私匙登录。
>
>    ![image-20191203124018520](C:\Users\elizabeth\AppData\Roaming\Typora\typora-user-images\image-20191203124018520.png)
>
> 远程登录原理：
>
> 1. 发送登录请求到远程主机，identity文件中包含一些信息。
> 2. 远程服务器利用这个账户下的公匙随机生成字符串然后发送给对方。
> 3. 对方利用私匙将字符串解密后发送给远程服务器。
> 4. 远程服务器验证通过后，准许对方登录。