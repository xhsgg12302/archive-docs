* ## Intro(FRIDA)

    > [!] APK 加固信息
    <br> 通过MT文件管理器 -> 安装包提取(左侧菜单) -> 选择应用，查看加固状态

    ```sh
    # app-demo
    site.example.framework
    
    # 哔哩哔哩
    tv.danmaku.bili

    # 个人所得税
    cn.gov.tax.its

    # 交管12302
    com.tmri.app.main
    ```

    > [?] command
    <br><br> `frida -D 192.168.1.200:5544 -l agent/frida_multiple_unpinning.js -f com.tmri.app.main -F`
    <br> `frida -U -f com.example.seccon2015.rock_paper_scissors  -l _agent.js`
    <br> `frida --debug --runtime=v8 -U -f com.example.seccon2015.rock_paper_scissors  -l _agent.js`
    <br> `frida -D local -l _lesson/function.js -n hello`
    <br> `frida-ps -D 192.168.1.200:5544 -a`
    <br> `frida-ps -D local  | grep hello`
    <br><br>`adb shell pm path`

* ## Reference

    + https://frida.re/docs/home/
    + https://github.com/deathmemory/FridaContainer
    + https://bbs.kanxue.com/thread-277034.htm
    + https://www.cnblogs.com/wingsummer/p/16695557.html
    + https://rivers.chaitin.cn/blog/cqq58eh0lnec5jjugirg
    + https://blog.csdn.net/sevenjoin/article/details/104531190