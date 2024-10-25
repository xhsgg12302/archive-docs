* ## TCP

* ## UDP

* ## InetAddress

    + ### 流程解析

        > [?] 根据域名实例化一个InetAddress对象的流程解析。
        <br>比如：`InetAddress byName = InetAddress.getByName("wtfu.site");`。如果是域名的话，会涉及到系统调用`getaddrinfo`.里面包括DNS解析。
        <br><br>1. `getAllByName(String host, InetAddress reqAddr)`
        <br>2. 不属于ip地址的话，会继续进入到`getAllByName0()`
        <br>3. `addresses = getAddressesFromNameService(host, reqAddr);`
        <br>4. `addresses = nameService.lookupAllHostAddr(host);`
        <br>5. `impl.lookupAllHostAddr(host); // impl为Inet6AddressImpl类型，且lookupAllHostAddr为native方法。`
        <br>6. 定位到c源码后如下：可以看到关键的`getaddrinfo`系统调用。

        ```c
        //src/java.base/unix/native/libnet/Inet6AddressImpl.c
        JNIEXPORT jobjectArray JNICALL
        Java_java_net_Inet6AddressImpl_lookupAllHostAddr(JNIEnv *env, jobject this,
                                                        jstring host, jint characteristics) {
            error = getaddrinfo(hostname, NULL, &hints, &res);
        }
        ```
    
    + ### 测试系统调用流程

        1. 编写样例代码

            ```c
            #include <sys/types.h>
            #include <sys/socket.h>
            #include <netdb.h>
            #include <stdio.h>
            #include <sys/socket.h>
            #include <netinet/in.h>
            #include <arpa/inet.h>
            #include <string.h>

            int main(int argc, char * argv[])
            {
                struct addrinfo hints, *res;
                memset (&hints, 0, sizeof (hints));
                hints.ai_family = AF_INET;
                hints.ai_flags = AI_CANONNAME;

                if (0 > getaddrinfo("wtfu.site", NULL, &hints, &res)){
                    perror("get error:");
                    return -1;
                }
                struct sockaddr_in * get_addr = res->ai_addr;
                printf("ip for wtfu: %s\n", inet_ntoa(get_addr->sin_addr));
                return 0;
            }
            ```

        2. 追踪系统调用

            > [?] 使用`gcc -o test test.c`编译源码，然后使用`strace ./test` 追踪调用。按顺序摘选输出：
            <br>可以看到从`/etc/resolv.conf`中读取到`nameserver`,然后使用`53`端口进行连接查询。

            ```shell
            execve("./test", ["./test"], 0x7ffe34b4bc10 /* 23 vars */) = 0
            open("/etc/nsswitch.conf", O_RDONLY|O_CLOEXEC) = 3
            fstat(3, {st_mode=S_IFREG|0644, st_size=1746, ...}) = 0
            mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f553499f000
            read(3, "#\n# /etc/nsswitch.conf\n#\n# An ex"..., 4096) = 1746
            read(3, "", 4096)                       = 0
            close(3)                                = 0
            munmap(0x7f553499f000, 4096)            = 0
            stat("/etc/resolv.conf", {st_mode=S_IFREG|0644, st_size=89, ...}) = 0
            open("/etc/host.conf", O_RDONLY|O_CLOEXEC) = 3
            fstat(3, {st_mode=S_IFREG|0644, st_size=9, ...}) = 0
            mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f553499f000
            read(3, "multi on\n", 4096)             = 9
            read(3, "", 4096)                       = 0
            close(3)                                = 0
            munmap(0x7f553499f000, 4096)            = 0
            open("/etc/resolv.conf", O_RDONLY|O_CLOEXEC) = 3
            fstat(3, {st_mode=S_IFREG|0644, st_size=89, ...}) = 0
            mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f553499f000
            read(3, "; generated by /usr/sbin/dhclien"..., 4096) = 89
            read(3, "", 4096)                       = 0
            close(3)                                = 0
            mprotect(0x7f55343aa000, 4096, PROT_READ) = 0
            munmap(0x7f5534998000, 30945)           = 0
            open("/etc/hosts", O_RDONLY|O_CLOEXEC)  = 3
            fstat(3, {st_mode=S_IFREG|0644, st_size=201, ...}) = 0
            mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f553499f000
            read(3, "127.0.0.1 12302 12302\n127.0.0.1 "..., 4096) = 201
            read(3, "", 4096)                       = 0
            close(3)                                = 0
            ...
            socket(AF_INET, SOCK_DGRAM|SOCK_CLOEXEC|SOCK_NONBLOCK, IPPROTO_IP) = 3
            setsockopt(3, SOL_IP, IP_RECVERR, [1], 4) = 0
            connect(3, {sa_family=AF_INET, sin_port=htons(53), sin_addr=inet_addr("183.60.82.98")}, 16) = 0
            poll([{fd=3, events=POLLOUT}], 1, 0)    = 1 ([{fd=3, revents=POLLOUT}])
            sendto(3, "\342\377\1\0\0\1\0\0\0\0\0\0\4wtfu\4site\0\0\1\0\1", 27, MSG_NOSIGNAL, NULL, 0) = 27
            poll([{fd=3, events=POLLIN}], 1, 5000)  = 1 ([{fd=3, revents=POLLIN}])
            ioctl(3, FIONREAD, [43])                = 0
            recvfrom(3, "\342\377\201\200\0\1\0\1\0\0\0\0\4wtfu\4site\0\0\1\0\1\300\f\0\1\0"..., 1024, 0, {sa_family=AF_INET, sin_port=htons(53), sin_addr=inet_addr("183.60.82.98")}, [28->16]) = 43
            close(3)                                = 0
            fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(136, 1), ...}) = 0
            mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f553499f000
            write(1, "ip for wtfu: 139.155.77.112\n", 28ip for wtfu: 139.155.77.112
            ) = 28
            exit_group(0)                           = ?
            +++ exited with 0 +++
            ```

    + **Reference**

        - https://man7.org/linux/man-pages/man3/getaddrinfo.3.html
        - https://www.cnblogs.com/Iflyinsky/p/17520932.html


