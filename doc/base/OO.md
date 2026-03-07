---
title: OBJECT ORIENTED
weight: 10
---
* ## 对象结构

    ![](/.images/doc/base/object-header-01.png ':size=60%')

    + ### 对象头

        - #### Mark Word

            > [?] Mark Word(标记字)，用于存储自身运行时的数据 例如`GC标志位`、`哈希码`、`锁状态`等信息。Mark Word 的位长度为 JVM 的一个 Word 大小，也就是说 32 位 JVM 的 Mark word 为 32 位，64 位 JVM 为 64 位。Mark Word 的位长度不会受到 Oop 对象指针压缩选项的影响。

            > [!] 结构信息：
            <br>Java 内置锁的涉及到很多重要信息，并且是存放于对象头的 Mark Word 字段中。
            <br><br>Java 内置锁的状态总共有四种，级别由低到高依次为:无锁、偏向锁、轻量级锁、重量级锁。为了让 Mark word 字段存储更多的信 息，JVM 将 Mark word 的最低两个位设置为 Java 内置锁状态位。
            <br><br>其实在 JDK 1.6 之前，Java 内置锁还是一个重量级锁，是一个效率比较低下的锁，在 JDK 1.6 之 后，JVM 为了提高锁的获取与释放效率，对 synchronized 的实现进行了优化，引入了偏向锁、轻量级锁的实现，从此以后 Java 内置锁的状态就有了四种(无锁、偏向锁、轻量级锁、重量级锁)， 并且四种状态会随着竞争的情况逐渐升级，而且是不可逆的过程，即不可降级，也就是说只能进 行锁升级(从低级别到高级别)。

            ![](/.images/doc/base/object-header-02.png ':size=99%')

        - #### Class Pointer

            > [?] Class Pointer(类对象指针)，用于存放方法区 Class 对象的地址，虚拟机通 过这个指针来确定这个对象是哪个类的实例。Class Pointer(类对象指针)字段的长度也为 JVM 的一个 Word(字)大小，即 32 位的 JVM为32位，64位的JVM为64位。

            > [!] 对于对象指针而言，如果 JVM 中对象数量过多，使用 64 位的指针将浪费大量内存，通过简单统计，64 位的 JVM 将会比 32 位的 JVM 多耗费 50%的内存。为了节约内存可以使用选项`+UseCompressedOops`开启指针压缩。选项 UseCompressedOops 中的 Oop 部分为 Ordinary object pointer 普通对象指针的缩写。
            <br><br>如果开启 UseCompressedOops 选项，以下类型的指针将从 64 位压缩至 32 位: 
            <br>①Class 对象的属性指针(即静态变量)；②Object 对象的属性指针(即成员变量)；③普通对象数组的元素指针。
            <br><br>当然，也不是所有的指针都会压缩，一些特殊类型的指针不会压缩，比如指向 PermGen(永 久代)的 Class 对象指针(JDK8 中指向元空间的 Class 对象指针)、本地变量、堆栈元素、入参、返 回值和 NULL 指针等。

        - #### Array Length(Optional)

            > [?] Array Length(数组长度)。如果对象是一个 Java 数组，那么此字段必须有， 用于记录数组长度的数据;如果对象不是一个 Java 数组，那么此字段不存在，所以这是一个可选 字段。Array Length 字段的长度也随着 JVM 架构的不同而不同:32 位的 JVM 上，长度为 32 位; 64 位 JVM 则为 64 位。

            > [!] 64 位 JVM 如果开启了 Oop 对象的指针压缩，Array Length 字段的长度也 将由64位压缩至32位。

    + ### 对象体

        > [?] 对象体包含了对象的实例变量(成员变量)。用于成员属性值，包括父类的成员属性值。这 部分内存按 4 字节对齐。
    
    + ### 对齐字节

        > [?] 对齐字节也叫做填充对齐，其作用是用来保证 Java 对象在所占内存字节数为 8 的倍数(8N bytes)。HotSpot VM 的内存管理要求对象起始地址必须是 8 字节的整数倍。对象头本身是 8 的 倍数，当对象的实例变量数据不是 8 的倍数，便需要填充数据来保证 8 字节的对齐。

## 封装

## 继承

## 多态

