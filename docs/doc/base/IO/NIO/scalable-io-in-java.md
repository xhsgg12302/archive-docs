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

## Reference
* https://gee.cs.oswego.edu/dl/cpjslides/nio.pdf