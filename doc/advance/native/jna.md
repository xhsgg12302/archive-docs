## example
<!-- tabs:start -->
#### **C library**
##### 1. 导入需要的依赖包
```xml
<!-- https://mvnrepository.com/artifact/net.java.dev.jna/jna -->
<dependency>
    <groupId>net.java.dev.jna</groupId>
    <artifactId>jna</artifactId>
    <version>5.13.0</version>
</dependency>
```
##### 2. 编写代码
> [?] The following program loads the local C standard library implementation and uses it to call the printf function. </br>
Note: The following code is portable and works the same on Windows and POSIX (Linux / Unix / macOS) platforms.
```java
import com.sun.jna.Library;
import com.sun.jna.Native;
import com.sun.jna.Platform;

/** Simple example of native library declaration and usage. */
public class HelloWorld {
    public interface CLibrary extends Library {
        CLibrary INSTANCE = (CLibrary) Native.loadLibrary(
            (Platform.isWindows() ? "msvcrt" : "c"), CLibrary.class);
        void printf(String format, Object... args);
    }
    
    public static void main(String[] args) {
        CLibrary.INSTANCE.printf("Hello, World from JNA\n");
        for (int i = 0; i < args.length; i++) {
            CLibrary.INSTANCE.printf("Argument %d: %s\n", i, args[i]);
        }
    }
}
```

##### 3. 编译
```shell
$ javac -cp .:jna-5.13.0.jar HelloWorld.java
```

##### 4. 运行
```shell
$ java -cp .:jna-5.13.0.jar HelloWorld
```

##### 5. 记录
![](/.images/doc/advance/native/jna-origin-01.png)


#### **C dynamic Library**
##### 1. 编写HelloJNA.java
```java
import com.sun.jna.Library;
import com.sun.jna.Native;

public class HelloJNA {
    /**
     * 定义一个接口，默认的是继承Library ，如果动态链接库里的函数是以stdcall方式输出的，那么就继承StdCallLibrary
     * 这个接口对应一个动态链接文件
     */
    public interface LibraryAdd extends Library {
        // 可以使用相对路径进行加载。
        LibraryAdd LIBRARY_ADD = Native.load("hello", LibraryAdd.class);

        /**
         * 接口中只需要定义你要用到的函数或者公共变量，不需要的可以不定义
         * 映射libadd.so里面的函数，注意类型要匹配
         */
        int add(int a, int b);
    }

    public static void main(String[] args) {
        // 调用so映射的接口函数
        int add = LibraryAdd.LIBRARY_ADD.add(10, 15);
        System.out.println("result：" + add);
    }
}
```
> [?] 编译可以和第2步编译动态链接库并行，互不相关 </br>
`$ javac -cp .:jna-5.13.0.jar HelloJNA.java `

##### 2. 编写动态链接库C代码 hello.c
```c
int add(int a, int b){
    return a + b;
}
```
> [?] 使用`$ gcc  -dynamiclib -o libhello.dylib hello.c `编译成动态链接库 **libhello.dylib**

##### 3. 运行
> [?] `$ java -cp .:jna-5.13.0.jar HelloJNA`

##### 4. 记录
![](/.images/doc/advance/native/jna-library-01.png)

#### **C++ dynamic library**
##### Reference
* https://dreammind.hatenadiary.org/entry/20091225/1261690796

#### **hikvision**
##### Reference
* https://github.com/12302-anft/hikvision-api/blob/master/src/main/java/com/anft/framework/common/HikvisionAPI.java

<!-- tabs:end -->


## Reference
* https://en.wikipedia.org/wiki/Java_Native_Access
* https://github.com/java-native-access/jna
* https://github.com/mski/JNA-example
* https://dreammind.hatenadiary.org/entry/20091225/1261690796
* https://blog.csdn.net/zhan107876/article/details/121051129
* https://cloud.tencent.com/developer/article/1433681