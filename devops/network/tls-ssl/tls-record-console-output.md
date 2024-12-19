---
draft : true
---

[wireshark capture package: instance-tls-ssl.pcapng](/.images/devops/network/tls-ssl/instance-tls-ssl.pcapng)，use filter：`ip.addr == 47.94.20.18 && tcp.port == 443`

```markup [data-cc:800px]
/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/bin/java -agentlib:jdwp=transport=dt_socket,address=127.0.0.1:53227,suspend=y,server=n -javaagent:/Users/stevenobelia/Library/Caches/JetBrains/IntelliJIdea2023.2/captureAgent/debugger-agent.jar -Dfile.encoding=UTF-8 -classpath /Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/charsets.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/deploy.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/cldrdata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/dnsns.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/jaccess.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/jfxrt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/localedata.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/nashorn.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/sunec.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/sunjce_provider.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/sunpkcs11.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/ext/zipfs.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/javaws.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/jce.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/jfr.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/jfxswt.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/jsse.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/management-agent.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/plugin.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/resources.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/jre/lib/rt.jar:/Users/stevenobelia/Documents/project_idea_test/idea-test-project/_4_springmvc/target/classes:/Users/stevenobelia/Documents/_maven_repo/javax/servlet/javax.servlet-api/3.0.1/javax.servlet-api-3.0.1.jar:/Users/stevenobelia/Documents/_maven_repo/javax/servlet/jstl/1.2/jstl-1.2.jar:/Users/stevenobelia/Documents/_maven_repo/org/springframework/spring-core/4.3.30.RELEASE/spring-core-4.3.30.RELEASE.jar:/Users/stevenobelia/Documents/_maven_repo/commons-logging/commons-logging/1.2/commons-logging-1.2.jar:/Users/stevenobelia/Documents/_maven_repo/org/springframework/spring-web/4.3.30.RELEASE/spring-web-4.3.30.RELEASE.jar:/Users/stevenobelia/Documents/_maven_repo/org/springframework/spring-beans/4.3.30.RELEASE/spring-beans-4.3.30.RELEASE.jar:/Users/stevenobelia/Documents/_maven_repo/org/springframework/spring-context/4.3.30.RELEASE/spring-context-4.3.30.RELEASE.jar:/Users/stevenobelia/Documents/_maven_repo/org/springframework/spring-oxm/4.3.30.RELEASE/spring-oxm-4.3.30.RELEASE.jar:/Users/stevenobelia/Documents/_maven_repo/org/springframework/spring-tx/4.3.30.RELEASE/spring-tx-4.3.30.RELEASE.jar:/Users/stevenobelia/Documents/_maven_repo/org/springframework/spring-jdbc/4.3.30.RELEASE/spring-jdbc-4.3.30.RELEASE.jar:/Users/stevenobelia/Documents/_maven_repo/org/springframework/spring-webmvc/4.3.30.RELEASE/spring-webmvc-4.3.30.RELEASE.jar:/Users/stevenobelia/Documents/_maven_repo/org/springframework/spring-expression/4.3.30.RELEASE/spring-expression-4.3.30.RELEASE.jar:/Users/stevenobelia/Documents/_maven_repo/org/springframework/spring-aop/4.3.30.RELEASE/spring-aop-4.3.30.RELEASE.jar:/Users/stevenobelia/Documents/_maven_repo/org/springframework/spring-context-support/4.3.30.RELEASE/spring-context-support-4.3.30.RELEASE.jar:/Users/stevenobelia/Documents/_maven_repo/org/springframework/spring-test/4.3.30.RELEASE/spring-test-4.3.30.RELEASE.jar:/Users/stevenobelia/Documents/_maven_repo/org/mybatis/mybatis/3.5.5/mybatis-3.5.5.jar:/Users/stevenobelia/Documents/_maven_repo/org/mybatis/mybatis-spring/2.0.5/mybatis-spring-2.0.5.jar:/Users/stevenobelia/Documents/_maven_repo/mysql/mysql-connector-java/5.1.42/mysql-connector-java-5.1.42.jar:/Users/stevenobelia/Documents/_maven_repo/com/zaxxer/HikariCP/4.0.3/HikariCP-4.0.3.jar:/Users/stevenobelia/Documents/_maven_repo/jstl/jstl/1.2/jstl-1.2.jar:/Users/stevenobelia/Documents/_maven_repo/log4j/log4j/1.2.17/log4j-1.2.17.jar:/Users/stevenobelia/Documents/_maven_repo/com/alibaba/fastjson/1.2.25.sec10/fastjson-1.2.25.sec10.jar:/Users/stevenobelia/Documents/_maven_repo/org/slf4j/slf4j-api/1.7.7/slf4j-api-1.7.7.jar:/Users/stevenobelia/Documents/_maven_repo/org/slf4j/slf4j-log4j12/1.7.7/slf4j-log4j12-1.7.7.jar:/Users/stevenobelia/Documents/_maven_repo/com/fasterxml/jackson/core/jackson-databind/2.9.5/jackson-databind-2.9.5.jar:/Users/stevenobelia/Documents/_maven_repo/com/fasterxml/jackson/core/jackson-annotations/2.14.2/jackson-annotations-2.14.2.jar:/Users/stevenobelia/Documents/_maven_repo/com/fasterxml/jackson/core/jackson-core/2.14.2/jackson-core-2.14.2.jar:/Users/stevenobelia/Documents/_maven_repo/commons-fileupload/commons-fileupload/1.3.3/commons-fileupload-1.3.3.jar:/Users/stevenobelia/Documents/_maven_repo/commons-io/commons-io/2.4/commons-io-2.4.jar:/Users/stevenobelia/Documents/_maven_repo/commons-codec/commons-codec/1.9/commons-codec-1.9.jar:/Users/stevenobelia/Documents/_maven_repo/org/apache/commons/commons-lang3/3.0/commons-lang3-3.0.jar:/Users/stevenobelia/Documents/_maven_repo/javax/annotation/jsr250-api/1.0/jsr250-api-1.0.jar:/Users/stevenobelia/Documents/_maven_repo/org/apache/poi/poi-ooxml/3.17/poi-ooxml-3.17.jar:/Users/stevenobelia/Documents/_maven_repo/org/apache/poi/poi-ooxml-schemas/3.17/poi-ooxml-schemas-3.17.jar:/Users/stevenobelia/Documents/_maven_repo/org/apache/xmlbeans/xmlbeans/2.6.0/xmlbeans-2.6.0.jar:/Users/stevenobelia/Documents/_maven_repo/stax/stax-api/1.0.1/stax-api-1.0.1.jar:/Users/stevenobelia/Documents/_maven_repo/com/github/virtuald/curvesapi/1.04/curvesapi-1.04.jar:/Users/stevenobelia/Documents/_maven_repo/org/apache/poi/poi/3.14/poi-3.14.jar:/Users/stevenobelia/Documents/_maven_repo/org/apache/commons/commons-collections4/4.1/commons-collections4-4.1.jar:/Users/stevenobelia/Documents/_maven_repo/net/sourceforge/jexcelapi/jxl/2.6.12/jxl-2.6.12.jar:/Users/stevenobelia/Documents/_maven_repo/org/projectlombok/lombok/1.16.22/lombok-1.16.22.jar:/Users/stevenobelia/Documents/_maven_repo/org/apache/httpcomponents/httpclient/4.5.14/httpclient-4.5.14.jar:/Users/stevenobelia/Documents/_maven_repo/org/apache/httpcomponents/httpcore/4.4.16/httpcore-4.4.16.jar:/Users/stevenobelia/Documents/_maven_repo/com/fasterxml/jackson/dataformat/jackson-dataformat-yaml/2.14.2/jackson-dataformat-yaml-2.14.2.jar:/Users/stevenobelia/Documents/_maven_repo/org/yaml/snakeyaml/1.33/snakeyaml-1.33.jar:/Users/stevenobelia/Documents/_maven_repo/org/java-websocket/Java-WebSocket/1.5.3/Java-WebSocket-1.5.3.jar:/Users/stevenobelia/Documents/_maven_repo/org/bouncycastle/bcprov-jdk15to18/1.78/bcprov-jdk15to18-1.78.jar:/Users/stevenobelia/Documents/_maven_repo/org/bouncycastle/bctls-debug-jdk15to18/1.78/bctls-debug-jdk15to18-1.78.jar:/Users/stevenobelia/Documents/_maven_repo/org/bouncycastle/bcutil-jdk15to18/1.78/bcutil-jdk15to18-1.78.jar:/Users/stevenobelia/Documents/_maven_repo/org/junit/jupiter/junit-jupiter/5.11.3/junit-jupiter-5.11.3.jar:/Users/stevenobelia/Documents/_maven_repo/org/junit/jupiter/junit-jupiter-api/5.11.3/junit-jupiter-api-5.11.3.jar:/Users/stevenobelia/Documents/_maven_repo/org/opentest4j/opentest4j/1.3.0/opentest4j-1.3.0.jar:/Users/stevenobelia/Documents/_maven_repo/org/junit/platform/junit-platform-commons/1.11.3/junit-platform-commons-1.11.3.jar:/Users/stevenobelia/Documents/_maven_repo/org/apiguardian/apiguardian-api/1.1.2/apiguardian-api-1.1.2.jar:/Users/stevenobelia/Documents/_maven_repo/org/junit/jupiter/junit-jupiter-params/5.11.3/junit-jupiter-params-5.11.3.jar:/Users/stevenobelia/Documents/_maven_repo/org/junit/jupiter/junit-jupiter-engine/5.11.3/junit-jupiter-engine-5.11.3.jar:/Users/stevenobelia/Documents/_maven_repo/org/junit/platform/junit-platform-engine/1.11.3/junit-platform-engine-1.11.3.jar:/Applications/IntelliJ IDEA.app/Contents/lib/idea_rt.jar site.wtfu.framework.utils.TLSTest
Connected to the target VM, address: '127.0.0.1:53227', transport: 'socket'
<mark class='under red'>16 03 01 00 fd 01 00 00 f9 03 03 3a 67 f5 2b 44 
11 e2 49 8e 32 6e 79 d2 ac 45 98 e8 1b 1c 74 94 
3d 0d 2c 22 be 71 58 28 b3 18 b4 20 83 ce 5a f0 
a0 a9 a0 a8 1e b2 b5 42 fc 2d db 66 03 2a c0 60 
4b 4e 04 e1 c4 1b 12 b2 5f d5 f1 a1 00 1e 13 03 
13 01 cc a9 c0 2b c0 23 c0 09 cc a8 c0 2f c0 27 
c0 13 cc aa 00 9e 00 67 00 33 00 ff 01 00 00 92 
00 17 00 00 00 16 00 00 00 2b 00 05 04 03 04 03 
03 00 0a 00 10 00 0e 00 1d 00 1e 00 17 00 18 01 
00 01 01 01 02 00 33 00 26 00 24 00 1d 00 20 54 
38 8f 34 90 97 4d ab 70 78 09 dd 48 2c 2d 70 bd 
f6 82 ea c1 7d 2d 29 31 3b 3d 64 27 69 93 22 00 
05 00 05 01 00 00 00 00 00 0d 00 30 00 2e 08 07 
08 08 04 03 05 03 06 03 08 04 08 05 08 06 08 09 
08 0a 08 0b 04 01 05 01 06 01 04 02 05 02 06 02 
03 03 03 01 03 02 02 03 02 01 02 02 00 0b 00 02 
01 00</mark>
void
<mark class='under green'>16 03 03 00 59 02 00 00 55 03 03 4e 4f 97 03 39 
fa 55 3b 42 5e 31 90 f8 25 8c 26 e2 a6 bc 43 00 
4b 12 9d b7 d6 5d 46 9c 16 8e 5b 20 d7 83 e1 70 
ff 7f 85 69 c7 d7 cf 65 fd 35 c2 95 c8 ec 81 dd 
79 0b e2 b5 f1 54 54 dd 9e c5 a9 29 c0 2b 00 00 
0d ff 01 00 01 00 00 0b 00</mark>                          // dismiss (04 03 00 01 02)
void
<mark class='under green'>16 03 03 0b 77 0b 00 0b 73 00 0b 70 00 04 07 30 
82 04 03 30 82 03 89 a0 03 02 01 02 02 10 0b a5 
7a 98 3f e4 c3 ae 18 ac 59 b0 b1 04 32 7f 30 0a 
06 08 2a 86 48 ce 3d 04 03 03 30 4b 31 0b 30 09 
06 03 55 04 06 13 02 41 54 31 10 30 0e 06 03 55 
04 0a 13 07 5a 65 72 6f 53 53 4c 31 2a 30 28 06 
03 55 04 03 13 21 5a 65 72 6f 53 53 4c 20 45 43 
43 20 44 6f 6d 61 69 6e 20 53 65 63 75 72 65 20 
53 69 74 65 20 43 41 30 1e 17 0d 32 34 30 39 32 
32 30 30 30 30 30 30 5a 17 0d 32 34 31 32 32 31 
32 33 35 39 35 39 5a 30 14 31 12 30 10 06 03 55 
04 03 13 09 77 74 66 75 2e 73 69 74 65 30 59 30 
13 06 07 2a 86 48 ce 3d 02 01 06 08 2a 86 48 ce 
3d 03 01 07 03 42 00 04 4a cd f6 13 8a a1 45 4b 
a1 b2 29 2f 8c 31 06 2a 7f 03 7f e0 cd be 38 9b 
c3 85 02 0e 08 13 fb 14 31 8c 52 9c 12 31 76 dd 
be bf 92 7a 7d 66 66 79 df 2e 90 18 2b 58 62 67 
51 57 82 5a 6c eb 11 56 a3 82 02 84 30 82 02 80 
30 1f 06 03 55 1d 23 04 18 30 16 80 14 0f 6b e6 
4b ce 39 47 ae f6 7e 90 1e 79 f0 30 91 92 c8 5f 
a3 30 1d 06 03 55 1d 0e 04 16 04 14 ee f5 a9 f7 
35 a7 22 36 8c b5 58 f4 03 78 2e 98 bd e6 fe c2 
30 0e 06 03 55 1d 0f 01 01 ff 04 04 03 02 07 80 
30 0c 06 03 55 1d 13 01 01 ff 04 02 30 00 30 1d 
06 03 55 1d 25 04 16 30 14 06 08 2b 06 01 05 05 
07 03 01 06 08 2b 06 01 05 05 07 03 02 30 49 06 
03 55 1d 20 04 42 30 40 30 34 06 0b 2b 06 01 04 
01 b2 31 01 02 02 4e 30 25 30 23 06 08 2b 06 01 
05 05 07 02 01 16 17 68 74 74 70 73 3a 2f 2f 73 
65 63 74 69 67 6f 2e 63 6f 6d 2f 43 50 53 30 08 
06 06 67 81 0c 01 02 01 30 81 88 06 08 2b 06 01 
05 05 07 01 01 04 7c 30 7a 30 4b 06 08 2b 06 01 
05 05 07 30 02 86 3f 68 74 74 70 3a 2f 2f 7a 65 
72 6f 73 73 6c 2e 63 72 74 2e 73 65 63 74 69 67 
6f 2e 63 6f 6d 2f 5a 65 72 6f 53 53 4c 45 43 43 
44 6f 6d 61 69 6e 53 65 63 75 72 65 53 69 74 65 
43 41 2e 63 72 74 30 2b 06 08 2b 06 01 05 05 07 
30 01 86 1f 68 74 74 70 3a 2f 2f 7a 65 72 6f 73 
73 6c 2e 6f 63 73 70 2e 73 65 63 74 69 67 6f 2e 
63 6f 6d 30 82 01 04 06 0a 2b 06 01 04 01 d6 79 
02 04 02 04 81 f5 04 81 f2 00 f0 00 75 00 76 ff 
88 3f 0a b6 fb 95 51 c2 61 cc f5 87 ba 34 b4 a4 
cd bb 29 dc 68 42 0a 9f e6 67 4c 5a 3a 74 00 00 
01 92 18 a0 0e eb 00 00 04 03 00 46 30 44 02 20 
60 ae ca 02 8d ce 11 be 52 af 5b 76 88 a0 a8 63 
2b d2 1c 27 24 8d ca e8 72 d0 88 8f 65 c9 b2 c9 
02 20 78 e3 ab fa b7 02 6e df 83 8f 91 5f 0b 9f 
a4 95 1d bd 4b 60 1a 5f 1c 21 52 04 b3 75 38 53 
5d 38 00 77 00 da b6 bf 6b 3f b5 b6 22 9f 9b c2 
bb 5c 6b e8 70 91 71 6c bb 51 84 85 34 bd a4 3d 
30 48 d7 fb ab 00 00 01 92 18 a0 0e f4 00 00 04 
03 00 48 30 46 02 21 00 df a0 af be 8b 27 a0 0a 
fc a5 de 4a 88 4f c0 f8 7b e4 c2 79 b5 a2 3d f5 
fb 18 02 fd 23 f6 ef 92 02 21 00 af 1f 6b 00 2e 
57 96 46 f7 9e 16 bb de fc 9c 4c c5 2b b8 dd 22 
af 6e 70 32 b5 13 75 cb 75 00 c8 30 23 06 03 55 
1d 11 04 1c 30 1a 82 09 77 74 66 75 2e 73 69 74 
65 82 0d 77 77 77 2e 77 74 66 75 2e 73 69 74 65 
30 0a 06 08 2a 86 48 ce 3d 04 03 03 03 68 00 30 
65 02 30 05 e3 a7 f8 33 0f 4e b1 62 4c 83 bb 21 
27 9f 1e b6 8c d4 3a 05 68 28 4e e2 21 68 ed 05 
dc 7c 00 75 c2 b6 10 97 a0 21 83 d3 02 89 34 d4 
6e 61 31 02 31 00 92 6e 16 04 c0 1e 20 f5 99 17 
72 b6 33 eb e7 5f 32 46 03 68 6c 6f 05 5a 90 3a 
82 89 90 98 05 00 8c 64 6c ae 68 5c d1 77 8d 74 
e1 f1 2f ea db 51 00 03 89 30 82 03 85 30 82 03 
0c a0 03 02 01 02 02 10 23 b7 6d e3 c1 bb 2b 1a 
51 96 1e 08 ea b7 64 e8 30 0a 06 08 2a 86 48 ce 
3d 04 03 03 30 81 88 31 0b 30 09 06 03 55 04 06 
13 02 55 53 31 13 30 11 06 03 55 04 08 13 0a 4e 
65 77 20 4a 65 72 73 65 79 31 14 30 12 06 03 55 
04 07 13 0b 4a 65 72 73 65 79 20 43 69 74 79 31 
1e 30 1c 06 03 55 04 0a 13 15 54 68 65 20 55 53 
45 52 54 52 55 53 54 20 4e 65 74 77 6f 72 6b 31 
2e 30 2c 06 03 55 04 03 13 25 55 53 45 52 54 72 
75 73 74 20 45 43 43 20 43 65 72 74 69 66 69 63 
61 74 69 6f 6e 20 41 75 74 68 6f 72 69 74 79 30 
1e 17 0d 32 30 30 31 33 30 30 30 30 30 30 30 5a 
17 0d 33 30 30 31 32 39 32 33 35 39 35 39 5a 30 
4b 31 0b 30 09 06 03 55 04 06 13 02 41 54 31 10 
30 0e 06 03 55 04 0a 13 07 5a 65 72 6f 53 53 4c 
31 2a 30 28 06 03 55 04 03 13 21 5a 65 72 6f 53 
53 4c 20 45 43 43 20 44 6f 6d 61 69 6e 20 53 65 
63 75 72 65 20 53 <mark class='box blue'>69</mark> 74 65 20 43 41 30 76 30 10
06 07 2a 86 48 ce 3d 02 01 06 05 2b 81 04 00 22 
03 62 00 04 36 41 61 17 2b 53 25 ed aa ca 94 e4</mark>
void
<mark class='under green'>d6 da 48 57 ef 50 ba 84 64 82 d7 bb 05 1b d6 1f 
06 24 f6 a5 33 9d 8c e7 f1 0b 55 68 63 82 30 10 
5f 8d 65 ec aa a8 af 97 ca b5 86 ce 30 01 89 74 
de e3 4e 5e 01 6e ee 26 7b cc 53 fa 23 a4 f7 44 
1d 3e 4d 1e 5f 66 a6 ad 85 f6 f2 e3 bc 8e 09 98 
80 24 8e 20 a3 82 01 75 30 82 01 71 30 1f 06 03 
55 1d 23 04 18 30 16 80 14 3a e1 09 86 d4 cf 19 
c2 96 76 74 49 76 dc e0 35 c6 63 63 9a 30 1d 06 
03 55 1d 0e 04 16 04 14 0f 6b e6 4b ce 39 47 ae 
f6 7e 90 1e 79 f0 30 91 92 c8 5f a3 30 0e 06 03 
55 1d 0f 01 01 ff 04 04 03 02 01 86 30 12 06 03 
55 1d 13 01 01 ff 04 08 30 06 01 01 ff 02 01 00 
30 1d 06 03 55 1d 25 04 16 30 14 06 08 2b 06 01 
05 05 07 03 01 06 08 2b 06 01 05 05 07 03 02 30 
22 06 03 55 1d 20 04 1b 30 19 30 0d 06 0b 2b 06 
01 04 01 b2 31 01 02 02 4e 30 08 06 06 67 81 0c 
01 02 01 30 50 06 03 55 1d 1f 04 49 30 47 30 45 
a0 43 a0 41 86 3f 68 74 74 70 3a 2f 2f 63 72 6c 
2e 75 73 65 72 74 72 75 73 74 2e 63 6f 6d 2f 55 
53 45 52 54 72 75 73 74 45 43 43 43 65 72 74 69 
66 69 63 61 74 69 6f 6e 41 75 74 68 6f 72 69 74 
79 2e 63 72 6c 30 76 06 08 2b 06 01 05 05 07 01 
01 04 6a 30 68 30 3f 06 08 2b 06 01 05 05 07 30 
02 86 33 68 74 74 70 3a 2f 2f 63 72 74 2e 75 73 
65 72 74 72 75 73 74 2e 63 6f 6d 2f 55 53 45 52 
54 72 75 73 74 45 43 43 41 64 64 54 72 75 73 74 
43 41 2e 63 72 74 30 25 06 08 2b 06 01 05 05 07 
30 01 86 19 68 74 74 70 3a 2f 2f 6f 63 73 70 2e 
75 73 65 72 74 72 75 73 74 2e 63 6f 6d 30 0a 06 
08 2a 86 48 ce 3d 04 03 03 03 67 00 30 64 02 30 
24 70 54 0f 01 c9 40 dd c8 54 d9 6d 54 ca c8 08 
ca 98 43 74 d8 3f f4 d7 a9 5f 6d f2 61 b9 70 0a 
26 1b 63 30 a8 8b 31 9c bf 77 ec 67 b0 7f a5 88 
02 30 25 ad ab a4 b0 ee 8d 52 e0 dd 0d 7c 9d df 
7d 1d ae e2 5c 64 9c 74 f8 7e 63 e5 c1 4e 60 16 
86 b0 a7 5e 19 6e ec 08 c6 91 d8 fb 03 14 a1 a5 
95 ab 00 03 d7 30 82 03 d3 30 82 02 bb a0 03 02 
01 02 02 10 56 67 1d 04 ea 4f 99 4c 6f 10 81 47 
59 d2 75 94 30 0d 06 09 2a 86 48 86 f7 0d 01 01 
0c 05 00 30 7b 31 0b 30 09 06 03 55 04 06 13 02 
47 42 31 1b 30 19 06 03 55 04 08 0c 12 47 72 65 
61 74 65 72 20 4d 61 6e 63 68 65 73 74 65 72 31 
10 30 0e 06 03 55 04 07 0c 07 53 61 6c 66 6f 72 
64 31 1a 30 18 06 03 55 04 0a 0c 11 43 6f 6d 6f 
64 6f 20 43 41 20 4c 69 6d 69 74 65 64 31 21 30 
1f 06 03 55 04 03 0c 18 41 41 41 20 43 65 72 74 
69 66 69 63 61 74 65 20 53 65 72 76 69 63 65 73 
30 1e 17 0d 31 39 30 33 31 32 30 30 30 30 30 30 
5a 17 0d 32 38 31 32 33 31 32 33 35 39 35 39 5a 
30 81 88 31 0b 30 09 06 03 55 04 06 13 02 55 53 
31 13 30 11 06 03 55 04 08 13 0a 4e 65 77 20 4a 
65 72 73 65 79 31 14 30 12 06 03 55 04 07 13 0b 
4a 65 72 73 65 79 20 43 69 74 79 31 1e 30 1c 06 
03 55 04 0a 13 15 54 68 65 20 55 53 45 52 54 52 
55 53 54 20 4e 65 74 77 6f 72 6b 31 2e 30 2c 06 
03 55 04 03 13 25 55 53 45 52 54 72 75 73 74 20 
45 43 43 20 43 65 72 74 69 66 69 63 61 74 69 6f 
6e 20 41 75 74 68 6f 72 69 74 79 30 76 30 10 06 
07 2a 86 48 ce 3d 02 01 06 05 2b 81 04 00 22 03 
62 00 04 1a ac 54 5a a9 f9 68 23 e7 7a d5 24 6f 
53 c6 5a d8 4b ab c6 d5 b6 d1 e6 73 71 ae dd 9c 
d6 0c 61 fd db a0 89 03 b8 05 14 ec 57 ce ee 5d 
3f e2 21 b3 ce f7 d4 8a 79 e0 a3 83 7e 2d 97 d0 
61 c4 f1 99 dc 25 91 63 ab 7f 30 a3 b4 70 e2 c7 
a1 33 9c f3 bf 2e 5c 53 b1 5f b3 7d 32 7f 8a 34 
e3 79 79 a3 81 f2 30 81 ef 30 1f 06 03 55 1d 23 
04 18 30 16 80 14 a0 11 0a 23 3e 96 f1 07 ec e2 
af 29 ef 82 a5 7f d0 30 a4 b4 30 1d 06 03 55 1d 
0e 04 16 04 14 3a e1 09 86 d4 cf 19 c2 96 76 74 
49 76 dc e0 35 c6 63 63 9a 30 0e 06 03 55 1d 0f 
01 01 ff 04 04 03 02 01 86 30 0f 06 03 55 1d 13 
01 01 ff 04 05 30 03 01 01 ff 30 11 06 03 55 1d 
20 04 0a 30 08 30 06 06 04 55 1d 20 00 30 43 06 
03 55 1d 1f 04 3c 30 3a 30 38 a0 36 a0 34 86 32 
68 74 74 70 3a 2f 2f 63 72 6c 2e 63 6f 6d 6f 64 
6f 63 61 2e 63 6f 6d 2f 41 41 41 43 65 72 74 69 
66 69 63 61 74 65 53 65 72 76 69 63 65 73 2e 63 
72 6c 30 34 06 08 2b 06 01 05 05 07 01 01 04 28 
30 26 30 24 06 08 2b 06 01 05 05 07 30 01 86 18 
68 74 74 70 3a 2f 2f 6f 63 73 70 2e 63 6f 6d 6f 
64 6f 63 61 2e 63 6f 6d 30 0d 06 09 2a 86 48 86 
f7 0d 01 01 0c 05 00 03 82 01 01 00 19 ec eb 9d 
89 2c 20 0b 04 80 1d 18 de 42 99 72 99 16 32 bd 
0e 9c 75 5b 2c 15 e2 29 40 6d ee ff 72 db db ab 
90 1f 8c 95 f2 8a 3d 08 72 42 89 50 07 e2 39 15 
6c 01 87 d9 16 1a f5 c0 75 2b c5 e6 56 11 07 df 
d8 98 bc 7c 9f 19 39 df 8b ca <mark class='box blue'>00</mark> 64 73 bc 46 10
9b 93 23 8d be 16 c3 2e 08 82 9c 86 33 74 76 3b 
28 4c 8d 03 42 85 b3 e2 b2 23 42 d5 1f 7a 75 6a 
1a d1 7c aa 67 21 c4 33 3a 39 6d 53 c9 a2 ed 62 
22 a8 bb e2 55 6c 99 6c 43 6b 91 97 d1 0c 0b 93 
02 1d d2 bc 69 77 49 e6 1b 4d f7 bf 14 78 03 b0 
a6 ba 0b b4 e1 85 7f 2f dc 42 3b ad 74 01 48 de 
d6 6c e1 19 98 09 5e 0a b3 67 47 fe 1c e0 d5 c1 
28 ef 4a 8b 44 31 26 04 37 8d 89 74 36 2e ef a5 
22 0f 83 74 49 92 c7 f7 10 c2 0c 29 fb b7 bd ba 
7f e3 5f d5 9f f2 a9 f4 74 d5 b8 e1 b3 b0 81 e4 
e1 a5 63 a3 cc ea 04                                // dismiss (78 90 6e bf f7)
16 03 03 00 93 0c 00 00 8f 03 00 17 41 04 b2 9d 
da 3c 48 fc b4 1b f2 d5 2a 8e e1 fe 50 c3 e0 7a 
1a 25 9f f0 de fe 12 8d b5 24 2f f3 ac 79 7a e7 
db 1a 7d 55 79 41 c1 f4 31 75 5d d8 64 8c c1 38 
0c 6e a8 91 2e c1 c2 98 4a c9 77 a8 33 7b 06 03 
00 46 30 44 02 20 4d 72 54 2a 6c 0f 82 02 c9 3e 
cf a0 df 0f f4 36 ac d7 52 73 68 31 02 8c bd c1 
48 69 20 5d 4b 2e 02 20 6c 70 35 34 b2 b3 79 66 
4e f0 19 14 46 76 43 5b 6c e4 60 ff 6d 80 d6 ed 
f2 fb 74</mark>                                            // dismiss 91 bf 6b 44 11
void
<mark class='under green'>16 03 03 00                                         // dismiss 04 0e 00 00 00
</mark>
void
<mark class='under red'>16 03 03 00 46 10 00 00 42 41 04 bb 8a 9f 88 82 
df 83 95 ec 48 a0 a0 d6 89 57 73 da 8f 11 de 3c 
06 77 1d e9 a9 75 80 a1 aa 4b 38 da e4 22 18 38 
be 32 6a 91 47 03 71 51 72 83 df be c5 7f 85 da 
c8 44 40 0c 2f 8a 7a db 03 4b db</mark>
void
<mark class='under red'>14 03 03 00 01 01</mark>
void
<mark class='under red'>16 03 03 00 28 00 00 00 00 00 00 00 00 12 aa cc 
d7 92 f7 d5 c1 0f fc 7f 8e 7f 3c 17 33 01 d1 a7 
8e 71 a3 63 24 d2 83 5b 98 30 48 0d 91</mark>
void
<mark class='under green'>14</mark>
void
<mark class='under green'>16 03 03 00 28 3e b7 b5 ea dd e0 9e 42 1f 4a 38 
51 22 d5 b2 8f 99 61 fa fc 1c 53 0c 45 e2 5b b4 
51 5d 7b e1 98 d1 4a 0e</mark>
void
<mark class='under red'>17 03 03 00 fa 00 00 00 00 00 00 00 01 0c 1f ff 
af 02 be 77 01 49 50 e2 88 4a 1d 19 e6 7a b1 4b 
03 ef cc 75 79 6a 59 f0 29 84 a8 28 13 20 e2 4a 
18 2f 54 9d 22 08 a2 02 d8 a0 49 c9 c0 1f 29 1e 
0e bb e6 b2 f2 48 92 39 a1 87 9f 56 6a f6 6d ea 
45 b7 89 92 34 e1 80 b9 7d 41 00 cd ad 65 57 3c 
5f a5 6a 3a a6 e8 d4 e6 f7 dc 3f e3 15 b6 51 3e 
97 8a 6a 8f c8 2a b8 e8 ab f3 a7 d4 31 17 8f e5 
4b 0c 9e 0f 70 90 b1 de 28 94 5f c3 f1 ac 8b 34 
96 db 03 05 73 29 2c 2b 6e df 40 b5 52 9c 71 31 
da 59 8b 3d 46 77 c8 13 6e 7e 21 41 57 d0 92 f2 
9c 5b a5 43 a3 11 96 9c c5 1d a3 a7 ef aa af b5 
89 e1 7d 20 77 04 60 32 7e 8f 61 bc 30 9d 8b 5b 
c7 97 d6 27 25 96 d1 c7 69 67 e0 8c 77 ff d5 57 
3d 72 37 99 cd f2 48 8a f2 ec a3 e2 ea 0f 08 6d 
0f e9 9f a7 6d 63 ab 3a 35 1f c0 ea ff 0b 9d</mark>
void
<mark class='under green'>17 03 03 0a 04 3e b7 b5 ea dd e0 9e 43 45 db 32 
e4 d2 bc 62 47 f7 17 bd ce ae 1c e3 d2 03 50 ef 
72 43 07 56 93 9d e9 a2 3a 83 d9 ef d9 0f f0 04 
03 c9 95 b4 b2 4e d6 5b c7 31 d2 a3 70 97 ff d3 
96 42 9d 6a 2d be 8c 63 50 52 4c 1d 4d 2d b3 73 
b5 8e 4d f4 55 1b 6a 47 8c c5 da 19 7f 3c a4 fd 
0c b8 ab 8f 7d 63 f8 99 b7 5d 6b 46 78 c3 0b 74 
c1 e1 c5 28 c4 2e ac c3 63 d4 a4 a7 a8 b6 b0 29 
01 d8 79 9e c4 1e f6 2a 41 15 3b 78 d1 e9 17 1e 
cd 7a 33 bf db 01 88 88 39 40 50 33 0a 37 11 f6 
4c 65 a8 55 5e 93 2d 73 ce d0 d7 f7 94 23 78 8d 
3a c4 f3 07 ef 8a 91 e7 c1 4a 36 c9 bb f2 c7 d9 
1f 80 19 6f 85 ff 45 bd 95 75 78 65 c3 46 33 d8 
bc 11 24 b8 4a d6 04 6e 59 79 01 28 45 62 2c aa 
54 06 1c ac e4 75 50 c7 25 21 26 8a f1 d3 71 1c 
7a 6b b4 08 aa 55 05 d5 be 70 b5 4b 7d 31 83 40 
51 29 07 66 0e 49 88 fe 5c 06 b4 39 ed 6c 16 f3 
0f 92 27 ca 9f 75 ff 0b 51 b6 53 cd ac 2a 3e dd 
68 d1 56 78 66 ff c1 58 6a 51 68 2c 67 cf 19 31 
15 fc 2a 33 e8 bb f0 e0 98 27 32 08 b5 30 5e d0 
63 ba e3 da 01 7f da 0c 16 1e 2e 8c f1 0c 6e 6d 
cf 1d 9d 0c e2 35 3e 6b 80 8d ca 28 25 29 d5 6d 
76 ce 94 7c ac 8c 81 df 4f f1 39 8e 17 be b3 46 
ca 17 d4 57 ce 2a 7b 5e 4e 2f 36 c5 95 af 48 ba 
f6 33 8b 1a 32 4d 74 ea 45 c5 ca 44 bd d7 35 14 
f8 f5 b3 60 0b f2 c7 7b 78 a4 46 e7 5f 58 be 13 
2a 36 e2 32 ea 4e 74 9f a1 fa 38 b9 93 8b 6b 16 
ea d2 8d 2a 2b 59 86 2e 85 c4 b9 82 3c e1 1a d2 
d4 de e7 fb 26 27 b0 a9 9e 5e aa 59 6e b2 ce 61 
1b 15 c5 31 f2 ec 0e 28 2e d4 3f d8 41 d2 4e e6 
b5 78 67 a7 bf d3 25 0e b7 0a 96 d8 28 ee fd 93 
c9 21 d0 c0 71 18 92 a2 df d3 ec 4f b6 e8 2a bd 
d8 cc 9a 18 c3 f5 5d 96 a2 01 49 dd cc 1f c3 20 
14 8a eb d1 fa c0 2e 1b 99 3c e1 99 c7 e1 71 45 
81 17 5e 7a 68 90 fc 41 a2 b9 71 1d c7 98 ed 6e 
17 2d ab f4 c1 33 8e d0 72 7c 55 69 90 5a bf d5 
b2 03 75 53 5f 2d f3 b7 89 80 cc f8 33 13 cc d7 
6c 2b e4 b7 eb 7f 51 df 9a 68 35 a3 6a 5c a1 c0 
f3 55 61 8d f4 6b 98 50 5e 84 a5 58 23 fd 06 5c 
fa 6d a5 ee d1 c7 9a d0 c6 a9 40 5e 69 d3 ba a7 
ab 9b eb 5d 81 f0 e4 59 67 ff d0 87 89 7b e5 7d 
7b cf 68 84 bb 1c 90 9f ac ea c1 16 16 f2 70 14 
24 63 6f ed ba 35 85 d6 4e 2a 65 7d f0 d0 bc 99 
0a f7 eb 36 53 d6 ee 26 d8 22 76 45 5f 51 09 80 
f3 2d 9d 06 71 bd b0 3d a4 d4 75 ec ca 45 f0 af 
53 64 b3 03 42 85 88 51 14 d7 06 e3 8f 17 3e 4c 
83 0c 39 8b ae cd 2d 7d 14 97 4f 02 9f 2b 22 47 
43 d0 5e 93 2a f0 11 39 f1 28 44 a4 77 3a 1f 3e 
fa d5 54 b5 49 a8 dc f0 d8 d8 4b 47 d7 47 b8 e4 
f1 fc d5 73 b6 8d be a5 52 ef 6e 8d 38 03 90 fe 
4b 73 5d a5 10 2c 93 ce f4 ab 5b 1d 96 32 0e c1 
7b b3 a5 ad 0b aa 4f 31 74 3b f6 35 c2 59 65 37 
1b f7 db 9c bb 82 09 e7 86 66 b6 e6 8a 50 ab 1e 
60 f2 fa 51 d8 cb 81 06 73 81 e0 25 e9 66 90 48 
ef 78 9c a4 9b 21 78 e9 81 34 f0 eb 57 3d f8 75 
72 fc 9e 03 ff bc 0b 48 3a 8b 6b ca fd 79 24 cc 
c4 94 45 3b 07 f8 43 7e ae 38 32 b2 2b d2 ce f5 
ab 67 b2 21 34 ed 17 92 35 ed 0b 71 8d 88 a4 42 
bb 2a 42 a2 cb 2e 70 91 5c 28 70 e4 0c 5d 90 3b</mark>
void
<mark class='under green'>06 62 6a 0f 11 2a ce 35 41 67 cb dc 1a 35 e5 7c 
1e e0 f5 ea c8 23 f1 ab 9d 5e fa 51 53 56 c7 4e 
3a a3 32 c3 43 a9 9d 5c 3a 50 41 c9 70 16 02 69 
d2 0e 6d c6 ad fa d7 f1 0d 83 70 f4 ac 3c 19 c7 
f4 10 0d f6 af a0 98 26 02 dc bb b8 12 9e 53 91 
19 e7 be 73 fc b5 15 44 ef 68 2c fb 4e 7b b9 8c 
a0 ec 00 5c a3 68 9d bc 10 59 ab 58 20 2f 5e 14 
4b 8a 61 d3 cc 64 b2 b3 d2 b1 55 99 44 8c 1e a0 
cf b7 1c 81 07 2f ba a1 76 b1 75 39 c8 41 cf 4f 
08 ed 7e 2b b9 7b 2a d7 98 9e 7f c4 08 34 58 0a 
44 1d 8e 06 fa dd e7 58 22 2e ce 4d 78 0a 65 2d 
e5 41 5e be d1 d2 d5 34 67 86 53 e8 94 ec 8e 5e 
e5 ee de d2 88 17 8e 0a f7 2e 7c 05 fe a2 60 ae 
f4 cd 65 bf c0 de ff dc df b9 c0 33 26 ee 24 a9 
39 58 3f 9d 2e af 5e a4 e2 ba 14 6b 33 3c fa 5a 
89 ee 5e 0d 1d 87 bf 93 4b 85 0e 10 fc 31 8b 74 
8d 1f 17 51 7d d0 da 6a dc df c9 61 77 05 af 67 
b7 29 7f 4d 31 00 7a 7b 2d 7e fd 5b eb 2b 72 cc 
ba e4 fe 50 8b 90 e3 5e ed 79 9b d0 70 f1 ad 5e 
da 96 63 15 83 53 e3 7c 07 45 08 ae 61 4d 1a 51 
fe 0e 97 2c ac 75 84 5b cd d0 5c e9 34 d9 4f c1 
92 35 d0 34 a2 cc a1 4f 42 78 18 c1 88 2b 1d 98 
22 62 d9 c1 b0 ab 38 32 03 69 ea af 29 07 5b 61 
f0 89 81 95 63 4f 18 3f dc 2e 40 ae 98 a0 d7 74 
02 6a 72 fa 90 86 b9 82 15 a0 71 4e 2e fc 98 b4 
c3 0e 60 2b 03 c3 bb 97 96 71 35 2e 63 17 78 9f 
48 7d b7 35 ce e2 90 2a 20 45 10 69 57 99 f9 cc 
3c 6a 3a 05 12 af 5b 78 ba e0 1f 4f 37 6a 52 27 
45 b3 62 04 34 1d c4 54 bb af d8 4d 1e 49 2c 17 
f2 87 df 4d 8f 00 7d 73 59 83 40 4f 21 f3 30 c3 
5c a9 9c 5f 2e 9f d2 03 3d db 23 6d 7c 59 04 59 
5c b6 7e 44 e7 61 0f 3a d6 2d aa eb c4 ed 8c 35 
c2 60 97 92 e4 9e 0d 63 d3 e4 b7 3c c8 a8 44 ca 
f4 87 01 e4 37 42 ba 88 14 4c 41 12 6c 5f 85 54 
7b 42 5f 2b 10 e2 25 cc bf 23 c7 da cd 88 cb bb 
a1 f8 f7 08 ae d0 eb 72 9d 88 ef 5c 8f 40 d0 1c 
23 92 2f 7c 22 33 e1 fa 6d 68 e6 70 aa dd 8f 82 
2a 5e c4 92 f6 df 05 db 45 9e e0 29 0b c0 23 97 
14 df 7f 2d f9 54 63 09 7d 24 55 62 6a c2 95 21 
4c 22 bf 0f c1 e6 18 57 87 f5 41 ba 6b a1 31 aa 
2f 4f e8 1c 27 57 a4 ce 8a 26 d7 71 a1 68 39 cf 
18 5e e8 8f 76 dd 53 c6 46 bb ab 0d b4 4c 16 c8 
ea b2 87 d6 40 f3 1d d0 c0 08 58 35 c9 fb 7d 66 
ee 8c 2f d8 f6 89 77 68 78 1c 93 43 99 aa 6f ef 
2e e7 a6 72 5a fc 4b f4 ba b8 3b c4 07 e2 1e c1 
3f 4e 90 3c 93 c7 cb cc 1b 2c 24 f6 a3 75 e4 6b 
d4 98 64 e7 d3 56 ca 76 d8 0a 8c 92 75 68 3b 6c 
e5 80 04 1d 01 6f 67 94 7c 28 7f 88 1d 54 7c fc 
bf 14 6e 0f e1 11 a6 4f 95 f1 ed c0 39 dd 3f 1d 
29 2c 0a bf 75 65 30 1b e7 a8 7c cf 8b 21 71 64 
cf 74 30 52 1a df 56 b9 38 52 8d 0c c7 1e 64 6d 
9f bb e3 0e f3 c6 94 6b 3f 89 7a ea 3f 60 3e 4f 
97 da ef 33 1e b0 bf 24 7b c8 a0 4a 16 51 e8 40 
6f 47 4c b3 53 99 d6 d4 46 26 bf e3 cf 3a 1a e6 
2f ed 88 7b 9f 15 01 ce 2f d6 cc d2 ef bb ff a0 
22 10 94 bf 34 c6 3f a5 ef 95 72 4d b8 ce cf df 
1e f4 58 46 16 af e7 3b 7c 66 bb 01 0c af db ab 
4d 3e bf 72 9e cb 40 eb 42 19 1f 8a 59 94 f2 3b 
9b a8 95 8e 82 2f e7 7c 11 a9 21 ce a3 0e 7f cf 
23 1b fa e0 6a 9d c1 b3 c3 29 d7 32 ca 27 e6 1c 
d3 3c a5 a3 4b 9a 5c 0f fe 53 7c 1d 46 f1 0e d1 
34 50 81 23 f4 e6 74 7d ab 0e 65 37 9b f0 82 bc 
1c d0 b9 2c 31 33 d1 8d 05 76 0c 35 b6 af 96 01 
73 01 ab bf ef b4 39 16 bb 9f d1 7d 02 97 f8 63 
20 c6 46 f7 da 4e 54 d8 a8 6e 7d 82 88 d5 10 20 
28 ea 33 79 b4 e1 04 f3 f8 b4 bc 10 f6 0b 70 a8 
29 19 8e e5 49 a7 40 60 0f 13 5f 0f f8 7f 3d 10 
27 fc 60 e3 2d 27 94 ac 79 6e 59 22 b4 a0 21 55 
4b 6b ff 44 67 2f 59 45 de a9 71 7d b3 10 d9 e4 
bd 6f c7 bf 30 49 d3 9e e7 95 f5 2f 86 9b ad c0 
e2 bc 46 46 6e a4 f7 86 dc d5 cb 68 fd c8 63 11 
38 55 2f f9 58 d4 85 e8 82 dc b1 a5 a5 53 18 64 
5d 2e d8 4b 4a e2 1c ed 35 5e aa dd 09 0a b6 17 
c3 84 61 d5 b9 bc cd ba 28 a4 ca 8c 38 09 eb 61 
1d da b5 bf c6 3e d1 14 a9 54 68 d3 90 3f 08 ed 
4f 71 0a c5 c0 50 ce 53 a6 21 e3 d9 8d 91 b3 60 
0b cb 33 6c 57 4b 90 9d 06 a7 cd 6f 2a 55 29 21 
e5 10 5e ea c4 6d 6c e9 22 1b 87 1d 06 6f 73 7f 
0d 95 f0 e9 a0 61 0b cf 58 ca 28 f2 8c b2 46 21 
ee a2 5c 4a 54 da cf 72 8d 0f 6a 7f cd af 3a 96 
4f c0 bd 7a 08 6f 05 d7 80 da b8 d7 0b 72 f4 e3 
ba 8b 18 15 ef 82 1f 60 f4 29 c0 b7 31 67 1f fb 
d2 c4 2a 1c 04 69 ec ab 5e a8 47 41 14 9e ce e8 
80 f3 5a d3 cb f3 74 84 ec 17 53 78 4d e5 f7 24 
91 08 6b 53 fe 4d db 19 f4 0b a5 87 34 31 6f 1d 
0f 65 3f d8 c7 3e 91 02 77 dd 2d db d6 3a c7 81 
cd 14 33 da dd 59 1d 98 f1 ca 8d 93 4d be 55 40 
a0 3d c6 10 67 c6 37 26 44 9f d9 87 a9 8a 74 4a 
d3 2d e0 ec 29 1c 13 9e 9e ec 4a 47 39 d5 9d 46 
2c c3 2d 64 d8 4a 61 ed 89 d8 46 6c 9c 13 ce 5b 
ad 80 4d e4 ac 3f 67 87 24 ce 67 4c 5c 23 a8 10 
8e fb c6 80 5e 4b 69 27 49 6e da 27 49 61 77 31 
81 98 4e 7f 58 ef 58 11 bb f5 77 f5 44 6a 95 4d 
ca bd 99 c3 b4 5b 35 98 a8 2a 58 08 24 ea 6e a6 
b4 13 77 a1 67 e9 76 7c 8a 99 2b 0d 7d b8 f1 f9 
6c b9 ce a4 8d df 54 ab 59 ae a7 24 5e 9a f9 54 
e6 b6 03 08 3c b1 bc 00 23 71 c7 5f 27 e7 ab 52 
7f 76 68 ae 9e 93 28 67 df cf f4 42 5f 51 f4 ae 
85 3e 5b be 84 68 b1 c5 0f 07 e9 ba be e2 d3 41 
bc 78 f1 d5 c8 de 35 52 96 e5 1a e0 84 89 a6 83 
a4 6f 54 82 c4 39 f0 03 a8 6d e5 89 b8 b7 61 73 
cf 49 72 b4</mark>
HTTP/1.1 200 OK
Server: X_12302.eli
Date: Sat, 14 Dec 2024 01:39:56 GMT
Content-Type: text/html
Content-Length: 2239
Last-Modified: Tue, 08 Oct 2024 01:41:47 GMT
Connection: close
Vary: Accept-Encoding
ETag: "67048ddb-8bf"
Strict-Transport-Security: max-age=15552000
Accept-Ranges: bytes

<mark class='under green'>15 03 03 00 1a 3e b7 b5 ea dd e0 9e 44 af d9 1c 
df 98 72 4e 8e 15 5a fa 67 13</mark>
void
<mark class='under red'>15 03 03 00 1a 00 00 00 00 00 00 00 02 80 d7 0f 
d3 8c c6 0d 7e d5 44 34 5c bc f3 1f 41 b1 29</mark>
void
<!doctypehtml><html lang="en"><meta charset="UTF-8"><title>index</title><style>body{margin:0;padding:0;overflow:hidden;position:relative;display:flex;justify-content:center;align-items:center;height:150vh;background:#eee8ee;font-size:12px;font-family:Arial,sans-serif}canvas{position:absolute;bottom:0;left:0;z-index:-1}#bottom_layer{visibility:hidden;width:3000px;position:fixed;z-index:302;bottom:0;left:0;height:65px;padding-top:1px;zoom:1;margin:0;line-height:39px}#bottom_layer .s-bottom-layer-content{margin:0 17px;text-align:center}#bottom_layer .lh{display:inline-block;margin-right:14px}#bottom_layer .text-color{color:#979e87}#bottom_layer a{font-size:12px;text-decoration:none}</style><canvas id="waveCanvas"></canvas><div id="bottom_layer"style="visibility:visible;width:100%"><div class="s-bottom-layer-content"><p class="lh"><a class="text-color"href="//wtfu.site"target="_self">关于首页</a><p class="lh"><a class="text-color"href="https://wtfu.site"target="_self">About Site</a><p class="lh"><a class="text-color"href="https://beian.mps.gov.cn/#/query/webSearch?code=11011402053698"rel="noreferrer"target="_blank"><img src="https://beian.mps.gov.cn/img/logo01.dd7ff50e.png"class="w-full"style="width:12px;padding-right:3px;vertical-align:-2px"> 京公网安备11011402053698</a><p class="lh"><a class="text-color"href="https://beian.miit.gov.cn"target="_blank">陇ICP备2020004323号</a><p class="lh"><span class="text-color">© Copyright 2000-2023 12302, Inc. and Contributors</span><p class="lh"><span class="text-color">Created using OpenAI</span></div></div><script>let i=document.getElementById("waveCanvas"),n=i.getContext("2d"),h=(i.width=window.innerWidth,i.height=window.innerHeight,{yOffset:100,amplitude:50,wavelength:.03,frequency:.01,phase:0,speed:.1});window.addEventListener("resize",()=>{i.width=window.innerWidth,i.height=window.innerHeight}),function e(){n.clearRect(0,0,i.width,i.height),n.fillStyle="rgba(255, 255, 255, 0.5)",n.beginPath();for(let e=0;e<i.width;e+=10){var t=h.yOffset+Math.sin(e*h.wavelength+h.phase)*h.amplitude*Math.sin(h.frequency*e+h.phase);n.lineTo(e,t)}n.lineTo(i.width,i.height),n.lineTo(0,i.height),n.closePath(),n.fill(),h.phase+=h.speed,requestAnimationFrame(e)}()</script>
Disconnected from the target VM, address: '127.0.0.1:53227', transport: 'socket'

Process finished with exit code 0
```