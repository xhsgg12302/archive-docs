+ ## launchctl

    !> 简单解释，launchd是一套统一的开源服务管理框架，它用于启动、停止以及管理后台程序、应用程序、进程和脚本。launchd是macOS第一个启动的进程，该进程的PID为1，整个系统的其他进程都是它创建的。当launchd启动后，它会扫描/System/Library/LaunchDaemons和/Library/LaunchDaemons中的plist文件并加载他们；当输入密码登录系统后，launchd会扫描/System/Library/LaunchdAgents、/Library/LaunchAgents、~/Library/LaunchAgents这三个目录中的plist文件并加载它们。每个plist文件都是一个任务，加载不代表立即运行，只有设置了RunAtLoad为true或keepAlive为true时，才会加载并同时启动这些任务。

    |name | ope|
    -|-
    |帮助|`launchctl help`|
    |列出所有由launchd管理的进程 | `launchctl list` |
    |加载 | `launchctl load ~/Library/LaunchAgents/dos.wtfu.site.plist`|
    |卸载 | `launchctl unload ~/Library/LaunchAgents/dos.wtfu.site.plist`|
    |启动 | `launchctl start dos.wtfu.site`|
    |关闭 | `launchctl stop dos.wtfu.site`|

    - ### plist

        <!-- tabs:start -->
        #### **docs.wtfu.site**
        path: `~/Library/LaunchAgents/dos.wtfu.site.plist`
        ```xml
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

        #### **frp.wtfu.site**
        path: `~/Library/LaunchAgents/frp.wtfu.site.plist`
        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
        <plist version="1.0">
            <dict>
                <key>Label</key>
                <string>frpc.wtfu.site</string>
                <key>ProgramArguments</key>
                <array>
                    <string>sh</string>
                    <string>-c</string>
                    <string>/Users/mac/Desktop/frp_0.34.1_darwin_amd64/frpc -c /Users/mac/Desktop/frp_0.34.1_darwin_amd64/frpc.ini </string>
                </array>
                <key>RunAtLoad</key>
                <true/>
                <key>EnvironmentVariables</key>
                <dict>
                    <key>PATH</key>
                    <string>/usr/local/bin:/usr/local/opt/node@18/bin</string>
                </dict>
                <key>StandardOutPath</key>
                <string>/tmp/frpc.wtfu.site.log</string>
                <key>StandardErrorPath</key>
                <string>/tmp/frpc.wtfu.site.err</string>
            </dict>
        </plist
        ```

        #### **ssh.plist**
        path: `/System/Library/LaunchDaemons/ssh.plist`
        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
        <plist version="1.0">
            <dict>
                <key>Disabled</key>
                <true/>
                <key>Label</key>
                <string>com.openssh.sshd</string>
                <key>Program</key>
                <string>/usr/libexec/sshd-keygen-wrapper</string>
                <key>ProgramArguments</key>
                <array>
                    <string>sshd-keygen-wrapper</string>
                </array>
                <key>Sockets</key>
                <dict>
                    <key>Listeners</key>
                    <dict>
                        <key>SockServiceName</key>
                        <string>ssh</string>
                        <key>Bonjour</key>
                        <array>
                            <string>ssh</string>
                            <string>sftp-ssh</string>
                        </array>
                    </dict>
                </dict>
                <key>inetdCompatibility</key>
                <dict>
                    <key>Wait</key>
                    <false/>
                    <key>Instances</key>
                    <integer>42</integer>
                </dict>
                <key>StandardErrorPath</key>
                <string>/dev/null</string>
                <key>SHAuthorizationRight</key>
                <string>system.preferences</string>
                <key>POSIXSpawnType</key>
                <string>Interactive</string>
                <key>MaterializeDatalessFiles</key>
                <true/>
            </dict>
        </plist>
        ```

        - #### Reference
            * https://serverfault.com/questions/18761/how-to-change-sshd-port-on-mac-os-x
            * https://apple.stackexchange.com/questions/395508/can-i-mount-the-root-system-filesystem-as-writable-in-big-sur
            * https://www.jianshu.com/p/406a4d06b788
        <!-- tabs:end -->

## reload
```shell
alias reload='source ~/.zshrc \!:1'

# Path to your oh-my-zsh installation.
export ZSH="/Users/stevenobelia/.oh-my-zsh"
export RANGER_LOAD_DEFAULT_RC=FALSE

if [ $# -eq 2 ]; then
	ZSH_THEME=$2
else
	ZSH_THEME="random"
fi
plugins=(git)

source $ZSH/oh-my-zsh.sh
source ~/.bash_profile
source /usr/local/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
export PATH="/usr/local/opt/ruby/bin:$PATH"
#export PATH="/usr/local/lib/ruby/gems/3.1.0/bin:$PATH"
___MY_VMOPTIONS_SHELL_FILE="${HOME}/.jetbrains.vmoptions.sh"; if [ -f "${___MY_VMOPTIONS_SHELL_FILE}" ]; then . "${___MY_VMOPTIONS_SHELL_FILE}"; fi
```

## Reference
* [Mac OS 增加开机自启动脚本](https://www.xiaocaicai.com/2021/11/mac-os-%E5%A2%9E%E5%8A%A0%E5%BC%80%E6%9C%BA%E8%87%AA%E5%90%AF%E5%8A%A8%E8%84%9A%E6%9C%AC/)
* https://stackoverflow.com/questions/63169877/running-a-shell-script-automatically-with-launchd-on-mac
* https://stackoverflow.com/questions/15536697/running-python-script-with-launchd-imports-not-found
* https://blog.csdn.net/dddgggd/article/details/122599616