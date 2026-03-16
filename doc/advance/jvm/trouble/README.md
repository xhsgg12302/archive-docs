---
---

* ## Can't attach symbolicator to the process

    > [?] 在 MacOSX 平台上，JDK 为 1.8，自己编写 agent 诊断 JVM 的情况下，如果给了 sudo 权限还会出现 
    <br>Error attaching to process: sun.jvm.hotspot.debugger.DebuggerException: Can't attach symbolicator to the process
    <br>sun.jvm.hotspot.debugger.DebuggerException: sun.jvm.hotspot.debugger.DebuggerException: Can't attach symbolicator to the process
    <br>这种问题，可以尝试在代码中添加 `System.loadLibrary("awt");`，看问题是否可以解决。
    <br><br>我本地的情况就是这样，但是发现另外一个工具`sun.jvm.hotspot.HSDB`添加 sudo 之后就可以正常工作了，于是找到代码，逻辑比对，发现在 HotSpotAgent attach 之前有这样的代码`SwingUtilities.invokeLater`，一路尝试追踪到 java.awt.Toolkit#getDefaultToolkit 中的代码`String nm = System.getProperty("awt.toolkit");`,然后使用 Class.forName(nm)加载了这个类。发现最终起作用的是[System.loadLibrary("awt");](https://github.com/openjdk/jdk/blob/8d11b977def8356b6fd8504562538d2af88c082b/src/java.desktop/macosx/classes/sun/lwawt/macosx/LWCToolkit.java#L167)，因为动态库的原因，继续不下去了...，也没能找到真正原因。但是可以解决问题。如下演示：
    
    ![](/.images/doc/advance/jvm/trouble/trouble-readme-01.gif)
