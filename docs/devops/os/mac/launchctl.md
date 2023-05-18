## launchctl

!> 可以使用的工具有 LaunchControl

```xml
<!-- ~/Library/LaunchAgents/dos.wtfu.site.plist -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
    <dict>
        <key>Label</key>
        <string>docs.wtfu.site</string>
        <key>ProgramArguments</key>
        <array>
            <string>sh</string>
            <string>-c</string>
            <string>cd ~/Desktop/knownledges/ &amp;&amp; docsify serve .</string>
        </array>
        <key>RunAtLoad</key>
        <true/>
        <key>EnvironmentVariables</key>
        <dict>
            <key>PATH</key>
            <string>/usr/local/bin</string>
            <!-- <string>/usr/local/bin:/usr/local/opt/node@18/bin</string> -->
        </dict>
        <key>StandardOutPath</key>
        <string>/tmp/docs.wtfu.site.log</string>
        <key>StandardErrorPath</key>
        <string>/tmp/docs.wtfu.site.err</string>
    </dict>
</plist>
```


## Reference
* [Mac OS 增加开机自启动脚本](https://www.xiaocaicai.com/2021/11/mac-os-%E5%A2%9E%E5%8A%A0%E5%BC%80%E6%9C%BA%E8%87%AA%E5%90%AF%E5%8A%A8%E8%84%9A%E6%9C%AC/)
* https://stackoverflow.com/questions/63169877/running-a-shell-script-automatically-with-launchd-on-mac
* https://stackoverflow.com/questions/15536697/running-python-script-with-launchd-imports-not-found
