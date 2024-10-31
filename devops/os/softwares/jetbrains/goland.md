* ## Intro(GoLand)

    + ### 断点不生效

        > [?] GoLand(2023.2.6) 中使用了最新版的 go(go1.23.2 darwin/amd64) 环境，但是由于工具自带的插件中的 dlv 版本为 1.21.0 ，导致 debug 的时候出现下述错误，以致断点不生效:
        <br>**WARNING: undefined behavior - version of Delve is too old for Go version 1.23.2 (maximum supported version 1.21)** 。
        <br><br>根据类似问题 [Issues-GO-14287](https://youtrack.jetbrains.com/issue/GO-14287/undefined-behavior-version-of-Delve-is-too-old-for-Go-version-1.20.0-maximum-supported-version-1.19#focus=Comments-27-6915232.0-0) 中提到修复步骤，使用如下命令进行解决：
        <br><span style='padding-left:2.3em'/>Install dlv binary with `go install github.com/go-delve/delve/cmd/dlv@latest`
        <br><span style='padding-left:2.3em'/>Set the `dlv.path=<path_to_dlv_executable>` under Help > Edit Custom Properties, 比如：`dlv.path=/Users/stevenobelia/.go/bin/dlv`
        <br><span style='padding-left:2.3em'/>Restart GoLand

        > [!CAUTION|label:备注信息] `export GOPATH=/Users/stevenobelia/.go` 
        <br>设置了**GOPTAH**环境变量后，dlv二进制文件会存放在`$GOPATH/bin`里面。
        <br><br>`/Users/stevenobelia/.go/bin/dlv version` 查看刚安装的 dlv 版本
        <br>`/Applications/GoLand.app/Contents/plugins/go-plugin/lib/dlv/mac/dlv version` 查看 GoLand 自带的 dlv 版本
        <br><br>![](/.images/devops/os/softwares/goland-dlv-version-01.png ':size=50%')

        ![](/.images/devops/os/softwares/goland-debug-01.png ':size=49%')
        ![](/.images/devops/os/softwares/goland-debug-02.png ':size=49%')

* ## Reference
    + https://youtrack.jetbrains.com/issue/GO-14287/undefined-behavior-version-of-Delve-is-too-old-for-Go-version-1.20.0-maximum-supported-version-1.19
