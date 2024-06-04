* ## Structure

    ![](/.images/doc/base/io/iostream.png )

* ## FileDescriptor

    ?> 文件描述符类的实例充当底层机器特定结构的不透明句柄，表示打开的文件、打开的套接字或另一个字节源或接收器。文件描述符的主要实际用途是创建FileInputStream或FileOutputStream来包含它。
    <br><br>它相当于将指定文件的信息与新建的文件指针fp相关联，在 FILE 结构内部记录了这样一些信息：文件内部的当前读写位置、读写报错的记录、文件结尾指示器、缓冲区开始位置的指针、文件标识符、一个计数器（统计拷贝进缓冲区的字节数）等等。后继的操作就可以使用这个指针（而不是文件名）来处理指定文件。[参考](https://wangdoc.com/clang/file)

* ## InputStream

    ?> signature： `public abstract class InputStream implements Closeable {}`

    ![](/.images/doc/base/io/io-01.png ':size=60%')

    + ### FileInputStream

        - #### 摘抄代码

            > [!] jdk代码来自【openjdk 23-internal】

            <!-- tabs:start -->
            ##### **FileInputStream.java**

            ```java
            package java.io;

            import java.nio.channels.FileChannel;
            import sun.nio.ch.FileChannelImpl;

            public
            class FileInputStream extends InputStream
            {
                /* File Descriptor - handle to the open file */
                private final FileDescriptor fd;

                /**
                * The path of the referenced file
                * (null if the stream is created with a file descriptor)
                */
                private final String path;

                public FileInputStream(String name) throws FileNotFoundException {
                    this(name != null ? new File(name) : null);
                }

                public FileInputStream(File file) throws FileNotFoundException {
                    String name = (file != null ? file.getPath() : null);
                    SecurityManager security = System.getSecurityManager();
                    if (security != null) {
                        security.checkRead(name);
                    }
                    if (name == null) {
                        throw new NullPointerException();
                    }
                    if (file.isInvalid()) {
                        throw new FileNotFoundException("Invalid file path");
                    }
                    fd = new FileDescriptor();
                    fd.attach(this);
                    path = name;
                    open(name);
                }

                private native void open0(String name) throws FileNotFoundException;

                private void open(String name) throws FileNotFoundException {
                    open0(name);
                }

                private static native void initIDs();

                private native void close0() throws IOException;

                static {
                    initIDs();
                }
            }
            ```

            ##### **FileInputStream.c**
            ```c
            jfieldID fis_fd; /* id for jobject 'fd' in java.io.FileInputStream */

            /**************************************************************
            * static methods to store field ID's in initializers
            */

            JNIEXPORT void JNICALL
            Java_java_io_FileInputStream_initIDs(JNIEnv *env, jclass fdClass) {
                fis_fd = (*env)->GetFieldID(env, fdClass, "fd", "Ljava/io/FileDescriptor;");
            }

            /**************************************************************
            * Input stream
            */

            JNIEXPORT void JNICALL
            Java_java_io_FileInputStream_open0(JNIEnv *env, jobject this, jstring path) {
                fileOpen(env, this, path, fis_fd, O_RDONLY);
            }

            JNIEXPORT jint JNICALL
            Java_java_io_FileInputStream_read0(JNIEnv *env, jobject this) {
                return readSingle(env, this, fis_fd);
            }
            ```

            ##### **io_util_md.c**
            ```c
            void
            fileOpen(JNIEnv *env, jobject this, jstring path, jfieldID fid, int flags)
            {
                WITH_PLATFORM_STRING(env, path, ps) {
                    FD fd;

            #if defined(__linux__) || defined(_ALLBSD_SOURCE)
                    /* Remove trailing slashes, since the kernel won't */
                    char *p = (char *)ps + strlen(ps) - 1;
                    while ((p > ps) && (*p == '/'))
                        *p-- = '\0';
            #endif
                    fd = handleOpen(ps, flags, 0666);
                    if (fd != -1) {
                        jobject fdobj;
                        jboolean append;
                        fdobj = (*env)->GetObjectField(env, this, fid);
                        if (fdobj != NULL) {
                            // Set FD
                            (*env)->SetIntField(env, fdobj, IO_fd_fdID, fd);
                            append = (flags & O_APPEND) == 0 ? JNI_FALSE : JNI_TRUE;
                            (*env)->SetBooleanField(env, fdobj, IO_append_fdID, append);
                        }
                    } else {
                        throwFileNotFoundException(env, path);
                    }
                } END_PLATFORM_STRING(env, ps);
            }
            ```

            ##### **FileDescriptor_md.c**
            ```c
            /* field id for jint 'fd' in java.io.FileDescriptor */
            jfieldID IO_fd_fdID;
            ```

            <!-- tabs:end -->

        - #### 构造器初始化解读

            1. 调用静态方法`initIDs()`用来初始化`FileDescriptor fd`属性，此处并不是赋值，而是让FileInputStream.c 中的`fis_fd`保存属性位置。方便后续通过C代码能够获取当前fd属性对象。近而对当前FileInputStream中的fd对象中的属性fd赋值真正的文件描述符id，例如整型值`23`.
            2. 通过open0()方法调用原生方法获取1中需要的文件描述符id，
            <br>2.1 将`fis_fd`通过fileOpen函数传入io_util_md.c中.
            <br>2.2 `fd = handleOpen(ps, flags, 0666);`尝试打开给定路径的文件。获取文件描述符id。
            3. 将2中获取的文件描述符id赋值给FileInputStream中的fd对象中的属性fd
            <br>3.1 `fdobj = (*env)->GetObjectField(env, this, fid);` 获取当前FileInputStream对象中的`FileDescriptor fd`这个对象。
            <br>3.2 `(*env)->SetIntField(env, fdobj, IO_fd_fdID, fd);` ___IO_fd_fdID___ 定义在FileDescriptor_md.c文件中。对3.1中的对象fdobj赋值 fd.
            4. 后续原生读写都会通过这个FD进行。

## Reference
* https://stackoverflow.com/questions/10006293/why-some-java-methods-in-core-libraries-end-with-numbers
* https://wangdoc.com/clang/file
* https://web.archive.org/web/20140528182031/https://blogs.oracle.com/slc/entry/javanio_vs_javaio
* https://medium.com/@nilasini/java-nio-non-blocking-io-vs-io-1731caa910a2
* https://stackoverflow.com/tags/nio/info