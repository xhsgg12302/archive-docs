* ## Intro(Mac OSX)

    ### zsh终端输出多一个'%'
    ?> [参考](https://unix.stackexchange.com/questions/167582/why-zsh-ends-a-line-with-a-highlighted-percent-symbol/)：在`~/.zshrc`配置文件中追加`export PROMPT_EOL_MARK=`控制结尾变量。

    ### macOS_14_Sonoma_关闭输入法图标提示
    ?> [参考](https://discussionschinese.apple.com/thread/255178136?sortBy=best)：终端输入`sudo defaults write /Library/Preferences/FeatureFlags/Domain/UIKit.plist redesigned_text_cursor -dict-add Enabled -bool YES `

    ### [command+Shift+F]呼出访达聚焦搜索

    ![](/.images/devops/os/mac/macosx-keyboard-conflict-01.png ':size=60%')

* ## Reference

    * https://unix.stackexchange.com/questions/167582/why-zsh-ends-a-line-with-a-highlighted-percent-symbol/