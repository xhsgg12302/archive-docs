* ## launchctl

    > [!] 简单解释，launchd是一套统一的开源服务管理框架，它用于启动、停止以及管理后台程序、应用程序、进程和脚本。launchd是macOS第一个启动的进程，该进程的PID为1，整个系统的其他进程都是它创建的。
    <br><br>`1).` 当launchd启动后，它会扫描`/System/Library/LaunchDaemons`和`/Library/LaunchDaemons`中的plist文件并加载他们；
    <br>`2).` 当输入密码登录系统后，launchd会扫描`/System/Library/LaunchdAgents`、`/Library/LaunchAgents`、`~/Library/LaunchAgents`这三个目录中的plist文件并加载它们。每个plist文件都是一个任务，加载不代表立即运行，只有设置了**RunAtLoad为true或keepAlive为true**时，才会加载并同时启动这些任务。

    |name | ope|
    -|-
    |帮助|`launchctl help`|
    |列出所有由launchd管理的进程 | `launchctl list` |
    |加载 | `launchctl load ~/Library/LaunchAgents/docs.wtfu.site.plist`|
    |卸载 | `launchctl unload ~/Library/LaunchAgents/docs.wtfu.site.plist`|
    |启动 (luanchctl start `<Label>`)| `launchctl start dos.wtfu.site`|
    |关闭 | `launchctl stop dos.wtfu.site`|

    - ### restart

        > [?] 摘自 [How to start/stop/restart launchd services from the command line?](https://serverfault.com/questions/194832/how-to-start-stop-restart-launchd-services-from-the-command-line)
        <br>Just in case if you are looking for launchctl reload, you can define shell function in your ~/.bashrc/.zshrc as I did:
        <br>Command execution looks like -> `lctl reload <your-plist-name>.plist`

        ```shell
        function lctl {
            COMMAND=$1
            PLIST_FILE=$2
            if [ "$COMMAND" = "reload" ] && [ -n "$PLIST_FILE" ]
            then
                echo "reloading ${PLIST_FILE}.."
                launchctl unload ${PLIST_FILE}
                launchctl load ${PLIST_FILE}
            else
                echo "either command not specified or plist file is not defined"
            fi
        }
        ```

    - ### plist

        <!-- tabs:start -->
        #### **docs.wtfu.site**
        path: `~/Library/LaunchAgents/docs.wtfu.site.plist`
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

        #### **tech.wtfu.site**
        path: `~/Library/LaunchAgents/tech.wtfu.site.plist`
        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
        <plist version="1.0">
            <dict>
                <key>Label</key>
                <string>tech.wtfu.site</string>
                <key>ProgramArguments</key>
                <array>
                    <string>sh</string>
                    <string>-c</string>
                    <string>cd ~/Desktop/archive/archive-hugo &amp;&amp; rm -rf public &amp;&amp; hugo server -D </string>
                </array>
                <key>RunAtLoad</key>
                <true/>
                <key>EnvironmentVariables</key>
                <dict>
                    <key>PATH</key>
                    <string>~/.go/bin:/bin:/usr/local/bin</string>
                    <!-- <string>/usr/local/bin:/usr/local/opt/node@18/bin</string> -->
                </dict>
                <key>StandardOutPath</key>
                <string>/tmp/tech.wtfu.site.log</string>
                <key>StandardErrorPath</key>
                <string>/tmp/tech.wtfu.site.err</string>
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
                    <string>~/Desktop/frp_0.34.1_darwin_amd64/frpc -c ~/Desktop/frp_0.34.1_darwin_amd64/frpc.ini </string>
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
        </plist>
        ```

        #### **jetbrains.vmoptions.plist**
        path: `~/Library/LaunchAgents/jetbrains.vmoptions.plist`
        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
        <plist version="1.0">
            <dict>
                <key>Label</key>
                <string>jetbrains.vmoptions</string>
                <key>ProgramArguments</key>
                <array>
                    <string>sh</string>
                    <string>-c</string>
                    <string>
                    launchctl setenv "IDEA_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/idea.vmoptions"
                    launchctl setenv "CLION_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/clion.vmoptions"
                    launchctl setenv "PHPSTORM_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/phpstorm.vmoptions"
                    launchctl setenv "GOLAND_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/goland.vmoptions"
                    launchctl setenv "PYCHARM_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/pycharm.vmoptions"
                    launchctl setenv "WEBSTORM_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/webstorm.vmoptions"
                    launchctl setenv "WEBIDE_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/webide.vmoptions"
                    launchctl setenv "RIDER_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/rider.vmoptions"
                    launchctl setenv "DATAGRIP_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/datagrip.vmoptions"
                    launchctl setenv "RUBYMINE_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/rubymine.vmoptions"
                    launchctl setenv "DATASPELL_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/dataspell.vmoptions"
                    launchctl setenv "AQUA_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/aqua.vmoptions"
                    launchctl setenv "RUSTROVER_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/rustrover.vmoptions"
                    launchctl setenv "GATEWAY_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/gateway.vmoptions"
                    launchctl setenv "JETBRAINS_CLIENT_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/jetbrains_client.vmoptions"
                    launchctl setenv "JETBRAINSCLIENT_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/jetbrainsclient.vmoptions"
                    launchctl setenv "STUDIO_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/studio.vmoptions"
                    launchctl setenv "DEVECOSTUDIO_VM_OPTIONS" "/Users/stevenobelia/Downloads/install-package/jetbra/vmoptions/devecostudio.vmoptions"
                    </string>
                </array>
                <key>RunAtLoad</key>
                <true/>
                <key>StandardOutPath</key>
                <string>/tmp/jetbrains.vmoptions.log</string>
                <key>StandardErrorPath</key>
                <string>/tmp/jetbrains.vmoptions.err</string>
            </dict>
        </plist>
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

* ## reload

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

* ## Reference

    + [mac上如何开机与关机时自动运行shell脚本](https://namiling.github.io/2020-11-24-mac上如何开机与关机时自动运行shell脚本/)
    + [Mac OS 增加开机自启动脚本](https://www.xiaocaicai.com/2021/11/mac-os-%E5%A2%9E%E5%8A%A0%E5%BC%80%E6%9C%BA%E8%87%AA%E5%90%AF%E5%8A%A8%E8%84%9A%E6%9C%AC/)
    + https://stackoverflow.com/questions/63169877/running-a-shell-script-automatically-with-launchd-on-mac
    + https://stackoverflow.com/questions/15536697/running-python-script-with-launchd-imports-not-found
    + https://blog.csdn.net/dddgggd/article/details/122599616