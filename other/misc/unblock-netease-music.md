
* ## Intro(UNblock Netease Music)

    > [?] 解锁网易云变灰歌曲，

    ```shell
    docker run -d --restart=always \
        -v /root/UNMCertificates/server.crt:/app/server.crt \
        -v /root/UNMCertificates/server.key:/app/server.key \
        -p 14163:14163 -p 14126:14126 --name UNM  pan93412/unblock-netease-music-enhanced -t hello:world -p 14163:14126 -o kugou kuwo bilibili
    ```
* ## Reference

    + https://github.com/UnblockNeteaseMusic/server