* ## Intro(JDK)

    + ## 下载JDK

        * ### reference

            > [?] ~special：macosx[Sonoma]安装特殊版本[1.6.0_65](https://updates.cdn-apple.com/2019/cert/041-88384-20191011-3d8da658-dca4-4a5b-b67c-26e686876403/JavaForOSX.dmg)，oracle没有提供~ 。
            <br>上面的没有源码，[参考](https://stackoverflow.com/questions/4011002/java-eclipse-on-macosx-where-is-the-src-zip) 改用[`Java for OS X 2013-005 Developer Package`](https://developer.apple.com/download/all/?q=Java%20for%20OS%20X%202013-005%20Developer%20Package)，对应的JAVA版本为 **1.6.0_65-b14-462.jdk** 。

            > [!] Please be aware that IntelliJ IDEA 2022.3 won’t support running and testing Java applications that use Java 6. Java 6 reached “end of life” status several years ago
            <br>对于JDK1.6，可以使用[`IDEA 2022.2`](https://download-cdn.jetbrains.com/idea/ideaIU-2022.2.5.dmg)，[参考](https://intellij-support.jetbrains.com/hc/en-us/community/posts/14203092325906-Which-version-of-Intellij-IDEA-works-with-Java-6-and-how-i-get-it)

            + https://mirrors.huaweicloud.com/java/jdk/
            + https://mirrors.huaweicloud.com/openjdk/
            + https://jdk.java.net/
            + https://www.oracle.com/java/technologies/downloads/archive/

    * ## 查看JDK详细信息包括版本，厂商

        > [?] `java -XshowSettings:properties -version`

    * ## 查看native 对应的C/C++代码
    
        ```c {16,31}
        // The theory behind this is that all native methods should start with "Java_" and continue by the rest of package name.
        // 形如：
        Java_com_foobar_main_test(...);
        // rapresents a method "test()" in packagename "com.foobar" and classfile "main". Overloaded methods could have their signature after the method name like:
        Java_com_foobar_main_test__Ljava_lang_String_I(..., jstring text, jint integer);

        // JDK native 方法位置(不同jdk方法名可能不一样，比如: (java.io.FileInputStream#open | open0)
        // for example
        // JDK 1.7 -> 

                |: https://github.com/openjdk/jdk/blob/jdk7-b80/jdk/src/share/classes/java/io/FileInputStream.java#L186
                /**
                * Opens the specified file for reading.
                * @param name the name of the file
                */
                private native void open(String name) throws FileNotFoundException;
            ==> 
                |: https://github.com/openjdk/jdk/blob/jdk7-b80/jdk/src/share/native/java/io/FileInputStream.c#L60C1-L60C34
                JNIEXPORT void JNICALL
                Java_java_io_FileInputStream_open(JNIEnv *env, jobject this, jstring path) {
                    fileOpen(env, this, path, fis_fd, O_RDONLY);
                }

        // JDK 23  -> 

                |: https://github.com/openjdk/jdk/blob/jdk-23%2B6/src/java.base/share/classes/java/io/FileInputStream.java#L203
                /**
                * Opens the specified file for reading.
                * @param name the name of the file
                */
                private native void open0(String name) throws FileNotFoundException;
            ==>
                |: https://github.com/openjdk/jdk/blob/jdk-23%2B6/src/java.base/share/native/libjava/FileInputStream.c#L60C1-L60C35
                JNIEXPORT void JNICALL
                Java_java_io_FileInputStream_open0(JNIEnv *env, jobject this, jstring path) {
                    fileOpen(env, this, path, fis_fd, O_RDONLY);
                }
        ```

## Reference
* https://stackoverflow.com/questions/65513492/how-to-find-the-native-c-function-who-called-a-java-method-in-a-android-app-an
* https://hackernoon.com/oracle-ibm-or-open-jdk-how-to-know-java-vendor-details