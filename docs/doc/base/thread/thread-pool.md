* ## Intro(THREAD POOL)

    > [!] [ThreadPoolExecutor.java](https://github.com/openjdk/jdk/blob/jdk8-b120/jdk/src/share/classes/java/util/concurrent/ThreadPoolExecutor.java)
    <br><br>线程池解决了两个不同的问题:
    <br>`1).` 在执行大量异步任务时，由于减少了每个任务的调用开销，它们通常提供改进的性能;
    <br>`2).` 在执行一组任务时，它们提供了一种绑定和管理资源(包括线程)的方法。每个ThreadPoolExecutor还维护一些基本统计信息，例如已完成任务的数量。
    <br><br>`核心和最大线程数`：
    <br>线程池会根据`核心线程数`和`最大线程数`自动调节池中的线程数量。当一个任务提交的时候，如果少于核心线程数的线程正在运行，则一个新的线程被创建用来处理当前的任务请求（即使其它线程空闲）。如果运行的线程数在`核心线程`和`最大线程`之间，则优先队列任务，如果队列满了，就创建线程。
    <br><br>`队列(BlockingQueue)`：
    <br>队列用来传输和持有提交的任务，它的使用和线程池中的线程数互相作用。有以下几点：
    <br>`a).` 小于核心线程数的线程正在运行，则创建新线程。
    <br>`b).` 大于核心线程数，则入队列
    <br>`c).` 入队列不成功(满了)，但是小于最大线程数，则创建新线程。
    <br>`d).` 其他情况(队列满了且达到最大线程数)，则拒绝执行。
    <br><br>对于队列的选择，一般有三种情况：
    <br>`SynchronousQueue`：这个阻塞队列的特性是，每插入一个元素，必须消费之后才可以继续插入。而且可以支持公平和非公平选择，用于对等待的生产者和消费者线程进行排序。所以使用这种队列，如果在生产速度大于消费速度，则通过queue.offer()方法放不进去，只能创建新线程进行处理了。所以线程数可能无限增长。
    <br>![](/.images/doc/base/thread/thread-pool/tp-queue-01.png ':size=90%')
    <br>`LinkedBlockingQueue`：偏无界队列（`Integer.MAX_VALUE`）
    <br>`ArrayBlockingQueue`：有边界
    <br><br>钩子方法：`beforeExecute(Thread, Runnable) `、`afterExecute(Runnable, Throwable)`
    <br><br>按需加载：`prestartCoreThread(启动一个核心线程)`、`prestartAllCoreThreads(启动所有核心线程)`

    + ### 属性

        > [?] **ctl** 是一个原子整数类型，里面主要体现两个属性：`workerCount`: 有效的线程数量、`runState`: 是否运行，关闭等状态。
        <br>为了将这两个属性放在一个数字里面控制，我们限制了`workerCount`的值为 ${\color{blue}(2^{29} - 1 )}$(大约5亿个线程)，而不是 ${\color{blue} (2^{31} - 1)}$(大约20亿)
        <br><br>`workerCount`：允许开始执行的线程数。因为一些原因，这个值可能和实际存活的线程数暂时不一致，比如：当要求线程池创建线程的时候失败了，或者在terminated之前退出线程依旧在执行任务。
        <br><br>`runState`：线程池状态
        <br><span style='padding-left: 2.9em'/>`RUNNING`: 可以接受新的任务并且处理队列中的任务
        <br><span style='padding-left: 2.9em'/>`SHUTDOWN`: 不接受新的任务但是会处理队列中的任务
        <br><span style='padding-left: 2.9em'/>`STOP`: 不接受新的任务，不处理队列中的任务，并且给中断正在执行的线程
        <br><span style='padding-left: 2.9em'/>`TIDYING`: 所有任务都已种植，**workerCount** 为零，转换到 **TIDYING** 状态的线程将执行`terminated()`钩子方法。
        <br><span style='padding-left: 2.9em'/>`TERMINATED`: `terminated()`钩子方法执行完毕。
        <br><br>为了允许有序比较，这些值之间的数字顺序很重要。runState单调地随时间增加，但不需要达到每个状态。转换过程：

        > [!CAUTION] 下面代码中的属性值转换成十进制没有任何意义，所以直接使用二进制计算就行。
        <br>~连续多个 0、1 简写：使用括号，里面是个数及值，中间用`|`分割。比如`(29|0)`表示连续 29个0，`(28|1)`表示连续 28个1 。~

        ```java
        private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));

        // Integer.SIZE = 32
        private static final int COUNT_BITS = Integer.SIZE - 3;  // 29
        private static final int CAPACITY   = (1 << COUNT_BITS) - 1;  // 1(29|0) - 1 = 0(29|1) = 0001 1111 1111 1111 1111 1111 1111 1111

        // runState is stored in the high-order bits
        private static final int RUNNING    = -1 << COUNT_BITS;  // 1110 0000 0000 0000 0000 0000 0000 0000
        private static final int SHUTDOWN   =  0 << COUNT_BITS;  // 0000 0000 0000 0000 0000 0000 0000 0000
        private static final int STOP       =  1 << COUNT_BITS;  // 0010 0000 0000 0000 0000 0000 0000 0000
        private static final int TIDYING    =  2 << COUNT_BITS;  // 0100 0000 0000 0000 0000 0000 0000 0000
        private static final int TERMINATED =  3 << COUNT_BITS;  // 0110 0000 0000 0000 0000 0000 0000 0000

        // Packing and unpacking ctl
        private static int runStateOf(int c)     { return c & ~CAPACITY; }  // c & 1110 0000 0000 0000 0000 0000 0000 0000  ==> 取传入值中的高3位
        private static int workerCountOf(int c)  { return c & CAPACITY;  }  // c & 0001 1111 1111 1111 1111 1111 1111 1111  ==> 取传入值中的低29位
        private static int ctlOf(int rs, int wc) { return rs | wc; }

        /*
         * Bit field accessors that don't require unpacking ctl.
         * These depend on the bit layout and on workerCount being never negative.
         */

        private static boolean runStateLessThan(int c, int s) { return c < s; } // 运行状态小于s。例如 如果 s = STOP, 则 c 处于 RUNNING、SHUTDOWN 状态

        private static boolean runStateAtLeast(int c, int s) { return c >= s; } // 运行状态 >=s。例如 如果 s = STOP, 则 c 处于 STOP、TIDYING、TERMINATED 状态

        private static boolean isRunning(int c) { return c < SHUTDOWN; }        // 是否运行中。 只有运行状态是负数，所以只需要判断小于 SHUTDOWN（0）即可
        ```

    + ### 方法

        - #### execute

            > [?] 提交任务

            ```java
            public void execute(Runnable command) {
                if (command == null)
                    throw new NullPointerException();
                /*
                 * Proceed in 3 steps:
                 *
                 * 1. If fewer than corePoolSize threads are running, try to
                 * start a new thread with the given command as its first
                 * task.  The call to addWorker atomically checks runState and
                 * workerCount, and so prevents false alarms that would add
                 * threads when it shouldn't, by returning false.
                 *
                 * 2. If a task can be successfully queued, then we still need
                 * to double-check whether we should have added a thread
                 * (because existing ones died since last checking) or that
                 * the pool shut down since entry into this method. So we
                 * recheck state and if necessary roll back the enqueuing if
                 * stopped, or start a new thread if there are none.
                 *
                 * 3. If we cannot queue task, then we try to add a new
                 * thread.  If it fails, we know we are shut down or saturated
                 * and so reject the task.
                 */
                int c = ctl.get();  // 获取线程池的控制状态
                if (workerCountOf(c) < corePoolSize) {              // 判断运行的线程数是否小于核心线程
                    if (addWorker(command, true))                       // 增加 worker 去处理任务
                        return;
                    c = ctl.get();
                }
                if (isRunning(c) && workQueue.offer(command)) {     // 将任务追加到队列
                    int recheck = ctl.get();                            // 再次获取状态
                    if (!isRunning(recheck) && remove(command))        // 如果线程池停止了，将任务从队列中移除
                        reject(command);                                    // 触发拒绝任务处理
                    else if (workerCountOf(recheck) == 0)               // 如果由于一些原因(进入TIDYING状态且移除失败)，没有工作线程了。[运行状态下 workerCountOf() 大概率不会为 0]
                        addWorker(null, false);                             // 则增加一个线程去处理队列中的任务。
                }
                else if (!addWorker(command, false))                // 扩大非核心线程
                    reject(command);                                    // 失败的话触发拒绝任务处理
            }
            ```

        - #### addWorker

            > [?] 检查是否可以根据当前池状态和给定的边界(core或maximum)添加新的工作线程。如果是，则相应地调整工作线程的数量，创建并启动一个新的工作线程，并将firstTask作为其第一个任务运行。
            <br><span style='padding-left:2.0em'/>如果池已停止或临近关闭，则此方法返回false。
            <br><span style='padding-left:2.0em'/>如果线程工厂在被要求创建线程时失败，它还返回false。如果线程创建失败，要么是由于线程工厂返回null，要么是由于异常(通常是thread .start()中的OutOfMemoryError)，我们将彻底回滚。

            ```java
            private final class Worker
                extends AbstractQueuedSynchronizer
                implements Runnable
            {
                ...
                Worker(Runnable firstTask) {
                    setState(-1); // inhibit interrupts until runWorker
                    this.firstTask = firstTask;
                    this.thread = getThreadFactory().newThread(this);
                }

                /** Delegates main run loop to outer runWorker  */
                public void run() {
                    runWorker(this);
                }

                /**
                 * Checks if a new worker can be added with respect to current pool state and the given bound (either 
                 * core or maximum).  If so, the worker count is adjusted accordingly, and, if possible, a new worker is
                 * created and started, running firstTask as its first task.  This method returns false if the pool is
                 * stopped or eligible to shut down.  It also returns false if the thread factory fails to create a thread
                 * when asked.  If the thread creation fails, either due to the thread factory returning null, or due to an
                 * exception (typically OutOfMemoryError in Thread.start()), we roll back cleanly.
                 */
                private boolean addWorker(Runnable firstTask, boolean core) {
                    retry:
                    for (;;) {
                        int c = ctl.get();
                        int rs = runStateOf(c);

                        // Check if queue empty only if necessary.
                        // rs >= SHUTDOWN && !( rs == SHUTDOWN && firstTask == null && !workQueue.isEmpty() )
                        // rs >= SHUTDOWN && ( rs != SHUTDOWN || firstTask != null || workQueue.isEmpty() )
                        // rs 为（STOP、TIDYING、TERMINATED）并且 (第一个任务不等于 null 或者 队列等于空）
                        if (rs >= SHUTDOWN &&
                            !(rs == SHUTDOWN &&
                            firstTask == null &&
                            !workQueue.isEmpty()))
                            return false;

                        for (;;) {
                            int wc = workerCountOf(c);
                            if (wc >= CAPACITY ||                               // 工作线程数大于最大值
                                wc >= (core ? corePoolSize : maximumPoolSize))  // 工作线程数大于(core, 核心线程数)，(非core，最大线程数)
                                return false;
                            if (compareAndIncrementWorkerCount(c))              // 确保 workerCount + 1，后退出循环
                                break retry;
                            c = ctl.get();  // Re-read ctl
                            if (runStateOf(c) != rs)
                                continue retry;
                            // else CAS failed due to workerCount change; retry inner loop
                        }
                    }

                    boolean workerStarted = false;
                    boolean workerAdded = false;
                    Worker w = null;
                    try {
                        w = new Worker(firstTask);
                        final Thread t = w.thread;
                        if (t != null) {
                            final ReentrantLock mainLock = this.mainLock;
                            mainLock.lock();
                            try {
                                // Recheck while holding lock.
                                // Back out on ThreadFactory failure or if
                                // shut down before lock acquired.
                                int rs = runStateOf(ctl.get());

                                if (rs < SHUTDOWN ||                                    // RUNNING
                                    (rs == SHUTDOWN && firstTask == null)) {            // SHUTDOWN 增加线程处理队列任务
                                    if (t.isAlive()) // precheck that t is startable
                                        throw new IllegalThreadStateException();
                                    workers.add(w);                                     // 增加到 workers 集合
                                    int s = workers.size();
                                    if (s > largestPoolSize)                            // 记录巅峰值
                                        largestPoolSize = s;
                                    workerAdded = true;                                 // workerAdded = true
                                }
                            } finally {
                                mainLock.unlock();
                            }
                            if (workerAdded) {                                          // 根据 workerAdded 判断是否需要启动线程，并设置 workerStarted = true
                                t.start();
                                workerStarted = true;
                            }
                        }
                    } finally {
                        if (!workerStarted)
                            addWorkerFailed(w);                                         // 没有启动的话，回滚。（移出队列，并且 workerCount - 1）
                    }
                    return workerStarted;
                }

                final void runWorker(Worker w) {
                    Thread wt = Thread.currentThread();
                    Runnable task = w.firstTask;
                    w.firstTask = null;
                    w.unlock(); // allow interrupts
                    boolean completedAbruptly = true;
                    try {
                        while (task != null || (task = getTask()) != null) {
                            w.lock();
                            // If pool is stopping, ensure thread is interrupted;
                            // if not, ensure thread is not interrupted.  This
                            // requires a recheck in second case to deal with
                            // shutdownNow race while clearing interrupt
                            if ((runStateAtLeast(ctl.get(), STOP) ||
                                (Thread.interrupted() &&
                                runStateAtLeast(ctl.get(), STOP))) &&
                                !wt.isInterrupted())
                                wt.interrupt();
                            try {
                                beforeExecute(wt, task);
                                Throwable thrown = null;
                                try {
                                    task.run();
                                } catch (RuntimeException x) {
                                    thrown = x; throw x;
                                } catch (Error x) {
                                    thrown = x; throw x;
                                } catch (Throwable x) {
                                    thrown = x; throw new Error(x);
                                } finally {
                                    afterExecute(task, thrown);
                                }
                            } finally {
                                task = null;
                                w.completedTasks++;
                                w.unlock();
                            }
                        }
                        completedAbruptly = false;
                    } finally {
                        processWorkerExit(w, completedAbruptly);
                    }
                }

                ...
            }
            ```
    + ### 状态转换

        ```java
        private void advanceRunState(int targetState) {
            for (;;) {
                int c = ctl.get();
                // 如果小于当前状态，没必要设置。比如当前为TIDYING，要设置 SHUTDOWN 就没必要
                // 否则将 工作线程数 + 目标状态 封装为一个新的 ctl 并赋值
                if (runStateAtLeast(c, targetState) ||                              
                    ctl.compareAndSet(c, ctlOf(targetState, workerCountOf(c))))
                    break;
            }
        }
        ```
        ![](/.images/doc/base/thread/thread-pool/tp-status-01.png ':size=80%')

    + ### 定义
    + ### 创建
    + ### 种类

* ## Reference

    + https://github.com/openjdk/jdk/blob/jdk8-b120/jdk/src/share/classes/java/util/concurrent/ThreadPoolExecutor.java
    + https://drive.google.com/file/d/1IvKzyPDZEnyhSu0Q2GfylXu07EPpjV91/view?usp=sharing