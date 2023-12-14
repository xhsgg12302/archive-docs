## 编译JDK
```shell
# 下载xcodeapp(~7G), OS version [13.3.1 (22E261)]
wget -c https://download.developer.apple.com/Developer_Tools/Xcode_14.3.1/Xcode_14.3.1.xip
# 切换xcode 到xcodeapp
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
sudo xcodebuild -license

# 配置
# disable-warnings-as-errors选项是禁止把warning 当成error
# --with-debug-level=slowdebug。用来设置编译的级别，可选值为release、fastdebug、slowde-bug，越往后进行的优化措施就越少，带的调试信息就越多。默认值为release。slowdebug 含有最丰富的调试信息，没有这些信息，很多执行可能被优化掉，我们单步执行时，可能看不到一些变量的值。所以最好指定slowdebug 为编译级别。
# with-jvm-variants 编译特定模式的HotSpot虚拟机，可选值：server、client、minimal、core、zero、custom
# configure 命令承担了依赖项检查、参数配置和构建输出目录结构等多项职责，如果编译过程中需要的工具链或者依赖项有缺失，命令执行后会得到明确的提示，并给出该依赖的安装命令。
bash configure --disable-warnings-as-errors --with-debug-level=slowdebug --with-jvm-variants=server
# 编译
make images

# 生成compile_commands.json文件
make compile-commands
```

## Reference
* https://github.com/openjdk/jdk/blob/master/doc/building.md
* https://segmentfault.com/a/1190000040305260
* https://www.jetbrains.com/help/clion/compilation-database.html
* https://segmentfault.com/a/1190000041074433?utm_source=sf-backlinks
* https://blog.csdn.net/qq_25825005/article/details/127163309