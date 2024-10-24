## Intro

> [!] Gradle是一个开源的自动构建工具，它在设计之初就是为了能够灵活地构建几乎所有类型的应用。以下是他的一些重要特性：
1. **高性能**：Gradle使用了缓存机制来加快构建
2. **基于JVM**：Gradle是基于JVM的工具，这对于熟悉Java的开发者来说是一件好事，因为你可以使用标准的Java APIs 在你的构建逻辑中，比如自定义的task或插件。这同时也使得Gradle可以轻易地实现跨平台。
3. **声明式（Conventions）**：Gradle汲取了Maven的长处，以Java项目为例，只要将合适的文件放在合适的地方，应用合适的插件就可以简单地执行构建
4. **扩展性**：Gradle具有良好的可扩展性，你可以通过自定义`task types`或甚至自定义`build model`来拓展Gradle。例如Android的构建工具就引入了许多新的构建概念例如`flavours和build types`.
5. **IDE支持**，几个主流的IDE都支持导入Gradle构建并且通过图形化界面的方式与Gradle进行交互。

## 查看gradle具体执行参数

> [?] 将GradleHome 下bin 中的gradle最后一行修改为`echo "$JAVACMD" "$@"`,将完整打印执行命令。

## IDEA 配置截图
![](/.images/devops/build/gradle/usage/gradle-usage-01.png ':size=80%')

## gradle单独设置caches目录

## Reference
* https://blog.csdn.net/qq_16386581/article/details/86982278
* https://cloud.tencent.com/developer/article/1818133
* https://cloud.tencent.com/developer/article/2106431