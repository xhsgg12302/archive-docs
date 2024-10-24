## example
<!-- tabs:start -->
#### ** Accessing Native APIS **
##### 1. 编写NativeLibrary.h
```cpp
#include <string>

namespace NativeLibrary {
    class NativeClass {
        public:
            const std::string& get_property() { return property; }
            void set_property(const std::string& property) { this->property = property; }
            std::string property;
    };
}
```
##### 2. 编写NativeLibrary.java
```java
import org.bytedeco.javacpp.*;
import org.bytedeco.javacpp.annotation.*;

@Platform(include="NativeLibrary.h")
@Namespace("NativeLibrary")
public class NativeLibrary {
    public static class NativeClass extends Pointer {
        static { Loader.load(); }
        public NativeClass() { allocate(); }
        private native void allocate();

        // to call the getter and setter functions 
        public native @StdString String get_property(); public native void set_property(String property);

        // to access the member variable directly
        public native @StdString String property();     public native void property(String property);
    }

    public static void main(String[] args) {
        // Pointer objects allocated in Java get deallocated once they become unreachable,
        // but C++ destructors can still be called in a timely fashion with Pointer.deallocate()
        NativeClass l = new NativeClass();
        l.set_property("Hello World!");
        System.out.println(l.property());
    }
}
```

##### 3. 执行
```shell
$ java -jar javacpp-1.5.9.jar NativeLibrary.java -exec
```

##### 4. 记录
![](/.images/doc/advance/native/javacpp-nl-01.png)


<!-- tabs:end -->

## Reference
* https://github.com/bytedeco/javacpp