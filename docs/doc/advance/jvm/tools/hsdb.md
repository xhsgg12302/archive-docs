* ## Intro(HSDB)

    !> `-XX:-UseCompressedOops`oops: ordinary object pointer，普通对象指针压缩，例如Object o = new Object();其中o就是个指向new Object()对象的指针，o在指针压缩前占用8个字节，在指针压缩后占用4个字节。
    <br>`-XX:-UseCompressedClassPointers`压缩Klass Pointer，压缩前8个字节，压缩后4个字节。

    ?> `sudo java -cp $JAVA_HOME/lib/sa-jdi.jar sun.jvm.hotspot.HSDB`

* ## Reference
    - https://github.com/openjdk/jdk/blob/jdk8-b120/hotspot/agent/src/share/classes/sun/jvm/hotspot/HSDB.java
    - https://openjdk.org/groups/hotspot/docs/Serviceability.html#bsa
    - https://docs.oracle.com/en/java/javase/11/tools/jhsdb.html#GUID-0345CAEB-71CE-4D71-97FE-AA53A4AB028E
    - 
    - https://github.com/openjdk/jdk/tree/jdk8-b120/hotspot/agent/doc #下面站点可以渲染里面的html.
        1. https://htmlpreview.github.io/?https://github.com/openjdk/jdk/blob/jdk8-b120/hotspot/agent/doc/index.html
        2. https://svn.netlabs.org/repos/java/trunk/openjdk/hotspot/agent/doc/index.html