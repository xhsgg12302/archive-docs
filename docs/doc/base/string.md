* ## Intro(String)

    + ### 内存布局

        ![](/.images/doc/base/string/string-memory-01.png ':size=37%')
        ![](/.images/doc/base/string/string-memory-02.png ':size=62%')

    + ### intern()

        ?> `String.intern()`方法表示获取一个字符串对象的值在常量池中的引用。如果常量池中存在，则直接返回引用。如果不存在，常量池创建并返回引用。
        <br><br>不同jdk版本创建并返回引用是有区别的：
        <br>JDK1.6，不存在则在**永久代字符串常量池**创建`等值字符串`，
        <br>JDK1.6之后，在**堆区字符串常量池**创建的是`堆中String obj的引用`。[参考1](https://stackoverflow.com/questions/27812666/why-string-intern-behave-differently-in-oracle-jdk-1-7)，[参考2](https://blog.csdn.net/tyyking/article/details/82496901)
        <br><br>另外字符串常量池的实现是[C++ 中的hashtable](https://www.cnblogs.com/mic112/p/15520770.html#字符串常量池)。

        !> `String s = new String("ABC")` 这行单纯代码层面来说，只创建一个对象。如果包含类加载的过程，有可能会创建两个，一个在常量池中，一个在堆里面。堆里面的引用常量池中的value。[参考](https://stackoverflow.com/questions/19672427/string-s-new-stringxyz-how-many-objects-has-been-made-after-this-line-of)
        <br><br>不要使用JUnit之类的工具测试intern方法，有可能会有误差，[详细解释查看case3](#case3)
        
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
        ##### **case1**
        ```java
        // source: https://blog.csdn.net/tyyking/article/details/82496901
        String str2 = new String("str") + new String("01");
        str2.intern(); 
        String str1 = "str01";
        System.out.println(str2 == str1);

        // 上述代码在JDK1.6及以后版本表现不同。假设str2的地址为`String@725`
        // 1.6  调用`str2.intern()`会在永久代字符串常量池中复制等值字符串String@825，然后返回引用String@825。给str1赋值的时候发现字符串常量池有'str01'，则将引用String@825给str1。
        // >1.6 调用`str2.intern()`会在堆区字符串常量池中放置对象str2的引用String@725，然后返回引用String@725。给str1赋值的时候发现字符串常量池有'str01'，则将引用String@725给str1。
        ```
        ![](/.images/doc/base/string/string-intern-01.png ':size=99%')
        ![](/.images/doc/base/string/string-intern-02.png ':size=99%')
        ##### **case2**
        ```java
        String s1 = "a";
        String s2 = "b";
        String s3 = "ab";
        String s4 = s1 + s2;
        boolean flag = s3 == s4;
        ```
        ![](/.images/doc/base/string/string-intern-03.png ':size=99%')
        ##### **case3**
        ?> 下面的代码在main方法以及JUnit中运行的效果不同。
        <br><br>[解释](https://blog.csdn.net/tyyking/article/details/82496901#comments_31667673)：
        <br>此处的不正常现象在于JUnit会加载一些其他类，这些类里面定义了一些需要加载到字符串常量池(SCP)的字面量。而且JVM的SCP仅有一个，所以出现这种现象。
        <br>现象分析：
        <br>如果在当前方法执行之前JUnit相关类已经加载`"11"`到字SCP。`String s3 = new String("1") + new String("1");` 在堆中创建了一个新的对象（包括字符串对象中的value引用也是最新的）。但是String s4引用的是SCP中存在的，这两个对象互不相关。*【不像main方法执行"s3.intern()"的时候,SCP 里面没对应的值，所以将s3的引用放在SCP里面,定义s4的时候自然将SCP中的那一个s3引用给s4，也就是s4 == s3】* 。当然，除了"11"字符串外，还有如下可以验证的值`"10","775","813","923","CN","星期日","大正","昭和","巴基斯坦卢比","java.home"`，只需要将上述字符串拆解即可验真。

        !> 注意：main方法测试的时候根据原理，发现一个`"java.home"`字符串，也会出现不一致的情况。应该还有其他类似的。

        ```java
        String s3 = new String("1") + new String("1");
        s3.intern();
        String s4 = "11";
        System.out.println(s3 == s4);
        ```
        ![](/.images/doc/base/string/string-intern-04.png ':size=49%')
        ![](/.images/doc/base/string/string-intern-05.png ':size=49%')
        <!-- tabs:end -->
        <!-- panels:end -->

* ## Reference
    + https://stackoverflow.com/questions/27812666/why-string-intern-behave-differently-in-oracle-jdk-1-7
    + https://stackoverflow.com/questions/19672427/string-s-new-stringxyz-how-many-objects-has-been-made-after-this-line-of
    + https://blog.csdn.net/tyyking/article/details/82496901
    + https://www.cnblogs.com/mic112/p/15520770.html#字符串常量池
    + 
    + https://drive.google.com/file/d/1YLB0u2DXSNAccpvdH_EZSwTTnYchHOKs/view?usp=sharing