

## 查看native 对应的C/C++代码
```c
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