## example
<!-- tabs:start -->
#### ** origin **
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
?> The following program loads the local C standard library implementation and uses it to call the printf function. </br>
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
![](/.images/doc/advance/jna/jna-origin-01.png)

#### ** hikvision **
<!-- tabs:end -->
## Reference
* https://en.wikipedia.org/wiki/Java_Native_Access
* https://github.com/java-native-access/jna