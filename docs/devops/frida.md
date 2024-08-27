* ## Intro(FRIDA)

    ```sh
    echo hello
    # 个人所得税
    cn.gov.tax.its

    # 交管12302
    com.tmri.app.main
    ```

    > [?] command
    <br><br> `frida -D 192.168.1.200:5544 -l agent/frida_multiple_unpinning.js -f com.tmri.app.main -F`
    <br> `frida -U -f com.example.seccon2015.rock_paper_scissors  -l _agent.js`
    <br> `frida --debug --runtime=v8 -U -f com.example.seccon2015.rock_paper_scissors  -l _agent.js`
    <br> `frida-ps -D 192.168.1.200:5544 -a`

* ## Reference

    + https://frida.re/docs/home/
    + https://github.com/deathmemory/FridaContainer