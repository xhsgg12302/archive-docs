* ## Intro(Thread)

    + ### 状态

        ![](/.images/doc/base/thread/thread-status-01.png ':size=70%')

        <details><summary>源码</summary>

        ```java
        public enum State {
            /**
            * Thread state for a thread which has not yet started.
            */
            NEW,

            /**
            * Thread state for a runnable thread.  A thread in the runnable
            * state is executing in the Java virtual machine but it may
            * be waiting for other resources from the operating system
            * such as processor.
            */
            RUNNABLE,

            /**
            * Thread state for a thread blocked waiting for a monitor lock.
            * A thread in the blocked state is waiting for a monitor lock
            * to enter a synchronized block/method or
            * reenter a synchronized block/method after calling
            * {@link Object#wait() Object.wait}.
            */
            BLOCKED,

            /**
            * Thread state for a waiting thread.
            * A thread is in the waiting state due to calling one of the
            * following methods:
            * <ul>
            *   <li>{@link Object#wait() Object.wait} with no timeout</li>
            *   <li>{@link #join() Thread.join} with no timeout</li>
            *   <li>{@link LockSupport#park() LockSupport.park}</li>
            * </ul>
            *
            * <p>A thread in the waiting state is waiting for another thread to
            * perform a particular action.
            *
            * For example, a thread that has called <tt>Object.wait()</tt>
            * on an object is waiting for another thread to call
            * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
            * that object. A thread that has called <tt>Thread.join()</tt>
            * is waiting for a specified thread to terminate.
            */
            WAITING,

            /**
            * Thread state for a waiting thread with a specified waiting time.
            * A thread is in the timed waiting state due to calling one of
            * the following methods with a specified positive waiting time:
            * <ul>
            *   <li>{@link #sleep Thread.sleep}</li>
            *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
            *   <li>{@link #join(long) Thread.join} with timeout</li>
            *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
            *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
            * </ul>
            */
            TIMED_WAITING,

            /**
            * Thread state for a terminated thread.
            * The thread has completed execution.
            */
            TERMINATED;
        }
        ```
        </details>

    + ### 创建方式

        ?> 基本两种方式： `继承Thread`或者`重写run方法`。或者实现runnable接口，定义target的run方法。<br>
        <br>其余的都是扩展，比如new Thread(futureTask),其实 FutureTask 一起实现了 Runnable, Future接口。运行结束后通过futureTask对象的get（）阻塞获取值。另外FutureTask构造器需要Callable

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### 重写run方法
        <!-- div:left-panel-40 -->
    
        ?> hello

        <!-- div:right-panel-60 -->
        ```java
        // method 1.0
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName() + ": hello world");
            }
        }).start();

        // method 1.1
        new Thread(() -> { System.out.println(Thread.currentThread().getName() + ": hello world"); }).start();
        ```
        <!-- panels:end -->

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### 继承Thread
        <!-- div:left-panel-40 -->
    
        ?> hello hello hello hello hello hello hello heelo

        <!-- div:right-panel-60 -->
        ```java
        // 定义MyThread线程
        class MyThread extends Thread {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName() + ": hello world");
            }
        }
        // 启动线程
        new MyThread().start();
        ```
        <!-- panels:end -->
    
    + ### 关于线程的几个方法

        !> `notify,wait`造成的死锁
        <br> 解释：假设有四个线程，C1,C2,P1,P2 正在锁池
        <br> 现在C1，拿到锁，没数据，进入等待池,C2 同理进入等待池
        <br> P1 拿到锁，生产，notify C1进入锁重新争取。P1退出。此时P2也在。若P2拿到锁，进入等待池。此时P2,C2都在等待池。
        <br> C1 拿到锁，消费，notify P2 --> 正常情况
        <br> C1 拿到锁，消费，notify C2 --> C2,P2会一直在等待池中。造成死锁

        1. sleep
        2. join
        3. wait
        4. interrupted
        5. interrupt
        6. isInterrupted

    ?> （sleep,join,wait,interrupted,interrupt,isInterrupted）
        <br> `join` 方法实现原理：调用 thd.join()方法的线程会wait在 thd线程对象的身上，时间为0的话会直到对象死亡，JVM释放对象身上的等待线程。<br> `isInterrupted()`返回标志。`interrupted()` 表示返回标志，如果为true,清除。`interrupt()`设置中断标志。

        <details><summary>代码示例</summary>

        ```java
        private static void t1() throws InterruptedException {
            Thread t1 = new Thread(() -> {
                synchronized (Thread.currentThread()) {
                    System.out.println("t1 running");
                    System.out.println("线程中断状态" + Thread.currentThread().isInterrupted());
                    try {
                        //等待，让线程被打标记为interrupted后再执行到wait方法
                        for (int i = 0; i < 10000; i++) {
                        }
                        // Thread.interrupted(); 可以通过这行清除 中断 标志，以至调用wait,join,sleep等方法不发生中断异常
                        Thread.currentThread().wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println("线程中断状态" + Thread.currentThread().isInterrupted());
                }
            });
            t1.start();
            t1.interrupt();
        }
        ```
        </details>

    + ### synchronzied关键字

        ?> 锁住代码，对象解释
        <br><br>1. 对象锁和类锁不同步
        <br>2. 加synchronized的方法，线程会读锁，而没有加的线程不会读锁，直接进入执行。(也就是一个对象两个syn方法会同步，一个syn方法和普通方法不同步)
        <br>3. 加载方法上通过字节码方法修饰符`ACC_SYNCHRONIZED`保证,代码块通过字节码指令`monitorenter`,`monitorexit`来保证。

    + ### volatile关键字

        ?> hello
    
    + ### ThreadLocal

        ?> hello

    + ### 生产者消费者模型

## thread pool
* 线程池的创建方式
* 线程池的几个核心参数
* 线程池的运行流程

## TODO
* ThreadLocal

## Reference
* [你真的懂wait、notify和notifyAll吗](https://www.jianshu.com/p/25e243850bd2?appinstall=0)
* [读懂isInterrupted、interrupted和interrupt](https://zhuanlan.zhihu.com/p/265169898)
* https://stackoverflow.com/questions/15680422/difference-between-wait-and-blocked-thread-states
* https://drive.google.com/file/d/1r_DtdgyC9bHP-Y-poLb5fbQIttrWTkAa/view?usp=sharing
