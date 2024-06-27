* ## Intro(ADB)

    + ### PUSH

        > [?] push moive to android: 
        <br>`adb push /Users/stevenobelia/Downloads/temporary-download/阳光电影dygod.org.八角笼中.2023.HD.1080P.国语中字.mp4/阳光电影dygod.org.八角笼中.2023.HD.1080P.国语中字.mp4  /sdcard/`

    + ### SYSTEMPROP(getprop)

        > [?] 通过[magisk设置系统属性](https://github.com/topjohnwu/Magisk/blob/master/docs/guides.md#the-system-folder)，然后根据[文章](https://xdaforums.com/t/how-to-make-adb-listen-to-tcpip-5555-after-reboot.1825359/#post-30032364)操作：`setprop service.adb.tcp.port 5555`:可以让手机中的adbd开放指定的tcp端口，方便连接调试。

        > [!CAUTION] `1).` <span style='color:blue'>一直开放tcp端口也有分险，需要注意。</span>
        <br>`2).` 直接在`/system/build.prop`文件中编辑不生效。需要借助magisk模块中的`system.prop`。

        ![](/.images/devops/os/util/adb-prop-01.png ':size=48%')
        ![](/.images/devops/os/util/adb-prop-02.png ':size=49%')

## Reference
* https://github.com/12302-bak/awesome-adb
* https://forum.xda-developers.com/t/a-magisk-script-to-set-adb-tcpip-port-at-startup-without-bricking-my-phone.4533603/
* https://github.com/12302-bak/awesome-adb?tab=readme-ov-file#%E6%97%A0%E7%BA%BF%E8%BF%9E%E6%8E%A5%E6%97%A0%E9%9C%80%E5%80%9F%E5%8A%A9-usb-%E7%BA%BF
* https://xdaforums.com/t/how-to-make-adb-listen-to-tcpip-5555-after-reboot.1825359/#post-30032364
* https://github.com/topjohnwu/Magisk/blob/master/docs/guides.md#the-system-folder