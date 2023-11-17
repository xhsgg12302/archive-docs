## example

<!-- tabs:start -->
#### ** C **
1. 写一段包含C代码的java代码
```java
// Save as HelloJNI.java
public class HelloJNI{
    static {
        // Load native library hello.dll (Windows) or libhello.so (Unixes) at runtime
        // This library contains a native method called sayHello()
        System.loadLibrary("hello");
    }
    // Declare an instance native method sayHello() which receives no parameter and returns void
    private native void sayHello();
    public static void main(String[] args){
        // Create an instance and invoke the native method
        new HelloJNI().sayHello();
    }
}
```
2. 

#### ** C++ **
```c++
```
<!-- tabs:end -->



## Reference
* https://www3.ntu.edu.sg/home/ehchua/programming/java/javanativeinterface.html