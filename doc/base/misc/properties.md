* ## Intro(PROPERTIES)

    + ### System.properties()

        > [?] 在`System`类中有 **initProperties(Properties props);** native 方法。在C代码[`GetJavaProperties(env);`](https://github.com/openjdk/jdk/blob/jdk8-b120/jdk/src/share/native/java/lang/System.c#L172)中会获取到一些props。这个方法的实现在[java_props_md.c](https://github.com/openjdk/jdk/blob/jdk8-b120/jdk/src/solaris/native/java/lang/java_props_md.c#L427)中。在 626 行`setProxyProperties(&sprops);` 会进行系统代理的设置。继续调用到[java_props_maxosx.c](https://github.com/openjdk/jdk/blob/jdk8-b120/jdk/src/solaris/native/java/lang/java_props_macosx.c#L290)中。主要会使用 macosx SDK 中的 [`SCDynamicStoreCopyProxies(_:)`](https://developer.apple.com/documentation/systemconfiguration/1517088-scdynamicstorecopyproxies)[【函数调用 C Demo】](https://github.com/12302-bak/practise-demo/blob/master/main.c#L14)， 获取当前系统中的网络代理设置。<span style='color: red'>需要注意与环境变量进行区分。</span>

    + ### Prop对unicode编码的处理

        > [?] 在查看 **Spring国际化(i18n)** 和 **ResourceBundle** 相关的代码的时候，发现都会用到`Properties`类相关的处理。而且对于 **unicode** 的处理都是由这个类去自动处理的。所以跟踪了相关代码，对这部分做一个记录。
        <br><br>大概的原理是：
        <br>`1).` 在 [java.util.Properties#load0](https://github.com/openjdk/jdk/blob/jdk8-b120/jdk/src/share/classes/java/util/Properties.java#L344) 读取每一行，通过`=`、`:` 分割每一个key和value。在最后对key和value分别进行转换。
        <br>`2).` 调用 [java.util.Properties#loadConvert](https://github.com/openjdk/jdk/blob/jdk8-b120/jdk/src/share/classes/java/util/Properties.java#L538C20-L538C31) 进行转换操作。转换过程：碰见`\u`将后面的四位使用算法转换成十进制数字，当成一个char类型的字符。在原来的数组上面进行覆盖。

        **相关算法如下图：**

        > [!CAUTION] 比如将`\u4f60`转成字符char`20320`. 过程如下：
        <br><br>第一个字符`'4'`，进到循环后，执行`(value << 4) + aChar - '0';` 此时 value = 0，左移四位还是0 ，再加上字符`'4'`表示的整型值 **4** (正好是当前字符`'4'`与`'0'`字符的差值) 最后value = `0100`;
        <br>第二个字符`'f'`，进来后执行`(value << 4) + 10 + aChar - 'a';` 此时将上一步得到的value 左移四位，得到`0100 0000` 然后与实际值(`'f'`表示的整型值 **15** `1111`)想加，即得到`0100 1111`。
        <br>依此类推：最终得到的value = `0100 1111 0110 0000` = `20320`。

        ![](/.images/doc/base/misc/properties/properties-unicode-01.png ':size=100%')

* ## Reference