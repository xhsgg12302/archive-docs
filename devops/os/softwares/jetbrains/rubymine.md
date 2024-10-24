* ## Intro(RubyMine)

    + ### SDK警告阻止⚠️

        > [?] IDE中使用了最新版的 ruby sdk(ruby-3.3.1-p55) 环境，但是由于项目中使用的配置`Gemfile.lock`中使用`BUNDLED WITH \n 2.1.4`版本的bundle，导致每次运行都会出现红色的警告信息。
        <br><br>根据 [issues-5234](https://github.com/rubygems/rubygems/issues/5234) 中提到的bundle版本2.3.4，使用命令`/usr/local/opt/ruby/bin/gem install bundler -v 2.3.4`安装，然后修改`Gemfile.lock`文件中的`BUNDLE WITH`版本即可解决。

        > [!CAUTION|label:备注指令] `/usr/local/opt/ruby/bin/gem pristine --all --no-extensions`

        ![](/.images/devops/os/softwares/rubymine-sdk-01.png ':size=29%')
        ![](/.images/devops/os/softwares/rubymine-sdk-02.png ':size=35%')
        ![](/.images/devops/os/softwares/rubymine-sdk-03.png ':size=35%')

* ## Reference
    + https://github.com/rubygems/rubygems/issues/5234