## socket

* ### http impl

    <!-- tabs:start -->
    #### **Server**
    ```java
    public class Server {
        public static void main(String[] args) {
            // You can use print statements as follows for debugging, they'll be visible when running tests.
            System.out.println("Logs from your program will appear here!");
            ServerSocket serverSocket = null;
            Socket clientSocket = null;

            try {
                serverSocket = new ServerSocket(4221);
                serverSocket.setReuseAddress(true);

                while(true) {
                    clientSocket = serverSocket.accept(); // Wait for connection from client.
                    System.out.println("accepted new connection");

                    Socket finalClientSocket = clientSocket;
                    new Thread(() -> {
                        // 必须关闭，不关闭的话，容易资源泄露，并且curl 会等待。
                        try(finalClientSocket;
                            InputStream input = finalClientSocket.getInputStream();
                            OutputStream output = finalClientSocket.getOutputStream()){

                            BufferedReader in = new BufferedReader(new InputStreamReader(input));
                            String startLine = in.lines().findFirst().get();
                            String path = startLine.split(" ")[1];

                            StringBuilder response = new StringBuilder();
                            if ("/".equals(path)) {
                                response.append("HTTP/1.1 200 OK\n");
                            } else {
                                response.append("HTTP/1.1 404 Not Found\n");
                            }

                            response.append("Content-Type: text/plain\n");
                            //response.append("Connection: keep-alive\n");
                            response.append("Connection: close\n");

                            response.append("\n");
                            response.append("hello world");

                            String responseStr = response.toString();
                            output.write(responseStr.getBytes(StandardCharsets.UTF_8));
                            output.flush();

                            System.out.println("This message is sent to the client: \n" + responseStr);
                        }catch (Exception e){e.printStackTrace();}
                    }).start();
                }
            } catch (Exception e) {
                System.out.println("IOException: " + e.getMessage());
            } finally {
                if (serverSocket != null) {
                    try {
                        serverSocket.close();
                    } catch (IOException e) {
                        throw new RuntimeException(e);
                    }
                }
            }
        }
    }
    ```
    #### **Client**
    ```java
    public class Client {
        public static void main(String[] args) throws Exception {

            //Instantiate a new socket
            try (Socket s = new Socket("localhost", 4221);
                OutputStream outputStream = s.getOutputStream();
                InputStream inputStream = s.getInputStream()) {
                //Prints the request string to the output stream
                StringBuilder req = new StringBuilder();
                req.append("GET / HTTP/1.1\r\n");
                req.append("Host: localhost\r\n");
                req.append("\r\n");

                outputStream.write(req.toString().getBytes(StandardCharsets.UTF_8));

                //Creates a BufferedReader that contains the server response
                BufferedReader bufRead = new BufferedReader(new InputStreamReader(inputStream));
                String outStr;

                //Prints each line of the response
                while((outStr = bufRead.readLine()) != null){
                    System.out.println(outStr);
                }
            }catch (Exception e){e.printStackTrace();}
        }
    }
    ```
    <!-- tabs:end -->

## channelsocket


## Reference
* https://stackoverflow.com/questions/23184845/dont-understand-character-digit-in-java
* https://stackoverflow.com/questions/27014955/socket-connect-vs-bind