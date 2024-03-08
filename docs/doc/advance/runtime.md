* ## Intro(Runtime)

    ?> 一个java程序的运行全过程解析。

    <!-- panels:start -->
    <!-- div:left-panel-50 -->
    ```java
    package _jvm.classPrase;

    /**
     *
     * Copyright https://wtfu.site Inc. All Rights Reserved.
     *
     * @date 2024/3/8
     *                          @since  1.0
     *                          @author 12302
     *
     */
    public class Student extends Person implements IStudyable {
        private static int cnt=5;
        static{
            cnt++;
        }
        private String sid;
        public Student(int age, String name, String sid){
            super(age,name);
            this.sid = sid;
        }
        public void run(){
            System.out.println("run()...");
        }
        public int study(int a, int b){
            int c = 10;
            int d = 20;
            return a+b*c-d;
        }
        public static int getCnt(){
            return cnt;
        }
        public static void main(String[] args){
            Student s = new Student(23,"dqrcsc","20150723");
            s.study(5,6);
            Student.getCnt();
            s.run();
        }
    }
    class Person {
        private String name;
        private int age;
        public Person(int age, String name){
            this.age = age;
            this.name = name;
        }
        public void run(){}
    }
    interface IStudyable {
        public int study(int a, int b);
    }
    ```
    ```c
    Constant pool:
        #1 = Methodref          #14.#35        // _jvm/classPrase/Person."<init>":(ILjava/lang/String;)V
        #2 = Fieldref           #7.#36         // _jvm/classPrase/Student.sid:Ljava/lang/String;
        #3 = Fieldref           #37.#38        // java/lang/System.out:Ljava/io/PrintStream;
        #4 = String             #39            // run()...
        #5 = Methodref          #40.#41        // java/io/PrintStream.println:(Ljava/lang/String;)V
        #6 = Fieldref           #7.#42         // _jvm/classPrase/Student.cnt:I
        #7 = Class              #43            // _jvm/classPrase/Student
        #8 = String             #44            // dqrcsc
        #9 = String             #45            // 20150723
        #10 = Methodref          #7.#46         // _jvm/classPrase/Student."<init>":(ILjava/lang/String;Ljava/lang/String;)V
        #11 = Methodref          #7.#47         // _jvm/classPrase/Student.study:(II)I
        #12 = Methodref          #7.#48         // _jvm/classPrase/Student.getCnt:()I
        #13 = Methodref          #7.#49         // _jvm/classPrase/Student.run:()V
        #14 = Class              #50            // _jvm/classPrase/Person
        #15 = Class              #51            // _jvm/classPrase/IStudyable
        #16 = Utf8               cnt
        #17 = Utf8               I
        #18 = Utf8               sid
        #19 = Utf8               Ljava/lang/String;
        #20 = Utf8               <init>
        #21 = Utf8               (ILjava/lang/String;Ljava/lang/String;)V
        #22 = Utf8               Code
        #23 = Utf8               LineNumberTable
        #24 = Utf8               run
        #25 = Utf8               ()V
        #26 = Utf8               study
        #27 = Utf8               (II)I
        #28 = Utf8               getCnt
        #29 = Utf8               ()I
        #30 = Utf8               main
        #31 = Utf8               ([Ljava/lang/String;)V
        #32 = Utf8               <clinit>
        #33 = Utf8               SourceFile
        #34 = Utf8               Student.java
        #35 = NameAndType        #20:#52        // "<init>":(ILjava/lang/String;)V
        #36 = NameAndType        #18:#19        // sid:Ljava/lang/String;
        #37 = Class              #53            // java/lang/System
        #38 = NameAndType        #54:#55        // out:Ljava/io/PrintStream;
        #39 = Utf8               run()...
        #40 = Class              #56            // java/io/PrintStream
        #41 = NameAndType        #57:#58        // println:(Ljava/lang/String;)V
        #42 = NameAndType        #16:#17        // cnt:I
        #43 = Utf8               _jvm/classPrase/Student
        #44 = Utf8               dqrcsc
        #45 = Utf8               20150723
        #46 = NameAndType        #20:#21        // "<init>":(ILjava/lang/String;Ljava/lang/String;)V
        #47 = NameAndType        #26:#27        // study:(II)I
        #48 = NameAndType        #28:#29        // getCnt:()I
        #49 = NameAndType        #24:#25        // run:()V
        #50 = Utf8               _jvm/classPrase/Person
        #51 = Utf8               _jvm/classPrase/IStudyable
        #52 = Utf8               (ILjava/lang/String;)V
        #53 = Utf8               java/lang/System
        #54 = Utf8               out
        #55 = Utf8               Ljava/io/PrintStream;
        #56 = Utf8               java/io/PrintStream
        #57 = Utf8               println
        #58 = Utf8               (Ljava/lang/String;)V
    ```
    <!-- div:right-panel-50 -->

    !> 左上java源代码，左下常量池，右下为Code方法块。
    ```java
    {
        public _jvm.classPrase.Student(int, java.lang.String, java.lang.String);
            descriptor: (ILjava/lang/String;Ljava/lang/String;)V
            flags: ACC_PUBLIC
            Code:
            stack=3, locals=4, args_size=4
                0: aload_0
                1: iload_1
                2: aload_2
                3: invokespecial #1                  // Method _jvm/classPrase/Person."<init>":(ILjava/lang/String;)V
                6: aload_0
                7: aload_3
                8: putfield      #2                  // Field sid:Ljava/lang/String;
                11: return
            LineNumberTable:
                line 19: 0
                line 20: 6
                line 21: 11

        public void run();
            descriptor: ()V
            flags: ACC_PUBLIC
            Code:
            stack=2, locals=1, args_size=1
                0: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
                3: ldc           #4                  // String run()...
                5: invokevirtual #5                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
                8: return
            LineNumberTable:
                line 23: 0
                line 24: 8

        public int study(int, int);
            descriptor: (II)I
            flags: ACC_PUBLIC
            Code:
            stack=3, locals=5, args_size=3
                0: bipush        10
                2: istore_3
                3: bipush        20
                5: istore        4
                7: iload_1
                8: iload_2
                9: iload_3
                10: imul
                11: iadd
                12: iload         4
                14: isub
                15: ireturn
            LineNumberTable:
                line 26: 0
                line 27: 3
                line 28: 7

        public static int getCnt();
            descriptor: ()I
            flags: ACC_PUBLIC, ACC_STATIC
            Code:
            stack=1, locals=0, args_size=0
                0: getstatic     #6                  // Field cnt:I
                3: ireturn
            LineNumberTable:
                line 31: 0

        public static void main(java.lang.String[]);
            descriptor: ([Ljava/lang/String;)V
            flags: ACC_PUBLIC, ACC_STATIC
            Code:
            stack=5, locals=2, args_size=1
                0: new           #7                  // class _jvm/classPrase/Student
                3: dup
                4: bipush        23
                6: ldc           #8                  // String dqrcsc
                8: ldc           #9                  // String 20150723
                10: invokespecial #10                 // Method "<init>":(ILjava/lang/String;Ljava/lang/String;)V
                13: astore_1
                14: aload_1
                15: iconst_5
                16: bipush        6
                18: invokevirtual #11                 // Method study:(II)I
                21: pop
                22: invokestatic  #12                 // Method getCnt:()I
                25: pop
                26: aload_1
                27: invokevirtual #13                 // Method run:()V
                30: return
            LineNumberTable:
                line 34: 0
                line 35: 14
                line 36: 22
                line 37: 26
                line 38: 30

        static {};
            descriptor: ()V
            flags: ACC_STATIC
            Code:
            stack=2, locals=0, args_size=0
                0: iconst_5
                1: putstatic     #6                  // Field cnt:I
                4: getstatic     #6                  // Field cnt:I
                7: iconst_1
                8: iadd
                9: putstatic     #6                  // Field cnt:I
                12: return
            LineNumberTable:
                line 13: 0
                line 15: 4
                line 16: 12
    }
    ```
    <!-- panels:end -->

    + ### 虚拟机栈

        <!-- panels:start -->
        <!-- div:left-panel -->
        ?> JVM中通过java栈，保存方法调用运行的相关信息，每当调用一个方法，会根据该方法的在字节码中的信息为该方法创建栈帧，不同的方法，其栈帧的大小有所不同。栈帧中的内存空间还可以分为3块，分别存放不同的数据：
        <br><br>1). `局部变量表`：存放该方法调用者所传入的参数，及在该方法的方法体中创建的局部变量。
        <br>2). `操作数栈`：用于存放操作数及计算的中间结果等。
        <br>3). `其他栈帧信息`：如返回地址、当前方法的引用等。
        <br><br>只有当前正在运行的方法的栈帧位于栈顶，当前方法返回，则当前方法对应的栈帧出栈，当前方法的调用者的栈帧变为栈顶；当前方法的方法体中若是调用了其他方法，则为被调用的方法创建栈帧，并将其压入栈顶。
        <br>___注意：局部变量表及操作数栈的最大深度在编译期间就已经确定了，存储在该方法字节码的Code属性中。___

        <!-- div:right-panel -->
        ![](/.images/doc/advance/runtime/runtime-stack-struction-01.png ':size=98%')
        <!-- panels:end -->

        - #### LocalVariableTable

            !> [局部变量表](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html#jvms-4.7.14)：在编译期间可以加`-g`参数用来生成调试信息。Code块可选的属性。可以用来debug,调试器可以使用它来确定方法执行期间给定局部变量的值。
            <!-- panels:start -->
            <!-- div:left-panel-50 -->
            ```java
            public int study(int a, int b){
                int c = 10;
                int d = 20;
                return a+b*c-d;
            }
            ```
            ?> 对于下列LocalVariableTable属性的解释：
            <br>`Start`:code块有值时候的始偏移量，
            <br>`Length`:code块有值时候的长度，
            <br>`Slot`:存放变量的槽位。
            <br><br>官方说有值: ***Each entry in the local_variable_type_table array indicates a range of code array offsets within which a local variable has a value. It also indicates the index into the local variable array of the current frame at which that local variable can be found***. 有些地方说"[可见](https://stackoverflow.com/questions/30104521/what-do-start-and-length-attribute-in-localvariabletable-mean)",一个意思。
            <br><br>比如：
            <br>1). `this,a,b`变量在code块中一直(0->16)有值。因为调用栈传入slot中的。
            <br>2). 而`c`变量直到在 ***2: istore_3*** 存入第三个slot中时才有值，也就是在(3->16)位置有值。
            <br>3). 同理`d`变量在 ***5: istore 4*** 存入第四个slot中时才有值，也就是在(7->16)位置有值。
            <!-- div:right-panel-50 -->
            ```java
            // 字节码节选
            public int study(int, int);
                descriptor: (II)I
                flags: ACC_PUBLIC
                Code:
                stack=3, locals=5, args_size=3
                    0: bipush        10
                    2: istore_3
                    3: bipush        20
                    5: istore        4
                    7: iload_1
                    8: iload_2
                    9: iload_3
                    10: imul
                    11: iadd
                    12: iload         4
                    14: isub
                    15: ireturn
                LineNumberTable:
                    line 26: 0
                    line 27: 3
                    line 28: 7
                LocalVariableTable:
                    Start  Length  Slot  Name   Signature
                        0      16     0  this   L_jvm/classPrase/Student;
                        0      16     1     a   I
                        0      16     2     b   I
                        3      13     3     c   I
                        7       9     4     d   I
            ```
            <!-- panels:end -->
        
    + ### 运行过程

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### into-main()
        ?> main方法的运行过程
        <br><br> stack 指定当前方法即main()方法对应栈帧中的操作数栈的最大深度，当前值为5
        <br> locals 指定main()方法中局部变量表的大小，当前为2，及有两个slot用于存放方法的参数及局部变量。
        <br> code length 指定main()方法中代码的长度，当前为31。
        <!-- div:left-panel -->
        ```java
        public static void main(String[] args){
            Student s = new Student(23,"dqrcsc","20150723");
            s.study(5,6);
            Student.getCnt();
            s.run();
        }
        ```
        ![](/.images/doc/advance/runtime/runtime-stack-main-01.png ':size=77%')
        <!-- div:right-panel -->
        ```java
        public static void main(java.lang.String[]);
            descriptor: ([Ljava/lang/String;)V
            flags: ACC_PUBLIC, ACC_STATIC
            Code:
            stack=5, locals=2, args_size=1
                0: new           #7                  // class _jvm/classPrase/Student
                3: dup
                4: bipush        23
                6: ldc           #8                  // String dqrcsc
                8: ldc           #9                  // String 20150723
                10: invokespecial #10                 // Method "<init>":(ILjava/lang/String;Ljava/lang/String;)V
                13: astore_1
                14: aload_1
                15: iconst_5
                16: bipush        6
                18: invokevirtual #11                 // Method study:(II)I
                21: pop
                22: invokestatic  #12                 // Method getCnt:()I
                25: pop
                26: aload_1
                27: invokevirtual #13                 // Method run:()V
                30: return
            LineNumberTable:
                line 34: 0
                line 35: 14
                line 36: 22
                line 37: 26
                line 38: 30
        ```
        <!-- panels:end -->

        ?> [`new #7`](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.new)指令：在java堆中创建一个Student对象，并将其引用值放入栈顶。
        <br><br>[`dup`](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.dup)：复制栈顶的值，然后将复制的结果入栈。
        <br>`bipush 23`：将单字节常量值23入栈。
        <br>`ldc #8`：将#8这个常量池中的常量即”dqrcsc”取出，并入栈。
        <br>`ldc #9`：将#9这个常量池中的常量即”20150723”取出，并入栈。

        ![](/.images/doc/advance/runtime/runtime-stack-main-02.png ':size=49%')
        ![](/.images/doc/advance/runtime/runtime-stack-main-03.png ':size=49%')

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### into-Student.construct()
        ?> [`invokespecial #10`](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.invokespecial)：调用#10这个常量所代表的方法，即Student.\<init\>()这个方法

        !> \<init\>()方法，是编译器将调用父类的\<init\>()的语句、构造代码块、实例字段赋值语句，以及自己编写的构造方法中的语句整合在一起生成的一个方法。保证调用父类的\<init\>()方法在最开头，自己编写的构造方法语句在最后，而构造代码块及实例字段赋值语句按出现的顺序按序整合到\<init\>()方法中。
        <br><br>注意到Student.\<init\>()方法的最大操作数栈深度为3，局部变量表大小为4。 从dup到ldc #9这四条指令向栈中添加了4个数据，虽然定义中只显式地定义了传入3个参数，而实际上会隐含传入一个当前对象的引用作为第一个参数，所以四个参数依次为this，age，name，sid。`参数传递`后调用了Student.\<init\>()方法，会创建该方法的栈帧，并入栈。栈帧中的局部变量表的第0到4个slot分别保存着入栈的那四个参数值。

        <!-- div:left-panel-61 -->
        ```java
        public Student(int age, String name, String sid){
            super(age,name);
            this.sid = sid;
        }
        ```
        ![](/.images/doc/advance/runtime/runtime-stack-main-04.png ':size=99%')
        <!-- div:right-panel-39 -->
        ```java
        public Person(int age, String name){
            this.age = age;
            this.name = name;
        }
        ```
        ```java
        public _jvm.classPrase.Student(int, java.lang.String, java.lang.String);
            descriptor: (ILjava/lang/String;Ljava/lang/String;)V
            flags: ACC_PUBLIC
            Code:
            stack=3, locals=4, args_size=4
                0: aload_0
                1: iload_1
                2: aload_2
                3: invokespecial #1                  // Method _jvm/classPrase/Person."<init>":(ILjava/lang/String;)V
                6: aload_0
                7: aload_3
                8: putfield      #2                  // Field sid:Ljava/lang/String;
                11: return
            LineNumberTable:
                line 19: 0
                line 20: 6
                line 21: 11
        ```
        <!-- panels:end -->

        ?> `aload_0`：将局部变量表slot0处的引用值入栈
        <br>`iload_1`：将局部变量表slot1处的int值入栈
        <br>`aload_2`：将局部变量表slot2处的引用值入栈
        <br><br>`invokespecial #1`：调用Person.\<init\>()方法，同调用Student.\<init\>过程类似，创建栈帧，将三个参数的值存放到局部变量表等，从Person.<init>()返回之后，用于传参的栈顶的3个值被回收了。
        <br>`aload_0`：将slot0处的引用值入栈。
        <br>`aload_3`：将slot3处的引用值入栈。
        <br><br>`putfield #2`：将当前栈顶的值”20150723”赋值给0x2222所引用对象的sid字段，然后栈中的两个值出栈。
        <br>`return`：返回调用方，即main()方法，当前方法栈帧出栈。

        ![](/.images/doc/advance/runtime/runtime-stack-main-05.png ':size=49%')
        ![](/.images/doc/advance/runtime/runtime-stack-main-06.png ':size=49%')

        ?> 重新回到main()方法中，继续执行下面的字节码指令：
        <br>`astore_1`：将当前栈顶引用类型的值赋值给slot1处的局部变量，然后出栈。
        <br>`aload_1`：slot1处的引用类型的值入栈。
        <br>`iconst_5`：将常数5入栈，int型常数只有0-5有对应的iconst_x指令。
        <br>`bipush 6`：将常数6入栈。

        ![](/.images/doc/advance/runtime/runtime-stack-main-02.png ':size=49%')
        ![](/.images/doc/advance/runtime/runtime-stack-main-07.png ':size=49%')

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### into-s.study()
        ?> [`invokevirtual #11`](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.invokevirtual)：调用虚方法study()，这个方法是重写的接口中的方法，需要动态分派，所以使用了invokevirtual指令。
        <br><br>最大栈深度3，局部变量表5。
        <!-- div:left-panel-45 -->
        ```java
        public int study(int a, int b){
            int c = 10;
            int d = 20;
            return a+b*c-d;
        }
        ```
        ![](/.images/doc/advance/runtime/runtime-stack-main-08.png ':size=99%')
        <!-- div:right-panel-55 -->
        ```java
        public int study(int, int);
            descriptor: (II)I
            flags: ACC_PUBLIC
            Code:
            stack=3, locals=5, args_size=3
                0: bipush        10
                2: istore_3
                3: bipush        20
                5: istore        4
                7: iload_1
                8: iload_2
                9: iload_3
                10: imul
                11: iadd
                12: iload         4
                14: isub
                15: ireturn
            LineNumberTable:
                line 26: 0
                line 27: 3
                line 28: 7
        ```
        <!-- panels:end -->

        ?> `bipush 10`：将10入栈
        <br>`istore_3`：将栈顶的10赋值给slot3处的int局部变量，即c，出栈。
        <br>`bipush 20`：将20入栈
        <br>`istore 4`：将栈顶的20付给slot4处的int局部变量，即d，出栈。
        <br>上面4条指令，完成对c和d的赋值工作。
        <br>`iload_1、iload_2、iload_3`这三条指令将slot1、slot2、slot3这三个局部变量入栈：
        <br><br>`imul`：将栈顶的两个值出栈，相乘的结果入栈：
        <br><br>`iadd`：将当前栈顶的两个值出栈，相加的结果入栈
        <br>`iload 4`：将slot4处的int型的局部变量入
        <br><br>`isub`：将栈顶两个值出栈，相减结果入栈：
        <br>`ireturn`：将当前栈顶的值返回到调用方。

        ![](/.images/doc/advance/runtime/runtime-stack-main-09.png ':size=49%')
        ![](/.images/doc/advance/runtime/runtime-stack-main-10.png ':size=49%')
        ![](/.images/doc/advance/runtime/runtime-stack-main-11.png ':size=49%')
        ![](/.images/doc/advance/runtime/runtime-stack-main-12.png ':size=49%')

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### into-Student.getCnt()-and-s.run()
        ?> 重新回到main()方法中：
        <br>`pop`指令，将study()方法的返回值出栈
        <br>`invokestatic #12` 调用静态方法getCnt()不需要传任何参数
        <br>`pop`：getCnt()方法有返回值，将其出栈
        <br>`aload_1`：将slot1处的引用值入栈
        <br>`invokevirtual #13`：调用0x2222对象的run()方法，重写自父类的方法，需要动态分派，所以使用invokevirtual指令
        <br>`return`：main()返回，程序运行结束。

        ![](/.images/doc/advance/runtime/runtime-stack-main-13.png ':size=49%')
        <!-- panels:end -->


* ## Reference

    + https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html
    + https://www.cnblogs.com/dqrcsc/p/4671879.html
    + 
    + https://drive.google.com/file/d/1-s_kefzJN_gHcZ8w-3zIQ0oxLQQlnuni/view?usp=sharing