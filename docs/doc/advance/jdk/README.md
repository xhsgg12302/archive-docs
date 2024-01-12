

## 查看native 对应的C/C++代码
```c
// The theory behind this is that all native methods should start with "Java_" and continue by the rest of package name.
// 形如：
Java_com_foobar_main_test(...);
// rapresents a method "test()" in packagename "com.foobar" and classfile "main". Overloaded methods could have their signature after the method name like:
Java_com_foobar_main_test__Ljava_lang_String_I(..., jstring text, jint integer);

// JDK native 方法位置
//       1.8 -> https://github.com/openjdk/jdk/blob/jdk8-b117/jdk/src/share/native/java/io/FileInputStream.c
//       21  -> src/java.base/share/native/libjava/FileInputStream.c
// for example
(java.io.FileInputStream#open0)
==> 
JNIEXPORT void JNICALL
Java_java_io_FileInputStream_open(JNIEnv *env, jobject this, jstring path) {
    fileOpen(env, this, path, fis_fd, O_RDONLY);
}
```

## Reference
* https://stackoverflow.com/questions/65513492/how-to-find-the-native-c-function-who-called-a-java-method-in-a-android-app-an