---
tags: ["translate"]
---

* ## Intro(ZERO COPY)

    > [!CAUTION] 下述零拷贝内容翻译自 [Efficient data transfer through zero copy](https://developer.ibm.com/articles/j-zerocopy/)

    + ### 概览

        > [?] 许多 Web 应用程序提供大量的就静态内容，这相当于从从磁盘读取数据并将完全相同的数据写回响应的 socket。此活动似乎只需要相对较少的 CPU 活动，但是在某种程度上是低效的。内核从磁盘读取数据，并将其跨越内核用户边界推送到应用程序，然后再跨越一次推送到需要被写的 socket。实际上，应用程序充当一个低效的中介，将数据从磁盘文件传输到 socket。
        <br><br>每一次数据从用户内核空间移动的时候，必须被复制，这会消耗 CPU 循环和内存带宽。幸运的是，你可以通过一个叫 `ZERO COPY` **零拷贝** 的技术减少这些复制。使用零拷贝的应用程序请求内核直接将数据从磁盘文件复制到 socket，而不需要经过应用程序。零拷贝极大的提高了应用程序的性能和减少了用户内核空间上下文切换的次数。
        <br><br>Java 类库通过`java.nio.channels.FileChannel`中的`transferTo()`方法在 Linux 和 Unix 系统上支持零拷贝。你可以使用`transferTo()`方法直接传输数据从被调用的 channel 到另外一个可写的 channel，而不需要数据流经应用程序。这篇文章首先演示了通过传统复制进行简单文件传输所带来的开销，然后展示了使用`transferTo()`的零拷贝技术如何获得更好地性能。

    + ### 数据传输：传统方式
        
        > [?] 思考一种场景：从文件中读取数据并通过网络将数据传输到另一个程序。（此场景描述了许多服务器应用程序的行为，包括提供静态内容的 Web 应用程序，FTP 服务器，邮件服务器等。）该操作的核心在清单1 中的两个调用中。或者下载 [完整的样例代码](https://s3.us.cloud-object-storage.appdomain.cloud/developer/default/articles/j-zerocopy/static/j-zerocopy.zip)

        #### 清单1，拷贝字节从文件到socket

        ```java
        File.read(fileDesc, buf, len);
        Socket.send(socket, buf, len);
        ```

        虽然清单1 在概念上很简单，但在内部，拷贝操作需要在用户模式和内核模式之间进行四个上下文切换，并在操作完成之前将数据复制四次。
        <br>下图显示了数据在内部移动从一个文件到 socket

        ![](/.images/doc/base/io/nio/zero-copy/zc-process-01.png ':size=40%')
        ![](/.images/doc/base/io/nio/zero-copy/zc-process-02.png ':size=46%')

        > [?] 涉及到的步骤是：
        <br>`1).` `read()`调用造成上下文切换从用户空间到内核空间，内部会发出一个 **sys_read()** (或等效函数)从文件中读取数据。第一次复制由 DMA（direct memory access）执行，读取文件内容从磁盘并且存储它们到内核地址空间缓存区。
        <br>`2).` 请求数据量大小的数据从读缓冲区到用户缓冲区，并且`read()`调用返回。从这个调用的返回操作造成另外一次上下文切换从内核模式返回到用户模式，现在数据存储在用户地址空间缓冲区中。
        <br>`3).` `send()`调用导致上下文从用户模式转换为内核模式。执行第三次复制以将数据再次放回内核地址空间缓冲区中。但是，这次将数据放入另一个于目标 socket 关联的缓冲区中。
        <br>`4).` `send()`系统调用返回，创建第四次上下文切换。独立，异步地进行第四次复制发生在 DMA 引擎传递数据从内核缓冲区到协议引擎。
        <br><br>使用中间内核缓冲区（而不是直接将数据传输到用户缓冲区）似乎效率很低。但是为了提高性能，在进程中引入了中间内核缓冲区。在读端使用中间缓冲区允许内核缓冲区充当“预读缓存”，当应用程序没有要求内核缓冲区保存那么多数据时。当请求的数据量小于内核缓冲区大小时，这将显著提高性能。写端的中间缓冲区允许写操作异步完成。
        <br><br>不幸的是，如果请求的数据的大小远远大于内核缓冲区的大小，这种方法本身就会成为性能瓶颈。在最终交付给应用程序之前，数据在磁盘、内核缓冲区和用户缓冲区之间被复制多次。
        <br><br>**零拷贝通过消除这些冗余的数据拷贝来提高性能。**

    + ### 数据传输：零拷贝方式

        > [?] 如果你重新检查传统方案，你会注意到实际上并不需要第二和第三个数据拷贝。应用程序只是缓存数据并将其传输回 socket 缓冲区。相反，数据可以直接从读缓冲区传输到 socket 缓冲区。`transferTo()`方法可以让您做到这一点。清单2 显示了`transferTo()`方法签名。
        <br><br>`transferTo()`方法传输数据从文件channel 到被给的 WritebaleByteChannel。在内部，它依赖底层操作系统对零拷贝的支持，在 unix 和各种类型的 linux 中，这个调用被路由到`sendfile()`系统调用，如清单3 所示，它将数据从一个文件描述符传递到另一个文件描述符。
        <br><br>在清单1 中的 file.read() 和 socket.send() 动作可以被替换为单个的 `transferTo()`调用，如清单4 中所展示的一样。

        #### 清单2，transferTo 方法

        ```java
        public void transferTo(long position, long count, WritableByteChannel target);
        ```

        #### 清单3，sendfile 系统调用

        ```c
        #include <sys/socket.h>
        ssize_t sendfile(int out_fd, int in_fd, off_t *offset, size_t count);
        ```

        #### 清单4，使用transferTo拷贝

        ```java
        transferTo(position, count, writableChannel);
        ```

        **Figure 3 展示了使用`transferTo()`方法时的数据路径。以及Figure 4 展示上下文切换。**

        ![](/.images/doc/base/io/nio/zero-copy/zc-process-03.png ':size=40%')
        ![](/.images/doc/base/io/nio/zero-copy/zc-process-04.png ':size=46%')

        > [?] 当使用清单4 中的 `transferTo()`方法时所需要的步骤：
        <br>`1).` `transferTo()`方法使文件内容被DMA引擎复制到读缓冲区中。然后，内核将数据复制到与输出套接字关联的内核缓冲区中。
        <br>`2).` 第三次复制发生在DMA引擎将数据从内核套接字缓冲区传递到协议引擎时。
        <br><br>这是一个改进：我们将上下文切换的数量从4个减少到2个，并将数据副本的数量从4个减少到3个（其中只有一个涉及CPU）。但这还没有让我们达到零复制的目标。如果底层网络接口卡支持`Scatter/Gather`操作，我们可以进一步减少内核完成的数据重复。在Linux内核2.4及更高版本中，套接字缓冲区描述符被修改以适应这一要求。这种方法不仅减少了多个上下文切换，而且消除了需要CPU参与的重复数据拷贝。用户端的用法仍然保持不变，但是内在的使用情况已经改变：
        <br>`a).` `transferTo()`方法使文件内容被DMA引擎复制到内核缓冲区中。
        <br>`b).` 没有数据被复制到套接字缓冲区。相反，只有包含有关数据位置和长度信息的描述符才会被附加到套接字缓冲区中。DMA引擎将数据直接从内核缓冲区传递到协议引擎，从而减少了剩余的最终CPU拷贝。
        <br><br>Figure 5 展示了带有`gather`操作的`transferTo()`方法数据拷贝。

        ![](/.images/doc/base/io/nio/zero-copy/zc-process-05.png ':size=76%')

        > [!WARNING|label:译者注] 参考[文章：Zero-copy: Principle and Implementation](https://medium.com/@kaixin667689/zero-copy-principle-and-implementation-9a5220a62ffd) 中的描述，此处的图应该是将读缓冲区的文件描述符和长度等关键信息追加到了 socket 缓冲区，然后 DMA 引擎复制 socket 缓存区中的内容时，发现数据在读缓冲区中，然后去读缓冲区将内容拷贝到了协议引擎（网络适配器）。

* ## Reference

    + https://developer.ibm.com/articles/j-zerocopy/
    + https://medium.com/@kaixin667689/zero-copy-principle-and-implementation-9a5220a62ffd