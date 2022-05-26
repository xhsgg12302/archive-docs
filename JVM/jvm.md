## 内存模型

- [方法区](https://baijiahao.baidu.com/s?id=1731211048001613394&wfr=spider&for=pc)

  > JDK1.7及之前的实现叫做永久代，包括类信息，常量，静态变量以及【类常量池，字符串常量池，运行时常量池，封装类常量池。1.7将字符串常量池放在堆里了。主要在堆里开辟内存，隶属于堆，连续的物理内存。但是相互隔离。
  >
  > JDK1.8改名为元空间，`元空间不再虚拟机设置的内存中，而是使用本地内存`

  > *[常量池](https://blog.csdn.net/qq_43210583/article/details/116558468)是方法区比较重要的一部分，分为四种：类文件常量池（静态常量池，[字节码文件解析](https://blog.csdn.net/n_fly/article/details/114026651) ），字符串常量池，运行时常量池，封装类常量池。类文件常量池中的数据在类加载的时候会放入到运行时常量池中，并将符号引用在解析阶段转换成运行时常量池中的直接引用。*

  | type     | explain                                                      |
  | -------- | ------------------------------------------------------------ |
  | 字面量   | 文本字符串（代码中双括号包裹的字符串）<br />声明为final的常量<br />基本数据类型的表示及属性方法明 |
  | 符号引用 | 类符号引用：类的完全限定名<br />字段的名称和描述符<br />方法的名称和描述符 |

  > `String s = new String("ABC")` 这行单纯代码层面来说，只创建一个对象。如果包含类加载的过程，有可能会创建两个，一个在常量池中，一个在堆里面。堆里面的引用常量池中的value。[参考](https://stackoverflow.com/questions/19672427/string-s-new-stringxyz-how-many-objects-has-been-made-after-this-line-of)
  >
  > `String.intern()` 方法首先会在常量池中查找等值字符串，找到了，返回引用。没找到，常量池创建并返回引用。
  > JDK1.7之前。不存在创建的是等值字符串，之后，创建的是堆中的引用。

- 堆

- 虚拟机栈
	
	- 局部变量表

## 垃圾回收器

## 垃圾回收算法

## 扩展

* 新生代到老年代的标准
  1. -XX:MaxTenuringThreshlod设置的默认对象年龄【15】，因为对象年龄用4bit 位表示，最大1111(15)。
  2. 动态对象年龄判断：survivor区域中年龄从低到高对象大小总和大于-XX:TargetSurvivorRatio[:50%]，则将当前年龄及大雨当前年龄的对象晋升。[误区](https://wenku.baidu.com/view/e90d99cc142ded630b1c59eef8c75fbfc77d94a0.html )
  3. 大对象直接进入老年代：-XX:PretenureSizeThreshold。超过这个值的对象直接放在老年代。
  4. 空间分配担保机制：![](../images/HandlerPromotionFailure.png)
* GCROOT 对象
  1. 每个帧栈[局部变量表](https://hllvm-group.iteye.com/group/topic/25858)
  2. 常量池中引用的对象。
  3. 方法区中静态变量引用的对象。



## 常见问题及调优

* [由于编写的Java类数量太多导致`PermGen OutOfMemoryError`](https://www.liaoxuefeng.com/article/1336345083510818)  

  > 一次是某公司的超大型Java程序，导致PermGen OutOfMemoryError,那是JDK1.6，原因很简单，编写的Java类数量太多了，撑爆了默认的128M的永久代。解决方法很简单，改成更大的512M（参数名叫啥已经忘了，因为新版JVM没有PermGen限制了）。但是根本问题不是出在JVM，而是代码太垃圾，Java类的数据超多造成的。

* CPU 100% 问题排查

  > 1 在Linux上面使用 `top` 命令进程查询 CPU100% 进程ID[pid]![linux-top](../images/linux-top.png)
  >
  > ![](/Users/stevenobelia/Desktop/knownledges/images/linux-top.png)
  >
  >
  > 2  使用`top -Hp 23198` 查看具体线程所占用CPU,然后将线程通过`printf "%x\n" 23208` 记录线程ID ![](../images/linux-thread-0x.jpg)
  >
  >
  > 
  > 3 使用`jstack 23198` 打印进程下线程状态。根据上一步获取的十六进制的线程ID 查找是那个线程栈导致。![](../images/find-nid-check.jpg)

* 频繁FGC导致线上程序运行缓慢