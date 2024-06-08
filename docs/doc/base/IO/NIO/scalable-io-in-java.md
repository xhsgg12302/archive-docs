* ## Intro(Scalable IO in Java)

    1. Scalable network services
    2. Event-driven processing
    3. Reactor pattern
        1. Basic version
        2. Multithreaded versions
        3. Other variants
    4. Walkthrough of java.nio nonblocking IO APIs

    + ### Network Services

        > [!TIP|style:flat|label:|iconVisibility:hidden] **Web services, Distributed Objects, etc. Most have same basic structure:**
        <br>&nbsp;&nbsp;&nbsp;`Read request` ➠ `Decode request` ➠ `Process service` ➠ `Encode reply` ➠ `Send reply`
        <br>**" But differ in nature and cost of each step XML parsing, File transfer, Web page generation, computational services, ...**

        - #### Classic Service Designs

            <!-- panels:start -->
            <!-- div:left-panel-50 -->
            ![](/.images/doc/base/io/nio/scalable-io-in-java/siij-01-classic.png 'Classic Service Designs :size=100% :align=center')

            <!-- div:right-panel-50 -->
            ```java
            public class Server implements Runnable{

                private static final int PORT = 12302;

                @Override
                public void run() {
                    try {
                        ServerSocket ss = new ServerSocket(PORT);
                        while(!Thread.interrupted()){
                            new Thread(new Handler(ss.accept())).start();
                        }
                    } catch (Exception e) { /* ... */}
                }

                static class Handler implements Runnable{
                    private final Socket socket;
                    public Handler(Socket socket){
                        this.socket = socket;
                    }
                    @Override
                    public void run() {
                        try {
                            byte[] input = new byte[32];
                            socket.getInputStream().read(input);
                            byte[] output = process(input);
                            socket.getOutputStream().write(output);
                            //socket.close();
                        } catch (Exception e) { /* ... */}
                    }

                    private byte[] process(byte[] cmd) throws IOException{
                        System.out.println(new String(cmd, StandardCharsets.UTF_8));
                        return cmd;
                    }
                }

                public static void main(String[] args) {
                    new Thread(new Server()).start();
                }
            }
            ```
            <!-- panels:end -->
        
        - #### Scalability Goals

            1. **Graceful degradation under increasing load(more clients)**
            2. **Continuous improvement with increasing resources (CPU, memory, disk, bandwidth)**
            3. **Also meet availability and performance goals**
                1. *Short latencies*
                2. *Meeting peak demand*
                3. *Tunable quality of service*
            4. **Divide-and-conquer is usually the best approach for achieving any scalability goal**

        - #### Divide and Conquer
            
            * __Divide processing into small tasks__, Each task performs an action without blocking
            * __Execute each task when it is enabled__
            
                Here, an IO event usually serves as trigger

                ![](/.images/doc/base/io/nio/scalable-io-in-java/siij-02-each-task.png ':size=40%')
            * __Basic mechanisms supported in java.nio__
                1. _Non-blocking reads and writes_
                2. _Dispatch tasks associated with sensed IO events_
            * __Endless variation possible__, A family of event-driven designs

    + ### Event-driven Designs

        * Usually more efficient than alternatives

            `Fewer resources`(Don't usually need a thread per client)
            <br>`Less overhead`(Less context switching, often less locking)
            <br>`But dispatching can be slower`(Must manually bind actions to events)

        * Usually harder to program

            `Must break up into simple non-blocking actions`
            
            1. Similar to GUI event-driven actions
            2. Cannot eliminate all blocking: GC, page faults, etc
        

            `Must keep track of logical state of service`

        * #### Background: Events in AWT

            <!-- panels:start -->
            <!-- div:left-panel-50 -->
            ![](/.images/doc/base/io/nio/scalable-io-in-java/siij-03-event-in-AWT.png ':size=100%')
            <!-- div:right-panel-50 -->
            ```java
            public class SimpleAWTExample {
                public SimpleAWTExample() {
                    // 创建窗口
                    Frame frame = new Frame("AWT示例");
                    frame.setSize(300, 150);
                    frame.addWindowListener(new WindowAdapter() {public void windowClosing(WindowEvent e) { System.exit(0);}});
                    // 创建文本框
                    TextField textField = new TextField(20);
                    frame.add(textField);
                    // 创建按钮
                    Button button = new Button("点击我");
                    button.addActionListener(new ActionListener() {
                        public void actionPerformed(ActionEvent e) {
                            textField.setText("Hello, World!");
                        }
                    });
                    frame.add(button);
                    // 设置布局
                    frame.setLayout(new FlowLayout());
                    // 显示窗口
                    frame.setVisible(true);
                }

                public static void main(String[] args) { new SimpleAWTExample();}
            }
            ```
            <!-- panels:end -->
        
    + ### Reactor Pattern

        > [!TIP|style:flat|label:|iconVisibility:hidden]
        <br>1. Reactor responds to IO events by dispatching the appropriate handler`(Similar to AWT thread)`
        <br>2. Handlers perform non-blocking actions`(Similar to AWT ActionListeners)`
        <br>3. Manage by binding handlers to events`(Similar to AWT addActionListener)`
        <br><br>See Schmidt et al, Pattern-Oriented Software Architecture, Volume 2 (POSA2). Also Richard Stevens's networking books, Matt Welsh's SEDA framework, etc.
        
        - #### Basic Reactor Design

            ![](/.images/doc/base/io/nio/scalable-io-in-java/siij-04-basic-reactor-design.png ':size=57%')

            <!-- panels:start -->
            <!-- div:left-panel-40 -->
            > [?] `Reactor` and `Acceptor`
            ```java
            public class Reactor {

                final Selector selector;
                final ServerSocketChannel serverSocket;

                // Reactor 1: Setup
                Reactor(int port) throws IOException {

                    /*
                        Alternatively, use explicit SPI provider:
                        SelectorProvider p = SelectorProvider.provider();
                        selector = p.openSelector();
                        serverSocket = p.openServerSocketChannel();
                    */
                    selector = Selector.open();
                    serverSocket = ServerSocketChannel.open();

                    serverSocket.socket().bind(new InetSocketAddress(port));
                    serverSocket.configureBlocking(false);

                    SelectionKey sk = serverSocket.register(selector, SelectionKey.OP_ACCEPT);
                    sk.attach(new Acceptor());
                }

                // Reactor 2: Dispatch Loop
                public void run() { // normally in a new Thread
                    try {
                        while (!Thread.interrupted()) {
                            selector.select();
                            Set selected = selector.selectedKeys();
                            Iterator it = selected.iterator();
                            while (it.hasNext())
                                dispatch((SelectionKey)(it.next()));
                            selected.clear();
                        }
                    } catch (IOException ex) { /* ... */ }
                }
                void dispatch(SelectionKey k) {
                    Runnable r = (Runnable)(k.attachment());
                    if (r != null)
                        r.run();
                }

                // Reactor 3: Acceptor
                class Acceptor implements Runnable { // inner
                    public void run() {
                        try {
                            SocketChannel c = serverSocket.accept();
                            if (c != null)
                                new Handler(selector, c);
                        }
                        catch(IOException ex) { /* ... */ }
                    }
                }
            }
            ```
            <!-- div:right-panel-60 -->
            > [?] Normal `Handler` and GoF `Handler`
            ```java
            final class Handler implements Runnable {

                // Reactor 4: Handler setup
                final SocketChannel socket;
                final SelectionKey sk;
                ByteBuffer input = ByteBuffer.allocate(1024);
                ByteBuffer output = ByteBuffer.allocate(1024);
                static final int READING = 0, SENDING = 1;
                int state = READING;

                Handler(Selector sel, SocketChannel c)
                        throws IOException {
                    socket = c;
                    c.configureBlocking(false);
                    // Optionally try first read now
                    sk = socket.register(sel, 0);
                    sk.attach(this);
                    sk.interestOps(SelectionKey.OP_READ);
                    sel.wakeup();
                }

                boolean inputIsComplete() { /* ... */ return true; }
                boolean outputIsComplete() { /* ... */ return true; }
                void process() { /* ... */}

                //run()
            }
            ```
            <!-- panels:start -->
            <!-- div:left-panel-30 -->
            ```java
            // Reactor 5: Request Handling
            @Override
            public void run() {
                try {
                    if (state == READING) read();
                    else if (state == SENDING) send();
                } catch (IOException ex) { /* ... */ }
            }

            void read() throws IOException {
                socket.read(input);
                if (inputIsComplete()) {
                    process();
                    state = SENDING;
                    // Normally also do first write now
                    sk.interestOps(SelectionKey.OP_WRITE);
                }
            }
            void send() throws IOException {
                input.flip();
                socket.write(input);
                if (outputIsComplete()) sk.cancel();
            }
            ```
            <!-- div:right-panel-30 -->
            ```java
            // Per-State Handlers (A simple use of GoF State-Object pattern)
            // Rebind appropriate handler as attachment

            // Reactor 5: Request Handling
            @Override
            public void run() throws IOException { 
                // initial state is reader
                socket.read(input);
                if (inputIsComplete()) {
                    process();
                    sk.attach(new Sender());
                    sk.interestOps(SelectionKey.OP_WRITE);
                    sk.selector().wakeup();
                }
            }

            class Sender implements Runnable {
                @SneakyThrows
                public void run(){ // ...
                    socket.write(output);
                    if (outputIsComplete()) sk.cancel();
                }
            }
            ```
            <!-- panels:end -->
            <!-- panels:end -->

            ---

            > [!CAUTION|style:flat|label:为什么需要调用wakeup（）方法？] [ref](https://yiyan.baidu.com/share/PjR5tzcKWN '针对改变interestOps(感兴趣的操作集合)做详细说明，为什么改变后需要wakeup')
            <br>当你改变了一个通道的`interestOps`后，这个改变不会立即触发select()方法返回。这是因为select()方法是在等待至少有一个已注册的通道的状态变为就绪（即满足之前设置的 interestOps）时才会返回。
            <br>然而，如果你已经改变了某个通道的`interestOps`，并且你希望这个改变能够立即影响到select()方法的执行（例如:你希望 select()方法立即返回以检查新的 interestOps），那么你就需要调用`wakeup()`方法。
            <br>因此，改变 interestOps 后需要调用 wakeup() 的主要原因是，你希望这个改变能够立即影响到 select() 方法的执行，以便能够及时响应新的感兴趣的操作。如果不调用 wakeup()，那么 select() 方法可能会继续阻塞，直到某个已注册的通道的状态变为就绪，而这可能并不符合你的期望。

        - #### Multithreaded Designs

            * __Strategically add threads for scalability__ (Mainly applicable to multiprocessors)
            * __Worker Threads__ 
                1. Reactors should quickly trigger handlers. (Handler processing slows down Reactor)
                2. Offload non-IO processing to other threads.
            

                > [?] `Offload non-IO processing to speed up Reactor thread` (Similar to POSA2 Proactor designs)
                <br> `Simpler than reworking compute-bound processing into event-driven form` (Should still be pure nonblocking computation,
                Enough processing to outweigh overhead)
                <br>`But harder to overlap processing with IO` (Best when can first read all input into a buffer)
                <br>`Use thread pool so can tune and control` (Normally need many fewer threads than clients)

                ![](/.images/doc/base/io/nio/scalable-io-in-java/siij-05-worker-thread-pools.png ':size=57%')

                ```java
                class Handler implements Runnable {
                    // uses util.concurrent thread pool
                    static PooledExecutor pool = new PooledExecutor(...);
                    static final int PROCESSING = 3;
                    // ...
                    synchronized void read() { // ...
                        socket.read(input);
                        if (inputIsComplete()) {
                            state = PROCESSING;
                            pool.execute(new Processer());
                        }
                    }
                    synchronized void processAndHandOff() {
                        process();
                        state = SENDING; // or rebind attachment
                        sk.interest(SelectionKey.OP_WRITE);
                    }
                    class Processer implements Runnable {
                        public void run() { processAndHandOff(); }
                    }
                }
                ```

                <!-- panels:start -->
                <!-- div:left-panel-50 -->
                > [?] Coordinating Tasks
                1. __Handoffs__ (Each task enables, triggers, or calls next one Usually fastest but can be brittle)
                2. __Callbacks to per-handler dispatcher__
                    * Sets state, attachment, etc
                    * A variant of GoF Mediator pattern
                3. __Queues__ (For example, passing buffers across stages)
                4. __Futures__
                    * When each task produces a result
                    * Coordination layered on top of join or wait/notify

             
                ---
                <!-- div:right-panel-50 -->
                > [?] Using PooledExecutor
                1. __A tunable worker thread pool__
                2. __Main method execute(Runnable r)__
                3. __Controls for:__
                    * The kind of task queue (any Channel)
                    * Maximum number of threads
                    * Minimum number of threads
                    * "Warm" versus on-demand threads
                    * Keep-alive interval until idle threads die (to be later replaced by new ones if necessary)
                    * Saturation policy (block, drop, producer-runs, etc)

             
                ---
                <!-- panels:end -->

            * __Multiple Reactor Threads__ (Reactor threads can saturate doing IO Distribute load to other reactors, Load-balance to match CPU and IO rates)

                <!-- panels:start -->
                <!-- div:left-panel-50 -->
                ![](/.images/doc/base/io/nio/scalable-io-in-java/siij-06-using-multiple-reactors.png ':size=100%')
                <!-- div:right-panel-50 -->
                ![](/.images/doc/base/io/nio/scalable-io-in-java/siij-07-multiple-reactor-threads.png ':size=100%')
                <!-- panels:end -->

                <!-- panels:start -->
                <!-- div:title-panel -->
                ##### 
                <!-- div:left-panel-50 -->
                > [?] Using other java.nio features
                1. __Multiple Selectors per Reactor__ (To bind different handlers to different IO events, May need careful synchronization to coordinate)
                2. __File transfer__ (Automated file-to-net or net-to-file copying)
                3. __Memory-mapped files__ (Access files via buffers)
                4. __Direct buffers__
                    * Can sometimes achieve zero-copy transfer
                    * But have setup and finalization overhead
                    * Best for applications with long-lived connections

             
                ---
                <!-- div:right-panel-50 -->
                > [?] Connection-Based Extensions
                1. __Instead of a single service request__
                    * Client connects
                    * Client sends a series of messages/requests
                    * Client disconnects
                2. __Examples__
                    * Databases and Transaction monitors
                    * Multi-participant games, chat, etc
                3. __Can extend basic network service patterns__
                    * Handle many relatively long-lived clients
                    * Track client and session state (including drops)
                    * Distribute services across multiple hosts

             
                ---
                <!-- panels:end -->


## Reference
* https://gee.cs.oswego.edu/dl/cpjslides/nio.pdf