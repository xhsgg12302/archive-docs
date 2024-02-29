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

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### 1. 重写run方法
        <!-- div:left-panel-40 -->
        !> 重写`Runnable`的run方法相当于自定义target，start启动后调用start0()native方法给线程分配运行资源，近而重新调度到`Thread`的run方法。并传递给`Runnbale target`中的run方法。
        <br><br>因为这个是直接通过Thread类启动的。所以run方法没有被重载。所以会调度到target --> run()方法中。
        <!-- div:right-panel-60 -->
        ```java
        class PrimeRun implements Runnable {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName() + ": hello world");
            }
        }
        // 启动线程
        new Thread(new PrimeRun()).start();
        ```
        <!-- panels:end -->

        ?> 其余的都是扩展，比如new Thread(futureTask),其实 FutureTask 一起实现了 Runnable, Future接口。运行结束后通过futureTask对象的get（）阻塞获取值。另外FutureTask构造器需要Callable

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### 2. 继承Thread
        <!-- div:left-panel-40 -->
        !> 而继承的线程和刚才的实现接口不太一样的地方是：不需要target，也无需调用target的 run()方法。并且整个运行过程中target为NULL.
        <br>因为整个Thread对象run方法被子类`PrimeThread`给重写了。

        <!-- div:right-panel-60 -->
        ```java
        class PrimeThread extends Thread {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName() + ": hello world");
            }
        }
        // 启动线程
        new MyThread().start();
        ```
        <!-- panels:end -->
    
    + ### 线程相关的方法

        ?> （sleep,join,wait,interrupted,interrupt,isInterrupted）

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### 1. sleep
        <!-- div:left-panel-40 -->
        !> 使当前执行的线程在指定的毫秒数内休眠(暂时停止执行)，这取决于系统计时器和调度器的精度和准确性。线程不会失去任何监视器的所有权(意味着不会释放锁)。
        <!-- div:right-panel-60 -->
        ```java
        // Causes the currently executing thread to sleep (temporarily cease execution) 
        // for the specified number of milliseconds, 
        // subject to the precision and accuracy of system timers and schedulers. 
        // The thread does not lose ownership of any monitors. 
        public static void sleep(long millis, int nanos)
        throws InterruptedException {
            if (millis < 0) {
                throw new IllegalArgumentException("timeout value is negative");
            }

            if (nanos < 0 || nanos > 999999) {
                throw new IllegalArgumentException(
                                    "nanosecond timeout value out of range");
            }

            if (nanos >= 500000 || (nanos != 0 && millis == 0)) {
                millis++;
            }

            sleep(millis);
        }

        // native
        public static native void sleep(long millis) throws InterruptedException;
        ```
        <!-- panels:end -->

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### 2. join
        <!-- div:left-panel-40 -->
        ?> 1). join() <=等价与=> join(0)
        <br><br>2). 等待该线程死亡的时间最多为`millis`毫秒。`0`意味着永久等待。
        <br><br>3). 通过循环调用`this.wait`来实现join逻辑，需要在当前线程存活的情况下。
        <br><br>4). 当一个线程终止时，`this.notifyAll`方法会被调用。所以`thread1`无限wait到`thread2`对象上时。`thread1`被唤醒的情况之一就是`thread2`死亡，JVM会调用`thread2`线程对象的notifyAll方法，释放wait在上面的其他资源(例如:`thread1`)。
        <!-- div:right-panel-60 -->
        ```java
        /*
         * Waits at most millis milliseconds for this thread to die. A timeout of 0 means to wait forever.
         * This implementation uses a loop of this.wait calls conditioned on this.isAlive. 
         * As a thread terminates the this.notifyAll method is invoked. 
         * It is recommended that applications not use wait, notify, or notifyAll on Thread instances.
         */
        public final synchronized void join(long millis) 
        throws InterruptedException {
            long base = System.currentTimeMillis();
            long now = 0;

            if (millis < 0) {
                throw new IllegalArgumentException("timeout value is negative");
            }

            if (millis == 0) {
                while (isAlive()) {
                    wait(0);
                }
            } else {
                while (isAlive()) {
                    long delay = millis - now;
                    if (delay <= 0) {
                        break;
                    }
                    wait(delay);
                    now = System.currentTimeMillis() - base;
                }
            }
        }
        ```
        <!-- panels:end -->

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### 3. wait/notify/notifyAll
        <!-- div:left-panel-40 -->
        ?> JVM会为一个使用内部锁（synchronized）的对象维护两个集合，Entry Set和Wait Set，也有人翻译为锁池和等待池，意思基本一致。[参考](https://www.jianshu.com/p/25e243850bd2)
        <!-- div:right-panel-60 -->
        ```java
        public final native void wait(long timeout) throws InterruptedException;
        public final native void notify();
        public final native void notifyAll();
        ```
        <!-- panels:end -->
        <!-- panels:start -->
        <!-- div:left-panel-40 -->
        !> `wait,notify`容易造成的死锁
        <br> 解释：假设有四个线程，C1,C2,P1,P2 正在锁池
        <br><br> 现在C1，拿到锁，没数据，进入等待池,C2 同理进入等待池
        <br><br> P1 拿到锁，生产，notify C1进入锁重新争取。P1退出。此时P2也在。若P2拿到锁，进入等待池。此时P2,C2都在等待池。
        <br><br> C1 拿到锁，消费，notify P2 --> 正常情况
        <br><br> C1 拿到锁，消费，notify C2 --> C2,P2会一直在等待池中。造成死锁
        <!-- div:right-panel-60 -->
        ![](/.images/doc/base/thread/thread-notify-wait-01.png ':size=100%')
        <!-- panels:end -->

        <!-- panels:start -->
        <!-- div:title-panel -->
        #### 4. interrupted/interrupt/isInterrupted
        <!-- div:left-panel-40 -->
        !> 1). interrupt()用于设置中断标志。如果在碰见以下情况:清除标志位，并且抛出一个`InterruptedException`异常
        <br> 此线程正在调用一个对象的`wait()`,`join()`,或者`sleep()`方法。等等...
        <br><br>2). 静态方法，获取当前线程的中断标志，并给native方法传参数`true`清除标志位。
        <br>换句话说，如果连续调用两次这个方法。则第二次会返回`false`。
        <br><br>3). 返回中断标志。
        <br><br>4). 中断探测与是否清空标志的native方法。
        <!-- div:right-panel-60 -->
        ```java
        // If this thread is blocked in an invocation of the wait(), join(),sleep() methods of the Object class, 
        // then its interrupt status will be cleared and it will receive an InterruptedException.
        // and else conditions ...
        // If none of the previous conditions hold then this thread's interrupt status will be set.
        public void interrupt() {
            if (this != Thread.currentThread())
                checkAccess();

            synchronized (blockerLock) {
                Interruptible b = blocker;
                if (b != null) {
                    interrupt0();           // Just to set the interrupt flag
                    b.interrupt(this);
                    return;
                }
            }
            interrupt0();
        }

        // Tests whether the current thread has been interrupted. 
        // The interrupted status of the thread is cleared by this method. 
        // In other words, if this method were to be called twice in succession, 
        // the second call would return false
        public static boolean interrupted() {
            return currentThread().isInterrupted(true);
        }

        // Tests whether this thread has been interrupted. 
        // The interrupted status of the thread is unaffected by this method.
        public boolean isInterrupted() {
            return isInterrupted(false);
        }

        // Tests if some Thread has been interrupted. 
        // The interrupted state is reset or not based on 
        // the value of ClearInterrupted that is passed.
        private native boolean isInterrupted(boolean ClearInterrupted);
        ```
        <!-- panels:end -->

        ?> `isInterrupted()`返回标志。`interrupt()`设置中断标志。
        <br>`interrupted()` 表示返回标志，如果为true,清除。

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

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```java
        @Test
        public void test01(){
            ThreadLocal<String> local = new ThreadLocal<>();
            local.set("hello world");
        }
        ```
        ```java
        static class ThreadLocalMap {
            static class Entry extends WeakReference<ThreadLocal<?>> {
                /** The value associated with this ThreadLocal. */
                Object value;

                Entry(ThreadLocal<?> k, Object v) {
                    super(k);
                    value = v;
                }
            }
        }
        ```
        <!-- div:right-panel-50 -->
        ```java
        //
        //
        public void set(T value) {
            Thread t = Thread.currentThread();
            ThreadLocalMap map = getMap(t);
            if (map != null)
                map.set(this, value);
            else
                createMap(t, value);
        }

        ThreadLocalMap getMap(Thread t) {
            return t.threadLocals;
        }

        void createMap(Thread t, T firstValue) {
            t.threadLocals = new ThreadLocalMap(this, firstValue);
        }
        
        // ThreadLocal values pertaining to this thread. This map is maintained by the ThreadLocal class.
        ThreadLocal.ThreadLocalMap threadLocals = null;
        ```
        <!-- panels:end -->
        !> 1). 主要是通过ThreadLocal对象给Thread中的属性`ThreadLocalMap`赋值。
        <br>但是因为这个属性`ThreadLocalMap`是默认类型，也只允许同包下的类可以访问。所以外部[我们编写的代码]不可以直接访问这个属性。
        <br><br>2). ThreadLocal造成内存泄露:
        <br>继承`WeakReference`的目的是为了调用`super(k)`，将这个Entry中的ThreadLocal类型的key进行虚化。
        <br>因为Entry节点在赋值的时候是强引用，Entry中的value也是强引用，而里面的属性key是虚引用。这样就会形成`Entry entry = new Entry(null, value)`的效果。因为ThreadLocal对象已经回收。所以这个entry正常不会使用了。也就造成内存泄露了。
        <br>当key为null，在下一次ThreadLocalMap调用set(),get()，remove()方法的时候会被清除value值。

    + ### 生产者消费者模型

## thread pool
* 线程池的创建方式
* 线程池的几个核心参数
* 线程池的运行流程

## TODO

## Reference
* [你真的懂wait、notify和notifyAll吗](https://www.jianshu.com/p/25e243850bd2?appinstall=0)
* [读懂isInterrupted、interrupted和interrupt](https://zhuanlan.zhihu.com/p/265169898)
* https://docs.oracle.com/javase/8/docs/technotes/guides/concurrency/threadPrimitiveDeprecation.html
* https://stackoverflow.com/questions/5218969/why-is-thread-stop-so-dangerous
* https://stackoverflow.com/questions/15680422/difference-between-wait-and-blocked-thread-states
* https://drive.google.com/file/d/1r_DtdgyC9bHP-Y-poLb5fbQIttrWTkAa/view?usp=sharing
