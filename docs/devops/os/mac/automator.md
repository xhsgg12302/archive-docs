## Automator

## DEMO
<!-- tabs:start -->
#### **close-viscosity**
```shell
# 在 macOS 上实现到某个时间点自动关闭某个软件，可以使用 AppleScript 脚本结合定时任务来实现。以下是一种可能的实现方式：
# 1.打开 "Script Editor" 应用程序：在 Launchpad 或者应用程序文件夹中找到 "Script Editor" 应用程序并打开。
# 2.编写 AppleScript 脚本：在 "Script Editor" 中编写一个 AppleScript 脚本，用于关闭指定的软件。例如，假设要关闭 Safari 浏览器，脚本内容如下：
tell application "Safari"
    quit
end tell
# 3.保存脚本：将脚本保存为可执行文件。选择 "File" -> "Export..."，选择 "File Format" 为 "Application"，然后保存到你喜欢的位置，例如桌面上。
# 4.创建定时任务：打开 "Automator" 应用程序，选择 "New Document"，然后选择 "Calendar Alarm"。
# 5.添加操作步骤：在右侧的操作库中，搜索并添加 "Run AppleScript" 操作步骤。
# 6.编写 AppleScript 代码：在 "Run AppleScript" 步骤中，将以下代码替换为你保存的脚本文件的路径：
do shell script "open /path/to/your/script.app"
# 7.保存这个automator.设定保存选项
```
![](/.images/devops/os/mac/automator-quit-viscosity-01.png)


#### **ommited**
```shell
hello world
```
<!-- tabs:end -->

## Reference
* https://www.jianshu.com/p/d306f1b703c2