* ## 语法

    + ### 修饰符

        > [?] 修饰符分为`访问修饰符`、`非访问修饰符`。修饰符用来定义类、方法或者变量，通常放在语句的最前端。

        - #### 访问修饰符

            |    | 当前类 | 同一包 | 子类中 | 其他地方 |
            | -- | :-:    | :-:    | :-:   | :-: |
            | `public`    | ✅ | ✅ | ✅ | ✅ |
            | `protected` | ✅ | ✅ | ✅ | ❌ | 
            | `default`   | ✅ | ✅ | ❌ | ❌ |
            | `private`   | ✅ | ❌ | ❌ | ❌ | 

            * ##### protected详解

                > [?] 画出关系图后，从被可见的角度去看，就是：“从当前类(包括)到定义方法的继承路径上的所有类，以及定义方法类同包”。比如：
                <br><span style='padding-left:2.3em'>1、对于 Son01#f()，就是 Son01，Fahter，Son03，Test01 中可见。
                <br><span style='padding-left:2.3em'>2、对于 Son01#clone()，就是 Son01，Fahter，Object 及 Object所处同包中可见。
                <br><span style='padding-left:2.3em'>3、对于 Son02#f()，就是 Son02，Son01，Test02 中可见。
                <br><br>用到的代码样例 [参见](https://github.com/12302-bak/idea-test-project/tree/modifier/_0_base-learning/src/main/java/_base/modifier/intro)

                ![](/.images/doc/base/oo/modifier/modifier-protected-intro-01.png ':size=99%')

                <!-- tabs:start -->
                ###### **Father**
                ```java {12}
                public void testWithFather() throws CloneNotSupportedException {

                    Father father = new Father();
                    father.f();
                    father.clone();

                    Son01 son01 = new Son01();
                    son01.f();
                    son01.clone();

                    Son02 son02 = new Son02();
                    son02.f();                      // compile error
                    son02.clone();

                    Son03 son03 = new Son03();
                    son03.f();
                    son03.clone();

                    Son04 son04 = new Son04();
                    son04.f();
                    son04.clone();

                    // Specifically for the scenario of multiple inheritance
                    GrandChild0401 grandChild0401 = new GrandChild0401();
                    grandChild0401.f();
                    grandChild0401.clone();
                }
                ```
                ###### **Son01**
                ```java {4-5,13,16-17,20-21,25-26}
                public void testWithSon01() throws CloneNotSupportedException {

                    Father father = new Father();
                    father.f();                     // compile error
                    father.clone();                 // compile error

                    Son01 son01 = new Son01();
                    son01.f();
                    son01.clone();

                    Son02 son02 = new Son02();
                    son02.f();
                    son02.clone();                  // compile error

                    Son03 son03 = new Son03();
                    son03.f();                      // compile error
                    son03.clone();                  // compile error

                    Son04 son04 = new Son04();
                    son04.f();                      // compile error
                    son04.clone();                  // compile error

                    // Specifically for the scenario of multiple inheritance
                    GrandChild0401 grandChild0401 = new GrandChild0401();
                    grandChild0401.f();             // compile error
                    grandChild0401.clone();         // compile error
                }
                ```
                ###### **Son02**
                ```java {4-5,8-9,16-17,20-21,25-26}
                public void testWithSon02() throws CloneNotSupportedException {

                    Father father = new Father();
                    father.f();                     // compile error
                    father.clone();                 // compile error

                    Son01 son01 = new Son01();
                    son01.f();                      // compile error
                    son01.clone();                  // compile error

                    Son02 son02 = new Son02();
                    son02.f();
                    son02.clone();

                    Son03 son03 = new Son03();
                    son03.f();                      // compile error
                    son03.clone();                  // compile error

                    Son04 son04 = new Son04();
                    son04.f();                      // compile error
                    son04.clone();                  // compile error

                    // Specifically for the scenario of multiple inheritance
                    GrandChild0401 grandChild0401 = new GrandChild0401();
                    grandChild0401.f();             // compile error
                    grandChild0401.clone();         // compile error
                }
                ```
                ###### **Son03**
                ```java {5,9,12-13,21,26}
                public void testWithSon03() throws CloneNotSupportedException {

                    Father father = new Father();
                    father.f();
                    father.clone();                 // compile error

                    Son01 son01 = new Son01();
                    son01.f();
                    son01.clone();                  // compile error

                    Son02 son02 = new Son02();
                    son02.f();                      // compile error
                    son02.clone();                  // compile error

                    Son03 son03 = new Son03();
                    son03.f();
                    son03.clone();

                    Son04 son04 = new Son04();
                    son04.f();
                    son04.clone();                  // compile error

                    // Specifically for the scenario of multiple inheritance
                    GrandChild0401 grandChild0401 = new GrandChild0401();
                    grandChild0401.f();
                    grandChild0401.clone();         // compile error
                }
                ```
                ###### **Son04**
                ```java {4-5,8-9,12-13,16-17}
                public void testWithSon04() throws CloneNotSupportedException {

                    Father father = new Father();
                    father.f();                     // compile error
                    father.clone();                 // compile error

                    Son01 son01 = new Son01();
                    son01.f();                      // compile error
                    son01.clone();                  // compile error

                    Son02 son02 = new Son02();
                    son02.f();                      // compile error
                    son02.clone();                  // compile error

                    Son03 son03 = new Son03();
                    son03.f();                      // compile error
                    son03.clone();                  // compile error

                    Son04 son04 = new Son04();
                    son04.f();
                    son04.clone();

                    // Specifically for the scenario of multiple inheritance
                    GrandChild0401 grandChild0401 = new GrandChild0401();
                    grandChild0401.f();
                    grandChild0401.clone();
                }
                ```
                ###### **GrandChild0401**
                ```java {4-5,8-9,12-13,16-17,20-21}
                public void testWithGrandChild0401() throws CloneNotSupportedException {

                    Father father = new Father();
                    father.f();                     // compile error
                    father.clone();                 // compile error

                    Son01 son01 = new Son01();
                    son01.f();                      // compile error
                    son01.clone();                  // compile error

                    Son02 son02 = new Son02();
                    son02.f();                      // compile error
                    son02.clone();                  // compile error

                    Son03 son03 = new Son03();
                    son03.f();                      // compile error
                    son03.clone();                  // compile error

                    Son04 son04 = new Son04();
                    son04.f();                      // compile error
                    son04.clone();                  // compile error

                    // Specifically for the scenario of multiple inheritance
                    GrandChild0401 grandChild0401 = new GrandChild0401();
                    grandChild0401.f();
                    grandChild0401.clone();
                }
                ```
                ###### **Test01**
                ```java {5,9,12-13,17,21,26}
                public void testWithTest01() throws CloneNotSupportedException {

                    Father father = new Father();
                    father.f();
                    father.clone();                 // compile error

                    Son01 son01 = new Son01();
                    son01.f();
                    son01.clone();                  // compile error

                    Son02 son02 = new Son02();
                    son02.f();                      // compile error
                    son02.clone();                  // compile error

                    Son03 son03 = new Son03();
                    son03.f();
                    son03.clone();                  // compile error

                    Son04 son04 = new Son04();
                    son04.f();
                    son04.clone();                  // compile error

                    // Specifically for the scenario of multiple inheritance
                    GrandChild0401 grandChild0401 = new GrandChild0401();
                    grandChild0401.f();
                    grandChild0401.clone();         // compile error
                }
                ```
                ###### **Test02**
                ```java {4-5,8-9,13,16-17,20-21,25-26}
                public void testWithTest02() throws CloneNotSupportedException {

                    Father father = new Father();
                    father.f();                     // compile error
                    father.clone();                 // compile error

                    Son01 son01 = new Son01();
                    son01.f();                      // compile error
                    son01.clone();                  // compile error

                    Son02 son02 = new Son02();
                    son02.f();
                    son02.clone();                  // compile error

                    Son03 son03 = new Son03();
                    son03.f();                      // compile error
                    son03.clone();                  // compile error

                    Son04 son04 = new Son04();
                    son04.f();                      // compile error
                    son04.clone();                  // compile error

                    // Specifically for the scenario of multiple inheritance
                    GrandChild0401 grandChild0401 = new GrandChild0401();
                    grandChild0401.f();             // compile error
                    grandChild0401.clone();         // compile error
                }
                ```
                <!-- tabs:end -->

                <details><summary>旧备份，有部分表述错误</summary>

                > [?] 简单介绍：`protected` 表示 `同包 + 继承`。
                <br><span style='padding-left: 2.9em'/>继承可以理解为将 protected 方法的可见性传递到子类中。比如 case02 中 `son2.f()、this.f()`可以访问。
                <br><br>`a).` 在同包中
                <br><span style='padding-left: 2.9em'/>访问方法`f()`的类与方法`f()`在同一个包中。
                <br><span style='padding-left: 2.9em'/>例如 **case01#Test00#son2.f()** 其中 son2.f() 方法继承自 Father1，定义在 p0 中。
                <br><span style='padding-left: 2.9em'/>例如 **case01#Test01#son1.f()** 访问的方法的类 Test01 与重写的方法 f() 都在 p1 包中。
                <br><span style='padding-left: 2.9em'/>例如 **case03#Father#test01()** Son1 中的 f() 在 同包 p0 中，而 Son2 中的方法 f() 在 p2 中
                <br><br>`b).` 在子类中
                <br><span style='padding-left: 2.9em'/>只能访问自己继承或重写的方法`this|super.f()`，<span style='color:blue'>~不能访问父类实例或其他后代实例的方法`new Father1().f() 、 new OtherSon().f()`~。</span>
                <br><span style='padding-left: 2.9em'/>例如 **case02#Son2** 只能访问自己 Son2实例 中的 f() 方法(可能是继承或者重写而来)，而不能访问父类实例或者其他子类实例中的方法。
                <br><br>参考：[Java protected 关键字详解](https://blog.csdn.net/justloveyou_/article/details/61672133)

                <!-- tabs:start -->
                ###### **case01**
                ![](/.images/doc/base/oo/modifier/modifier-protected-01.png ':size=99%')
                ###### **case02**
                ![](/.images/doc/base/oo/modifier/modifier-protected-02.png ':size=99%')
                ###### **case03**
                ![](/.images/doc/base/oo/modifier/modifier-protected-03.png ':size=99%')
                <!-- tabs:end -->
                </details>

## Reference
* https://github.com/openjdk/jdk/blob/jdk8-b120/hotspot/src/share/vm/oops/markOop.hpp
* https://github.com/openjdk/jol
* https://juejin.cn/post/6993308982081224711
* 
* https://drive.google.com/file/d/17IVppRV74_wf1k5No8Us7Qhy75o6-SME/view?usp=sharing
