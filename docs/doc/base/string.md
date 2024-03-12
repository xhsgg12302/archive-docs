* ## Intro(String)

    + ### 内存布局

        ![](/.images/doc/base/string/string-memory-01.png ':size=99%')

    + ### intern()

        ?> 这个方法表示获取一个字符串对象的值在常量池中的引用。如果常量池中存在，则直接返回引用。如果不存在，JDK1.6的话，会copy String.value 到字符串常量池中，而包含及以上版本的话，会直接将String.value的引用放入到字符串常量池中。另外字符串常量池的实现是[C++ 中的hashtable](https://www.cnblogs.com/mic112/p/15520770.html#字符串常量池)。

        !> `String s = new String("ABC")` 这行单纯代码层面来说，只创建一个对象。如果包含类加载的过程，有可能会创建两个，一个在常量池中，一个在堆里面。堆里面的引用常量池中的value。[参考](https://stackoverflow.com/questions/19672427/string-s-new-stringxyz-how-many-objects-has-been-made-after-this-line-of)
        <br><br>`String.intern()` 方法首先会在常量池中查找等值字符串，找到了，返回引用。没找到，常量池创建并返回引用。
        <br>JDK1.6。不存在创建的是等值字符串，之后，创建的是堆中value的引用。[参考](https://blog.csdn.net/tyyking/article/details/82496901)

        ```java
        /**
         * Returns a canonical representation for the string object.
         * <p>
         * A pool of strings, initially empty, is maintained privately by the
         * class {@code String}.
         * <p>
         * When the intern method is invoked, if the pool already contains a
         * string equal to this {@code String} object as determined by
         * the {@link #equals(Object)} method, then the string from the pool is
         * returned. Otherwise, this {@code String} object is added to the
         * pool and a reference to this {@code String} object is returned.
         * <p>
         * It follows that for any two strings {@code s} and {@code t},
         * {@code s.intern() == t.intern()} is {@code true}
         * if and only if {@code s.equals(t)} is {@code true}.
         * <p>
         * All literal strings and string-valued constant expressions are
         * interned. String literals are defined in section 3.10.5 of the
         * <cite>The Java&trade; Language Specification</cite>.
         *
         * @return  a string that has the same contents as this string, but is
         *          guaranteed to be from a pool of unique strings.
         */
        public native String intern();
        ```

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### 测试
        <!-- tabs:start -->
        ##### **hello**
        ```java
        // 代码片段1
        String str2 = new String("str")+new String("01"); // 创建string对象 0x02912。
        str2.intern(); // jdk1.7及以下。 str2.intern()如果常量池中没有的话，会创建 str2 0x02912。 . 所以 str2.intern() == str2
        String str1 = "str01"; // 现在常量池中有了，直接返回引用 0x02912。
        System.out.println(str2==str1); // 所以相等

        // 代码片段2
        String str2 = new String("str")+new String("01"); // 创建string对象 0x02912。
        String str1 = "str01"; // 常量池中没有，创建"str01"字符串并返回引用 0xbc0cf
        str2.intern(); // 常量池中有字符串 ”str01" 所以返回引用 0xbc0cf .所以 str2.intern()== str01 == 0xbc0cf 
        System.out.println(str2==str1); // 不相等。因为str2 == 0x2912 ,str1 = oxbc0cf
        ```
        ##### **world**
        ```java
        public class Hello{}
        ```
        <!-- tabs:end -->
        <!-- panels:end -->

* ## Reference
    + https://drive.google.com/file/d/1YLB0u2DXSNAccpvdH_EZSwTTnYchHOKs/view?usp=sharing
    + https://www.cnblogs.com/mic112/p/15520770.html#字符串常量池
    + https://cloud.tencent.com/developer/article/2110482 常量池在运行区域的位置
    + https://www.cnblogs.com/ghj1976/p/5408295.html

        ?> select s from java.lang.String s where s.toString().contains("xhsgg1230")

    + https://example.com