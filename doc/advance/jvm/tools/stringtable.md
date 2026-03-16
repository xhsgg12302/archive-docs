---
---

* ## Intro(StringTable | 打印 JVM StringTable)

    > [?] 将虚拟机中的 StringTable 里面的字符全部打印出来，有两种方式，1：通过[jcmd](https://docs.oracle.com/javase/9/tools/jcmd.htm#:~:text=VM.stringtable) 命令，2：借助 sun 下 hotspot 中的相关工具类编码。

    + ### jcmd 方式

        > [!NOTE] jcmd 里面有个命令是`VM.stringtable`，可以查看 VM 里面的 stringtable，jdk 9以下好像没有这个命令[参考](https://stackoverflow.com/questions/64458776/why-can-some-jcmd-commandseg-vm-set-flag-can-not-be-used-on-all-pid)，会抛出：”java.lang.IllegalArgumentException: Unknown diagnostic command“错误，所以可以使用 jdk9 去诊断。

        ```java
        package _base.str.test;

        public class StringTest {
            public static void main(String[] args) {
                String str2 = new String("str")+new String("01"); // 创建string对象 0x02912。
                str2.intern(); // jdk1.7及以下。 str2.intern()如果常量池中没有的话，会创建 str2 0x02912。 . 所以 str2.intern() == str2
                String str1 = "str01"; // 现在常量池中有了，直接返回引用 0x02912。
                System.out.println(str2==str1); // 所以相等

                String str3 = new String("hel") + new String("lo");

                String str4 = new String("he") + new String("llo");

                String str3Tmp = str3.intern();
                String str4Tmp = str4.intern();
                System.out.println(str3.equals(str4));

                keepRunning();
                System.out.println();
            }

            public static void keepRunning(){
                Thread thread = Thread.currentThread();
                synchronized (thread){
                    try {
                        thread.wait();
                    } catch (InterruptedException e) { e.printStackTrace(); }
                }
            }
        }
        ```

        可以使用 idea 中的 JDK 1.8 环境编译，然后运行使用 jdk9。
        <br>再运行：`/Library/Java/JavaVirtualMachines/jdk-9.0.4.jdk/Contents/Home/bin/java -cp /Users/stevenobelia/Documents/project_idea_test/idea-test-project/_0_base-learning/target/classes _base.str.test.StringTest`

        查看当前pid 为 36522，然后是用 JDK1.8 中的 jcmd 也可以查看 `jcmd 36522 VM.stringtable -verbose`。

        ![](/.images/doc/advance/jvm/stringtable/st-with-jcmd-01.png ':size=99%')

    + ### 自己编码

        > [!NOTE] 在 idea 中搜索 StringTable 的时候发现了处于`sun.jvm.hotspot.memory`中的这个类，大概查看了一下，基本可以满足，然后结合`sun.jvm.hotspot.tools.Tool`工具，attach 到目标虚拟机上，获取相关信息。编码如下：

        ```java
        package _base.str.test;

        import sun.jvm.hotspot.memory.StringTable;
        import sun.jvm.hotspot.oops.Oop;
        import sun.jvm.hotspot.runtime.VM;
        import sun.jvm.hotspot.tools.Tool;

        public class PrintStringTable extends Tool {

            public static void main(String args[]) throws Exception {
                if(args.length != 1) {
                    System.err.println("need pid");
                    System.exit(1);
                }
                PrintStringTable pst = new PrintStringTable();
                pst.execute(args);
                pst.stop();
            }

            @Override
            public void run() {
                StringTable table = VM.getVM().getStringTable();
                table.stringsDo(Oop::printValue);
            }
        }
        ```

        不过在本地 MacOSX 上运行的时候，会出现如下错误：<br>ERROR: attach: task_for_pid(13871) failed: '(os/kern) failure' (5) <br>Error attaching to process: sun.jvm.hotspot.debugger.DebuggerException: Can't attach to the process. Could be caused by an incorrect pid or lack of privileges.<br>sun.jvm.hotspot.debugger.DebuggerException: sun.jvm.hotspot.debugger.DebuggerException: Can't attach to the process. Could be caused by an incorrect pid or lack of privileges。
        <br><br>现在可以添加 sudo 权限去应对这个，但也有可能出现其他的问题，比如[Can’t attach symbolicator to the process](../trouble/README.md#cant-attach-symbolicator-to-the-process)。貌似是 MacOS 内核的安全策略问题。
        <br><br>暂时先选择切换平台（ubuntu 22.04, java-8-openjdk-amd64[1.8.0_482]）。另外还需要注意可能会出现”Metadata does not appear to be polymorphic“之类的错误。
        <br>解决方案[参考](https://stackoverflow.com/questions/33733445/java-heap-dump-error-metadata-does-not-appear-to-be-polymorphic):`sudo apt-get install openjdk-8-dbg`

        ![](/.images/doc/advance/jvm/stringtable/st-with-sun-code-01.png ':size=99%')

* ## Reference

    - https://0equalsto1.medium.com/internal-working-of-java-string-pool-interning-37cc892ae739
    - https://github.com/puneetlakhina/javautils/blob/master/src/com/blogspot/sahyog/PrintStringTable.java
    - https://stackoverflow.com/questions/33733445/java-heap-dump-error-metadata-does-not-appear-to-be-polymorphic # "Metadata does not appear to be polymorphic" 
    - https://stackoverflow.com/questions/64458776/why-can-some-jcmd-commandseg-vm-set-flag-can-not-be-used-on-all-pid
