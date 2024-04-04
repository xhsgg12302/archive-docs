* ## MAVEN分享

    ?> 今天这次分享的主要就是maven构建工具使用的一些经验， 和大家起分享，交流一下。也是为了日后能够更加灵活的使用。内容中也包括我原来对maven存在的误解和疑问的一些答疑。以交流学习，整理归纳为主。

    + ### Tips

        1. 运行mvn compile 会执行 clean 吗? 运行 package 会执行 compile吗?
        2. optional,provided 区别?
        3. 既然maven有自带的依赖仲裁机制，为什么还会出现依赖冲突?
        4. mvn 依赖可以定义的位置及加载顺序，远程仓库定义的位置及加载顺序。mirrorOf实际意义? 

    + ### 介绍（一笔带过）

        ```
        maven 是继 ant 之后apache开源的软件构建工具，由绑定在生命周期上的插件辅助完成构建任务，主要的
        功能包括解决依赖，集成测试，编译项目，定制化资源打包发布等。
        介绍，下载地址，使用方式。
        ```

    + ### settings.xml 介绍

        ```
        <localRepository>
        <pluginGroups>
            配置插件的groupId。为的是单独执行插件的时候，可以简单调用。
            1，全路径执行。2，如何配置省略执行，3，自带的没有配置，为什么可以省略执行 4,prefix
        <proxies>
        <servers> 将pluginGroups中默认的标签解释一下
        <mirrors>
            <mirror>
                <mirrorOf>
        <profile>  -- 属性压制
        ```
    + ### POM.xml介绍

        ```
        <relativePath/>
            查找parent路径优先级； 指定的位置 > 本地仓库 > 远程仓库 
            1,当为空标签得时候，默认从本地仓库查找没有的话再从远程，
            2,不写得时候这个值默认为 ../pom.xml，也就是他上级路径得pom.xml.
        
        <properties>
        <dependencyManagement>
        <dependencies>
            <dependency>
                <scope>
                <type>
                <classifier>
        <build>
            <filters>
                <filter>
            <resource>
                <filtering>true|false
            <plugins>
        ```

    + ### properties 引用的各种形式，${},@@
        
        ```
        https://maven.apache.org/pom.html#properties
        
        resources 过滤或加载顺序
        1,基本使用。
        2，属性优先级
        ```
    + ### maven特性

        ```
        生命周期，
        继承与聚合，，
        https://maven.apache.org/pom.html#a-final-note-on-inheritance-v-aggregation
            继承通过申明<parent>标签来定义。
        依赖机制
        
        ```
    + ### 问题答疑
        
        ```
        1,  运行mvn compile 会执行 clean吗？运行package 会执行 compile吗？
            不会，因为查看文档发现 compile 阶段 跟 clean 阶段不属于同一个声明周期【分组】。
            或者执行命令也可发现在compile命令下，不会调用clean相关插件。
        
        2,  optional ,provided 区别，
            optional 表示可选依赖传递，可以理解为默认排除
            provided 表示依赖必选，但是使用方提供，
            
        3，既然maven有自带的依赖仲裁机制，为什么还会出现依赖冲突？
            
            
        4，mvn 依赖可以在哪些地方定义？最终生效的是那部分？
            1, <dependency> , <repository> 的定义是有顺序的。
            <dependency source> like repository,mirror,<profile>
            pom.dependency  > profile.dependency > parent
            
            2,远程仓库定义
            settings > pom.profile > pom.repository
        ```
        
        ![](/.images/devops/build/maven-project-dependency.png '项目结构 :size=70%')
        ![nihao](/.images/devops/build/maven-pull-process.png 'repo :size=70%')

    + ### 常用命令

        ```
        构建命令[phase]： mvn [ clean, compile, package, install, deploy, site ] 
        查看插件帮助：mvn [plugin:help]
            mvn dependency:tree -Dverbose=true -Dincludes=commons-lang:commons-lang
        构建命令[插件：goals]：mvn [clean:clean, spring-boot:repackage ] 
        直接命令[options]: mvn [-Da=b, -emp, -ep, -X]
        ```

    + ### 插件介绍(插件，resources)
        
        ```xml
        <!-- 
            1, 打jar包-> exe文件，
                - maven-shade-plugin 配合 launch4j-maven-plugin
                - spring-boot-maven-plugin 配合 launch4j-maven-plugin 
        -->
            
        <!-- 2，远程部署tomcat插件 -->
        <plugins>
            <plugin>
                <groupId>org.apache.tomcat.maven</groupId>
                <artifactId>tomcat7-maven-plugin</artifactId>
                <version>2.2</version>
                <configuration>
                    <url>http://test.wtfu.site/manager/text</url>
                    <server>tomcat7</server>
                    <path>/test##2.1</path>
                    <charset>utf8</charset>
                    <update>true</update>
                </configuration>
            </plugin>
        </plugins>
            
        <!-- 3，代码生成插件 -->
        <plugin>
                <!-- _12302_2021/9/7_< https://gitee.com/xhsgg12302/haohuo-component.git > -->
                <groupId>com.haohuo.framework</groupId>
                <artifactId>gencode-maven-plugin</artifactId>
                <version>1.0.3-SNAPSHOT</version>
                <executions>
                    <!-- _12302_2021/9/7_< 不跟任何生命周期绑定，
                    命令行 mvn com.haohuo.framework:gencode-maven-plugin:product
                    或者maven任务栏插件，goal,执行 > -->
                </executions>
                <configuration>
                .....
                </configuration>
        </plugin>
        ```

    + ### maven私服及免费的maven仓库

        | name     | url                                                            |
        | -------- | -------------------------------------------------------------- |
        | 自己搭建 | http://mvn.wtfu.site/nexus                                     |
        | 163      | http://mirrors.163.com/.help/maven.html                        |
        | huawei   | https://mirrors.huaweicloud.com/home                           |
        | alibaba  | https://developer.aliyun.com/mvn/guide                         |
        | tencent  | https://mirrors.cloud.tencent.com/help/maven.html              |
        | github   |                                                                |
        | 云效旧版 | https://repomanage.rdc.aliyun.com/                             |
        | 云效新版 | https://packages.aliyun.com/maven （有覆盖 release版本的设置） |
    
    + ### 项目中的log4j[CVE-2021-44228] 漏洞修复思路及复现

        ```
        解决思路：
            一，访问log4j官网，查看最新响应漏洞及解决版本。替换相应的版本即可。（可能需要即时跟踪log4j动    态，最近漏洞更新挺快）
            二，以假乱真，移花接木
                1，根据全局需要的排除得jar定制高版本fake包，上传到私服，
                2，由于maven得仲裁机制压制真正得log4j包。
                3，引入三方开源得桥接包，将输出流重新路由到slf4j->logback.
        
        注意点：log4j分为log4j 1和2版本，而且它们的groupId 也不一样。 
            引入 log4j1 只需要一个 log4j:log4j:1.2.17, 爆出漏洞得不是log4j1
            而log4j2 需要引入连个：org.apache.logging.log4j:log4j-api:2.14.1 【门面接口包】
                                org.apache.logging.log4j:log4j-core:2.14.1 【实现包(出问题的包)】
        
        
            
        扩展：    
            1, logj漏洞原理的简单介绍，及两种方式复现
            2, 实际中的log4j攻击现象（nginx日志）。
        ```
        
    + ### deploy

        ```shell
        mvn clean deploy -Dmaven.test.skip=true -DaltDeploymentRepository=rdc-snapshots::default::https://packages.aliyun.com/maven/repository/2066950-snapshot-w6DSio/
        ```

* ## XML

    <!-- tabs:start -->
    #### **settings.xml**
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
    
    <localRepository>/Users/stevenobelia/Documents/maven_repository</localRepository> 
    
    <pluginGroups></pluginGroups>

    <proxies></proxies>

    <!-- servers
    | This is a list of authentication profiles, keyed by the server-id used within the system.
    | Authentication profiles can be used whenever maven must make a connection to a remote server.
    |-->
    <servers>
        <server>
            <id>releases</id>
            <username>admin</username>
            <password>{0L7qrv2rYMEHTV896SqpI58ztyMinhfN7expcu6wFes=}</password>
        </server>

        <server>
            <id>snapshots</id>
            <username>admin</username>
            <password>{0L7qrv2rYMEHTV896SqpI58ztyMinhfN7expcu6wFes=}</password>
        </server>

        <server>
            <id>tomcat7</id>
            <username>xhs</username>
            <password>{ybhHCUAegqsH4EQp2GVT6Zh4w1O3s4ZLZphTwbSe7Wo=}</password>
        </server>

        <server>
        <id>github</id>
        <username>xhs-12302</username>
        <password>{6JnHDzZJqBAHiSA42DPOtf5ZwpOCeM7vUUS+1dKKwyBvkX0Cx2m3qAuNxP05RHhYrA61UKIWvRz+BtdAQZPdig==}</password>
        </server>
        
    </servers>

    <!-- mirrors
    | This is a list of mirrors to be used in downloading artifacts from remote repositories.
    |
    | It works like this: a POM may declare a repository to use in resolving certain artifacts.
    | However, this repository may have problems with heavy traffic at times, so people have mirrored
    | it to several places.
    |
    | That repository definition will have a unique id, so we can create a mirror reference for that
    | repository, to be used as an alternate download site. The mirror site will be the preferred
    | server for that repository.
    |-->
    <mirrors></mirrors>

    <profiles>
        <profile>
            <id>father</id>
            <activation>
            <!--<activeByDefault>true</activeByDefault>-->
                <jdk>1.8</jdk>
            </activation>

            <repositories>
                <repository>
                    <id>12302</id>
                    <name>local private nexus</name>
                    <url>http://mvn.wtfu.site/nexus/content/groups/public/</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </repository>
            </repositories>
            
            <pluginRepositories>
                <pluginRepository>
                    <id>aliyun-plugin</id>
                    <name>aliyun-plugin-name</name>
                    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>false</enabled>
                    </snapshots>
                </pluginRepository>
            </pluginRepositories>	
        </profile>
            
    </profiles>
    </settings>
    ```

    #### **pom.xml**

    ?> [[version]版本规范](https://maven.apache.org/pom.html#dependency-version-requirement-specification)
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>

        <!--<parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>2.0.3.RELEASE</version>
            <relativePath/>
        </parent>-->
        
        <groupId>com.haohuo.framework</groupId>
        <artifactId>haohuo-component</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <packaging>pom</packaging>

        <name>haohuo-component</name>
        <url>https://wtfu.site/</url>

        <modules>
            <module>hh-cpt-executor</module>
            <module>hh-cpt-test</module>
            <module>hh-cpt-fake-log4j</module>
            <module>hh-cpt-fake-jcl</module>
            <module>hh-cpt-log4j-CVE</module>
            <module>hh-cpt-maven-conflict</module>
            <module>hh-cpt-maven-dependency</module>
            <module>hh-cpt-maven-plugin</module>
        </modules>

        <properties>
            <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
            <maven.compiler.source>1.8</maven.compiler.source>
            <maven.compiler.target>1.8</maven.compiler.target>
            <java.version>1.8</java.version>
            <prod.config.url>http://120.26.161.40:8888/</prod.config.url>
            <test.config.url>http://112.124.46.254:8888/</test.config.url>
            <spring.boot.version>2.0.3.RELEASE</spring.boot.version>
            <spring.cloud.version>Finchley.RELEASE</spring.cloud.version>
            <haohuo.sleuth.starter.version>0.0.1.RELEASE</haohuo.sleuth.starter.version>
            <curator.version>2.10.0</curator.version>
        </properties>

        <dependencies>
            <dependency>
                <groupId>redis.clients</groupId>
                <artifactId>jedis</artifactId>
                <version>2.9.0</version>
            </dependency> 
        </dependencies>

        <dependencyManagement>
            <dependencies>
                <dependency>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-dependencies</artifactId>
                    <version>2.0.3.RELEASE</version>
                    <type>pom</type>
                    <scope>import</scope>
                </dependency>

                <dependency>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-context</artifactId>
                    <version>5.0.7.RELEASE</version>
                </dependency>
            </dependencies>
        </dependencyManagement>
        
        <repositories></repositories>

        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-source-plugin</artifactId>
                    <version>2.1.1</version>
                    <executions>
                        <execution>
                            <id>attach-sources</id>
                            <goals>
                                <goal>jar-no-fork</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>

        <!-- 发布到私服插件<mvn deploy> 的配置 -->
        <distributionManagement>
            <!--<repository>
                <id>rdc-releases</id>
                <name>Internal releases</name>
                <url>https://packages.aliyun.com/maven/repository/2066950-release-cbvBqh/</url>
            </repository>
            <snapshotRepository>
                <id>rdc-snapshots</id>
                <name>Internal snapshots</name>
                <url>https://packages.aliyun.com/maven/repository/2066950-snapshot-w6DSio/</url>
            </snapshotRepository>-->

            <repository>
                <id>rdc-releases</id>
                <url>https://repo.rdc.aliyun.com/repository/138059-release-UHEMVy/</url>
            </repository>
            <snapshotRepository>
                <id>rdc-snapshots</id>
                <url>https://repo.rdc.aliyun.com/repository/138059-snapshot-uQQQPY/</url>
            </snapshotRepository>

            <!--<repository>
                <id>releases</id>
                <name>Internal releases</name>
                <url>http://mvn.wtfu.site/nexus/content/repositories/releases</url>
            </repository>
            <snapshotRepository>
                <id>snapshots</id>
                <name>Internal snapshots</name>
                <url>http://mvn.wtfu.site/nexus/content/repositories/snapshots</url>
            </snapshotRepository>-->

        </distributionManagement>
    </project>
    ```
    <!-- tabs:end -->


* ## 工具链

    ?> 简单理解就是，使用工具链可以配置插件调用过程中需要使用到的工具比如JDK.在maven系统中。有一些默认的工具链感知插件。比如.compile,javadoc等。这个可以[参考官方](https://maven.apache.org/guides/mini/guide-using-toolchains.html)给出的图。

    + #### 先决条件

        1. 在pom中配置`maven-toolchains-plugin`插件。
        2. 存在`toolchains.xml`配置文件用来供toolchains插件匹配。

    + #### 注意事项

        1. toolchain goal默认绑定生命周期为`validate`. default阶段为首。
        2. source和target需要JDK支持。且source小于target。可以[参考](https://maven.apache.org/plugins/maven-compiler-plugin/examples/set-compiler-source-and-target.html)

    + #### 参考配置

        ```xml
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.3.2</version>
                <configuration>
                    <source>1.8</source>
                    <target>11</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-toolchains-plugin</artifactId>
                <version>3.1.0</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>toolchain</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <toolchains>
                        <!-- 下面的配置存在于 toolchains.xml中 -->
                        <jdk>
                            <version>21</version>
                            <vendor>openjdk</vendor>
                        </jdk>
                    </toolchains>
                </configuration>
            </plugin>
        </plugins>
        ```


* ## 插件篇

    ?> 插件的[执行/调用](https://maven.apache.org/guides/plugin/guide-java-plugin-development.html#executing-your-first-mojo)方式： mvn groupId__colon__artifactId__colon__version__colon__goal
    <br> 插件配置：https://maven.apache.org/guides/mini/guide-configuring-plugins.html
    <br> 插件executions执行配置： https://maven.apache.org/guides/mini/guide-default-execution-ids.html
    <br> 插件执行[单元测试样例](https://github.com/12302-haohuo/haohuo-component/blob/master/hh-cpt-maven-plugin/gencode-maven-plugin/src/test/java/com/haohuo/framework/mojo/MojoTest.java), 也可以debug了。也可以参考[官网介绍](https://maven.apache.org/plugin-developers/plugin-testing.html).

    + ### maven-help-plugin

        ```xml
        <!-- 计算maven表达式-->
        <!-- mvn help:evaluate -Dexpression=basedir  -->

        <!-- 查看依赖项 -->
        <!-- mvn dependency:tree -Dverbose -Dincludes=commons-digester:commons-digester  -->

        <!-- _12302_2021/9/2_< filter文档 > -->
        <!-- _12302_2021/9/2_< http://maven.apache.org/guides/getting-started/index.html#how-do-i-filter-resource-files > -->
        ```

    + ### maven-assembly-plugin

        ```xml
        <!-- pom.xml -->
        <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-assembly-plugin</artifactId>
        <version>3.4.2</version>
        <executions>
            <execution>
                <id>make-assembly</id>
                <phase>package</phase>
                <goals>
                    <goal>single</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <!-- see: https://github.com/apache/maven-assembly-plugin/blob/master/src/main/resources/assemblies/jar-with-dependencies.xml -->
            <!--<descriptorRefs>
                <descriptorRef>jar-with-dependencies</descriptorRef>
            </descriptorRefs>-->
            <descriptors>
                <descriptor>src/main/assembly/assembly.xml</descriptor>
            </descriptors>

            <finalName>youzan-crawler</finalName>
            <appendAssemblyId>true</appendAssemblyId>
            <outputDirectory>${project.build.directory}/archive-demo</outputDirectory>

            <!-- see: https://stackoverflow.com/questions/15530453/how-to-stop-the-maven-assembly-plugin-from-deploying-the-artifact-it-creates -->
            <attach>false</attach>

            <!--<archiveBaseDirectory>${pom.build.}/src</archiveBaseDirectory>-->
            <archive>
                <addMavenDescriptor>true</addMavenDescriptor>
                <manifest>
                    <mainClass>com.test.youzan.Main2</mainClass>
                    <addClasspath>false</addClasspath>

                    <!--
                        see: https://docs.oracle.com/javase/tutorial/deployment/jar/downman.html
                        jar文件中的jar的加载必须由自定义的类加载器去完成,所以在fat-jar模式下 jar包内部的 lib/*.jar 不会生效。
                    -->
                    <classpathPrefix>lib/</classpathPrefix>
                </manifest>
            </archive>
        </configuration>
        </plugin>
        ```
        ```xml
        <!-- assembly/assembly.xml -->
        <assembly xmlns="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                xsi:schemaLocation="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.0 http://maven.apache.org/xsd/assembly-1.1.0.xsd">
            <id>${pom.version}</id>
            <formats>
                <!-- 指定打包格式。maven-assembly-plugin插件支持的打包格式有zip、tar、tar.gz (or tgz)、tar.bz2 (or tbz2)、jar、dir、war，可以同时指定多个打包格式 -->
                <format>jar</format>
            </formats>
            <!--指定打的包是否包含打包层目录，比如finalName是terminal-dispatch，当值为true，所有文件被放在包内的terminal-dispatch目录下，否则直接放在包的根目录下，-->
            <includeBaseDirectory>false</includeBaseDirectory>
            <fileSets>
                <fileSet>
                    <!--将target class下 的打包到conf 因为我们可能用resource占位-->
                    <directory>./target/classes</directory>
                    <outputDirectory>/</outputDirectory>
                    <includes>
                        <include>**</include>
                    </includes>
                </fileSet>
            </fileSets>
            <dependencySets>
                <dependencySet>
                    <outputDirectory>/</outputDirectory>
                    <scope>runtime</scope>
                    <useProjectArtifact>false</useProjectArtifact>
                </dependencySet>
            </dependencySets>
        </assembly>
        ```

    + ### maven-shade-plugin
    
        ```xml
        <hello></hello>
        ```

    + ### maven-dependency-plugin

        ```xml
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-dependency-plugin</artifactId>
            <version>3.1.0</version>
            <executions>
                <execution>
                    <id>copy-dependencies</id>
                    <phase>package</phase>
                    <goals>
                        <goal>copy-dependencies</goal>
                    </goals>
                    <configuration>
                        <!-- 把依赖的所有maven jar包拷贝到lib目录中 -->
                        <outputDirectory>${project.build.directory}/archive-demo/lib</outputDirectory>
                    </configuration>
                </execution>
            </executions>
        </plugin>
        ```

    + ### maven-source-plugin

        ```xml
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-source-plugin</artifactId>
            <version>3.2.1</version>
            <!-- 配置插件参数 -->
            <executions>
                <execution>
                    <id>attach-sources</id>
                    <phase>verify</phase>
                    <goals>
                        <goal>jar-no-fork</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

    + ### maven-release-plugin

        !> 需要注意release插件直接使用-D系统变量不会生效，需要通过 -Darguments="" 传递。[参见引用](https://stackoverflow.com/questions/28948048/how-to-make-maven-release-plugin-skip-tests)。
        
        ```shell
        mvn -Darguments="-Dmaven.test.skip=true -Dmaven.javadoc.skip=true -Dswagger2markup.skip=true -Dasciidoctor.skip=true -DautoVersionSubmodules=true -DscmCommentPrefix='[3.0.0-M5]' -DscmDevelopmentCommitComment='@{prefix} next development iteration @{releaseLabel}...' -DscmReleaseCommitComment='@{prefix} release @{releaseLabel}...'" org.apache.maven.plugins:maven-release-plugin:3.0.0-M5:clean
        ```

        ```xml
        <!--
        解决 java版本过高，然后Lombok兼容的问题。
        https://stackoverflow.com/questions/65380359/lomboks-access-to-jdk-compilers-internal-packages-incompatible-with-java-16 -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
                <fork>true</fork>
                <compilerArgs>
                    <arg>-J--add-opens=jdk.compiler/com.sun.tools.javac.code=ALL-UNNAMED</arg>
                    <arg>-J--add-opens=jdk.compiler/com.sun.tools.javac.comp=ALL-UNNAMED</arg>
                    <arg>-J--add-opens=jdk.compiler/com.sun.tools.javac.file=ALL-UNNAMED</arg>
                    <arg>-J--add-opens=jdk.compiler/com.sun.tools.javac.main=ALL-UNNAMED</arg>
                    <arg>-J--add-opens=jdk.compiler/com.sun.tools.javac.model=ALL-UNNAMED</arg>
                    <arg>-J--add-opens=jdk.compiler/com.sun.tools.javac.parser=ALL-UNNAMED</arg>
                    <arg>-J--add-opens=jdk.compiler/com.sun.tools.javac.processing=ALL-UNNAMED</arg>
                    <arg>-J--add-opens=jdk.compiler/com.sun.tools.javac.tree=ALL-UNNAMED</arg>
                    <arg>-J--add-opens=jdk.compiler/com.sun.tools.javac.util=ALL-UNNAMED</arg>
                    <arg>-J--add-opens=jdk.compiler/com.sun.tools.javac.jvm=ALL-UNNAMED</arg>
                </compilerArgs>
                <annotationProcessorPaths>
                    <path>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok</artifactId>
                        <version>${lombok.version}</version>
                    </path>
                </annotationProcessorPaths>
            </configuration>
        </plugin>

        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-release-plugin</artifactId>
            <!--<version>2.3.2</version>-->
            <version>3.0.0-M1</version>
            <configuration>
                <autoVersionSubmodules>true</autoVersionSubmodules>
                <scmCommentPrefix>[xhsgg12302]</scmCommentPrefix>
                <scmReleaseCommitComment>@{prefix} release @{releaseLabel}</scmReleaseCommitComment>
                <scmDevelopmentCommitComment>@{prefix} next development @{releaseLabel}</scmDevelopmentCommitComment>
            </configuration>
        </plugin>
        ```

        [执行顺序](https://maven.apache.org/maven-release/maven-release-manager/#prepare)
        <details><summary>mvn release:clean release:prepare</summary>
 
        ```shell
        ➜  haohuo-component git:(master) ✗ mvn release:clean release:prepare
        [INFO] Scanning for projects...
        [INFO] ------------------------------------------------------------------------
        [INFO] Reactor Build Order:
        [INFO] 
        [INFO] haohuo-component                                                   [pom]
        [INFO] hh-cpt-executor                                                    [jar]
        [INFO] hh-cpt-test                                                        [jar]
        [INFO] log4j                                                              [jar]
        [INFO] commons-logging                                                    [jar]
        [INFO] hh-cpt-log4j-CVE                                                   [jar]
        [INFO] hh-cpt-maven-dependency                                            [jar]
        [INFO] 
        [INFO] ---------------< com.haohuo.framework:haohuo-component >----------------
        [INFO] Building haohuo-component 1.1.1-SNAPSHOT                           [1/7]
        [INFO] --------------------------------[ pom ]---------------------------------
        [INFO] 
        [INFO] --- maven-release-plugin:3.0.0-M1:clean (default-cli) @ haohuo-component ---
        [INFO] phase cleanup
        [INFO] Cleaning up after release...
        [INFO] 
        [INFO] --- maven-release-plugin:3.0.0-M1:prepare (default-cli) @ haohuo-component ---
        [INFO] phase verify-release-configuration
        [INFO] starting prepare goal, composed of 17 phases: check-poms, scm-check-modifications, check-dependency-snapshots, create-backup-poms, map-release-versions, input-variables, map-development-versions, rewrite-poms-for-release, generate-release-poms, run-preparation-goals, scm-commit-release, scm-tag, rewrite-poms-for-development, remove-release-poms, run-completion-goals, scm-commit-development, end-release
        [INFO] [prepare] 1/17 check-poms
        [INFO] [prepare] 2/17 scm-check-modifications
        [INFO] Verifying that there are no local modifications...
        [INFO]   ignoring changes on: **/pom.xml.releaseBackup, **/pom.xml.next, **/pom.xml.tag, **/pom.xml.branch, **/release.properties, **/pom.xml.backup
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git rev-parse --show-prefix
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git status --porcelain .
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-executor/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-fake-jcl/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-fake-log4j/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-log4j-CVE/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-conflict/hh-cpt-project-common/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-conflict/hh-cpt-project-demo1/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-conflict/hh-cpt-project-demo2/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-conflict/hh-cpt-project-test/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-dependency/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-plugin/gencode-maven-plugin/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-test/target/
        [WARNING] Ignoring unrecognized line: ?? release.properties
        [INFO] [prepare] 3/17 check-dependency-snapshots
        [INFO] Checking dependencies and plugins for snapshots ...
        [INFO] [prepare] 4/17 create-backup-poms
        [INFO] [prepare] 5/17 map-release-versions
        What is the release version for "haohuo-component"? (com.haohuo.framework:haohuo-component) 1.1.1: : 
        What is the release version for "hh-cpt-executor"? (com.haohuo.framework:hh-cpt-executor) 1.1.1: : 
        What is the release version for "hh-cpt-test"? (com.haohuo.framework:hh-cpt-test) 1.1.1: : 
        What is the release version for "log4j"? (log4j:log4j) 1.1.1: : 
        What is the release version for "commons-logging"? (commons-logging:commons-logging) 1.1.1: : 
        What is the release version for "hh-cpt-log4j-CVE"? (com.haohuo.framework:hh-cpt-log4j-CVE) 1.1.1: : 
        What is the release version for "hh-cpt-maven-dependency"? (com.haohuo.framework:hh-cpt-maven-dependency) 1.1.1: : 
        [INFO] [prepare] 6/17 input-variables
        What is the SCM release tag or label for "haohuo-component"? (com.haohuo.framework:haohuo-component) haohuo-component-1.1.1: : 1.1.1.release
        [INFO] [prepare] 7/17 map-development-versions
        What is the new development version for "haohuo-component"? (com.haohuo.framework:haohuo-component) 1.1.2-SNAPSHOT: : 
        What is the new development version for "hh-cpt-executor"? (com.haohuo.framework:hh-cpt-executor) 1.1.2-SNAPSHOT: : 
        What is the new development version for "hh-cpt-test"? (com.haohuo.framework:hh-cpt-test) 1.1.2-SNAPSHOT: : 
        What is the new development version for "log4j"? (log4j:log4j) 1.1.2-SNAPSHOT: : 
        What is the new development version for "commons-logging"? (commons-logging:commons-logging) 1.1.2-SNAPSHOT: : 
        What is the new development version for "hh-cpt-log4j-CVE"? (com.haohuo.framework:hh-cpt-log4j-CVE) 1.1.2-SNAPSHOT: : 
        What is the new development version for "hh-cpt-maven-dependency"? (com.haohuo.framework:hh-cpt-maven-dependency) 1.1.2-SNAPSHOT: : 
        [INFO] [prepare] 8/17 rewrite-poms-for-release
        [INFO] Transforming 'haohuo-component'...
        [INFO] Transforming 'hh-cpt-executor'...
        [INFO] Transforming 'hh-cpt-test'...
        [INFO] Transforming 'log4j'...
        [INFO] Transforming 'commons-logging'...
        [INFO] Transforming 'hh-cpt-log4j-CVE'...
        [INFO] Transforming 'hh-cpt-maven-dependency'...
        [INFO] [prepare] 9/17 generate-release-poms
        [INFO] Not generating release POMs
        [INFO] [prepare] 10/17 run-preparation-goals
        [INFO] Executing goals 'clean verify'...
        [WARNING] Maven will be executed in interactive mode, but no input stream has been configured for this MavenInvoker instance.
        [INFO] [INFO] Scanning for projects...
        [INFO] [INFO] ------------------------------------------------------------------------
        [INFO] [INFO] Reactor Build Order:
        [INFO] [INFO] 
        [INFO] [INFO] haohuo-component                                                   [pom]
        [INFO] [INFO] hh-cpt-executor                                                    [jar]
        [INFO] [INFO] hh-cpt-test                                                        [jar]
        [INFO] [INFO] log4j                                                              [jar]
        [INFO] [INFO] commons-logging                                                    [jar]
        [INFO] [INFO] hh-cpt-log4j-CVE                                                   [jar]
        [INFO] [INFO] hh-cpt-maven-dependency                                            [jar]
        [INFO] [INFO] 
        [INFO] [INFO] ---------------< com.haohuo.framework:haohuo-component >----------------
        [INFO] [INFO] Building haohuo-component 1.1.1                                    [1/7]
        [INFO] [INFO] --------------------------------[ pom ]---------------------------------
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ haohuo-component ---
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-source-plugin:2.1.1:jar-no-fork (attach-sources) @ haohuo-component ---
        [INFO] [INFO] 
        [INFO] [INFO] ----------------< com.haohuo.framework:hh-cpt-executor >----------------
        [INFO] [INFO] Building hh-cpt-executor 1.1.1                                     [2/7]
        [INFO] [INFO] --------------------------------[ jar ]---------------------------------
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ hh-cpt-executor ---
        [INFO] [INFO] Deleting /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-executor/target
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ hh-cpt-executor ---
        [INFO] [INFO] Using 'UTF-8' encoding to copy filtered resources.
        [INFO] [INFO] skip non existing resourceDirectory /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-executor/src/main/resources
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ hh-cpt-executor ---
        [INFO] [INFO] Changes detected - recompiling the module!
        [INFO] [INFO] Compiling 17 source files to /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-executor/target/classes
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ hh-cpt-executor ---
        [INFO] [INFO] Using 'UTF-8' encoding to copy filtered resources.
        [INFO] [INFO] skip non existing resourceDirectory /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-executor/src/test/resources
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ hh-cpt-executor ---
        [INFO] [INFO] No sources to compile
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ hh-cpt-executor ---
        [INFO] [INFO] No tests to run.
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ hh-cpt-executor ---
        [INFO] [INFO] Building jar: /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-executor/target/hh-cpt-executor-1.1.1.jar
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-source-plugin:2.1.1:jar-no-fork (attach-sources) @ hh-cpt-executor ---
        [INFO] [INFO] Building jar: /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-executor/target/hh-cpt-executor-1.1.1-sources.jar
        [INFO] [INFO] 
        [INFO] [INFO] ------------------< com.haohuo.framework:hh-cpt-test >------------------
        [INFO] [INFO] Building hh-cpt-test 1.1.1                                         [3/7]
        [INFO] [INFO] --------------------------------[ jar ]---------------------------------
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ hh-cpt-test ---
        [INFO] [INFO] Deleting /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-test/target
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ hh-cpt-test ---
        [INFO] [INFO] Using 'UTF-8' encoding to copy filtered resources.
        [INFO] [INFO] Copying 3 resources
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ hh-cpt-test ---
        [INFO] [INFO] Changes detected - recompiling the module!
        [INFO] [INFO] Compiling 3 source files to /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-test/target/classes
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ hh-cpt-test ---
        [INFO] [INFO] Using 'UTF-8' encoding to copy filtered resources.
        [INFO] [INFO] skip non existing resourceDirectory /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-test/src/test/resources
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ hh-cpt-test ---
        [INFO] [INFO] No sources to compile
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ hh-cpt-test ---
        [INFO] [INFO] No tests to run.
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ hh-cpt-test ---
        [INFO] [INFO] Building jar: /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-test/target/hh-cpt-test-1.1.1.jar
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-source-plugin:2.1.1:jar-no-fork (attach-sources) @ hh-cpt-test ---
        [INFO] [INFO] Building jar: /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-test/target/hh-cpt-test-1.1.1-sources.jar
        [INFO] [INFO] 
        [INFO] [INFO] ----------------------------< log4j:log4j >-----------------------------
        [INFO] [INFO] Building log4j 1.1.1                                               [4/7]
        [INFO] [INFO] --------------------------------[ jar ]---------------------------------
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ log4j ---
        [INFO] [INFO] Deleting /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-fake-log4j/target
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ log4j ---
        [INFO] [INFO] Using 'UTF-8' encoding to copy filtered resources.
        [INFO] [INFO] skip non existing resourceDirectory /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-fake-log4j/src/main/resources
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ log4j ---
        [INFO] [INFO] Nothing to compile - all classes are up to date
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ log4j ---
        [INFO] [INFO] Using 'UTF-8' encoding to copy filtered resources.
        [INFO] [INFO] skip non existing resourceDirectory /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-fake-log4j/src/test/resources
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ log4j ---
        [INFO] [INFO] No sources to compile
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ log4j ---
        [INFO] [INFO] No tests to run.
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ log4j ---
        [INFO] [WARNING] JAR will be empty - no content was marked for inclusion!
        [INFO] [INFO] Building jar: /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-fake-log4j/target/log4j-1.1.1.jar
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-source-plugin:2.1.1:jar-no-fork (attach-sources) @ log4j ---
        [INFO] [INFO] Building jar: /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-fake-log4j/target/log4j-1.1.1-sources.jar
        [INFO] [INFO] 
        [INFO] [INFO] ------------------< commons-logging:commons-logging >-------------------
        [INFO] [INFO] Building commons-logging 1.1.1                                     [5/7]
        [INFO] [INFO] --------------------------------[ jar ]---------------------------------
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ commons-logging ---
        [INFO] [INFO] Deleting /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-fake-jcl/target
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ commons-logging ---
        [INFO] [INFO] Using 'UTF-8' encoding to copy filtered resources.
        [INFO] [INFO] skip non existing resourceDirectory /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-fake-jcl/src/main/resources
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ commons-logging ---
        [INFO] [INFO] Nothing to compile - all classes are up to date
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ commons-logging ---
        [INFO] [INFO] Using 'UTF-8' encoding to copy filtered resources.
        [INFO] [INFO] skip non existing resourceDirectory /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-fake-jcl/src/test/resources
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ commons-logging ---
        [INFO] [INFO] No sources to compile
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ commons-logging ---
        [INFO] [INFO] No tests to run.
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ commons-logging ---
        [INFO] [WARNING] JAR will be empty - no content was marked for inclusion!
        [INFO] [INFO] Building jar: /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-fake-jcl/target/commons-logging-1.1.1.jar
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-source-plugin:2.1.1:jar-no-fork (attach-sources) @ commons-logging ---
        [INFO] [INFO] Building jar: /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-fake-jcl/target/commons-logging-1.1.1-sources.jar
        [INFO] [INFO] 
        [INFO] [INFO] ---------------< com.haohuo.framework:hh-cpt-log4j-CVE >----------------
        [INFO] [INFO] Building hh-cpt-log4j-CVE 1.1.1                                    [6/7]
        [INFO] [INFO] --------------------------------[ jar ]---------------------------------
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ hh-cpt-log4j-CVE ---
        [INFO] [INFO] Deleting /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-log4j-CVE/target
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ hh-cpt-log4j-CVE ---
        [INFO] [INFO] Using 'UTF-8' encoding to copy filtered resources.
        [INFO] [INFO] skip non existing resourceDirectory /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-log4j-CVE/src/main/resources
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ hh-cpt-log4j-CVE ---
        [INFO] [INFO] Changes detected - recompiling the module!
        [INFO] [INFO] Compiling 14 source files to /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-log4j-CVE/target/classes
        [INFO] [WARNING] /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-log4j-CVE/src/main/java/com/haohuo/framework/jndi/RMIRefServer.java:[3,32] ReferenceWrapper是内部专用 API, 可能会在未来发行版中删除
        [INFO] [WARNING] /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-log4j-CVE/src/main/java/com/haohuo/framework/jndi/RMIRefServer.java:[3,32] ReferenceWrapper是内部专用 API, 可能会在未来发行版中删除
        [INFO] [WARNING] /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-log4j-CVE/src/main/java/com/haohuo/framework/jndi/RMIRefServer.java:[3,32] ReferenceWrapper是内部专用 API, 可能会在未来发行版中删除
        [INFO] [WARNING] /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-log4j-CVE/src/main/java/com/haohuo/framework/jndi/RMIRefServer.java:[3,32] ReferenceWrapper是内部专用 API, 可能会在未来发行版中删除
        [INFO] [WARNING] /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-log4j-CVE/src/main/java/com/haohuo/framework/jndi/RMIRefServer.java:[3,32] ReferenceWrapper是内部专用 API, 可能会在未来发行版中删除
        [INFO] [WARNING] /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-log4j-CVE/src/main/java/com/haohuo/framework/jndi/RMIRefServer.java:[28,8] ReferenceWrapper是内部专用 API, 可能会在未来发行版中删除
        [INFO] [WARNING] /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-log4j-CVE/src/main/java/com/haohuo/framework/jndi/RMIRefServer.java:[28,48] ReferenceWrapper是内部专用 API, 可能会在未来发行版中删除
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ hh-cpt-log4j-CVE ---
        [INFO] [INFO] Using 'UTF-8' encoding to copy filtered resources.
        [INFO] [INFO] skip non existing resourceDirectory /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-log4j-CVE/src/test/resources
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ hh-cpt-log4j-CVE ---
        [INFO] [INFO] No sources to compile
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ hh-cpt-log4j-CVE ---
        [INFO] [INFO] No tests to run.
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ hh-cpt-log4j-CVE ---
        [INFO] [INFO] Building jar: /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-log4j-CVE/target/hh-cpt-log4j-CVE-1.1.1.jar
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-source-plugin:2.1.1:jar-no-fork (attach-sources) @ hh-cpt-log4j-CVE ---
        [INFO] [INFO] Building jar: /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-log4j-CVE/target/hh-cpt-log4j-CVE-1.1.1-sources.jar
        [INFO] [INFO] 
        [INFO] [INFO] ------------< com.haohuo.framework:hh-cpt-maven-dependency >------------
        [INFO] [INFO] Building hh-cpt-maven-dependency 1.1.1                             [7/7]
        [INFO] [INFO] --------------------------------[ jar ]---------------------------------
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ hh-cpt-maven-dependency ---
        [INFO] [INFO] Deleting /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-maven-dependency/target
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ hh-cpt-maven-dependency ---
        [INFO] [INFO] Using 'UTF-8' encoding to copy filtered resources.
        [INFO] [INFO] Copying 2 resources
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ hh-cpt-maven-dependency ---
        [INFO] [INFO] No sources to compile
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ hh-cpt-maven-dependency ---
        [INFO] [INFO] Using 'UTF-8' encoding to copy filtered resources.
        [INFO] [INFO] skip non existing resourceDirectory /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-maven-dependency/src/test/resources
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ hh-cpt-maven-dependency ---
        [INFO] [INFO] No sources to compile
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ hh-cpt-maven-dependency ---
        [INFO] [INFO] No tests to run.
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ hh-cpt-maven-dependency ---
        [INFO] [INFO] Building jar: /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-maven-dependency/target/hh-cpt-maven-dependency-1.1.1.jar
        [INFO] [INFO] 
        [INFO] [INFO] --- maven-source-plugin:2.1.1:jar-no-fork (attach-sources) @ hh-cpt-maven-dependency ---
        [INFO] [INFO] Building jar: /Users/mac/Documents/idea-projects/haohuo-component/hh-cpt-maven-dependency/target/hh-cpt-maven-dependency-1.1.1-sources.jar
        [INFO] [INFO] ------------------------------------------------------------------------
        [INFO] [INFO] Reactor Summary for haohuo-component 1.1.1:
        [INFO] [INFO] 
        [INFO] [INFO] haohuo-component ................................... SUCCESS [  0.396 s]
        [INFO] [INFO] hh-cpt-executor .................................... SUCCESS [  3.177 s]
        [INFO] [INFO] hh-cpt-test ........................................ SUCCESS [  1.619 s]
        [INFO] [INFO] log4j .............................................. SUCCESS [  0.042 s]
        [INFO] [INFO] commons-logging .................................... SUCCESS [  0.042 s]
        [INFO] [INFO] hh-cpt-log4j-CVE ................................... SUCCESS [  1.938 s]
        [INFO] [INFO] hh-cpt-maven-dependency ............................ SUCCESS [  0.572 s]
        [INFO] [INFO] ------------------------------------------------------------------------
        [INFO] [INFO] BUILD SUCCESS
        [INFO] [INFO] ------------------------------------------------------------------------
        [INFO] [INFO] Total time:  8.157 s
        [INFO] [INFO] Finished at: 2023-04-07T11:56:32+08:00
        [INFO] [INFO] ------------------------------------------------------------------------
        [INFO] [prepare] 11/17 scm-commit-release
        [INFO] Checking in modified POMs...
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git add -- pom.xml hh-cpt-executor/pom.xml hh-cpt-test/pom.xml hh-cpt-fake-log4j/pom.xml hh-cpt-fake-jcl/pom.xml hh-cpt-log4j-CVE/pom.xml hh-cpt-maven-dependency/pom.xml
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git rev-parse --show-prefix
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git status --porcelain .
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-executor/pom.xml.releaseBackup
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-executor/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-fake-jcl/pom.xml.releaseBackup
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-fake-jcl/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-fake-log4j/pom.xml.releaseBackup
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-fake-log4j/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-log4j-CVE/pom.xml.releaseBackup
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-log4j-CVE/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-conflict/hh-cpt-project-common/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-conflict/hh-cpt-project-demo1/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-conflict/hh-cpt-project-demo2/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-conflict/hh-cpt-project-test/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-dependency/pom.xml.releaseBackup
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-dependency/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-plugin/gencode-maven-plugin/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-test/pom.xml.releaseBackup
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-test/target/
        [WARNING] Ignoring unrecognized line: ?? pom.xml.releaseBackup
        [WARNING] Ignoring unrecognized line: ?? release.properties
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git commit --verbose -F /var/folders/g8/1p6zv3xs00x2b9h8fd6h8nr80000gn/T/maven-scm-2145511670.commit
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git symbolic-ref HEAD
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git push https://gitee.com/xhsgg12302/haohuo-component refs/heads/master:refs/heads/master
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [INFO] [prepare] 12/17 scm-tag
        [INFO] Tagging release with the label 1.1.1.release...
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git tag -F /var/folders/g8/1p6zv3xs00x2b9h8fd6h8nr80000gn/T/maven-scm-1550821822.commit 1.1.1.release
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git push https://gitee.com/xhsgg12302/haohuo-component refs/tags/1.1.1.release
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git ls-files
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [INFO] [prepare] 13/17 rewrite-poms-for-development
        [INFO] Transforming 'haohuo-component'...
        [INFO] Transforming 'hh-cpt-executor'...
        [INFO] Transforming 'hh-cpt-test'...
        [INFO] Transforming 'log4j'...
        [INFO] Transforming 'commons-logging'...
        [INFO] Transforming 'hh-cpt-log4j-CVE'...
        [INFO] Transforming 'hh-cpt-maven-dependency'...
        [INFO] [prepare] 14/17 remove-release-poms
        [INFO] Not removing release POMs
        [INFO] [prepare] 15/17 run-completion-goals
        [INFO] [prepare] 16/17 scm-commit-development
        [INFO] Checking in modified POMs...
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git add -- pom.xml hh-cpt-executor/pom.xml hh-cpt-test/pom.xml hh-cpt-fake-log4j/pom.xml hh-cpt-fake-jcl/pom.xml hh-cpt-log4j-CVE/pom.xml hh-cpt-maven-dependency/pom.xml
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git rev-parse --show-prefix
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git status --porcelain .
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-executor/pom.xml.releaseBackup
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-executor/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-fake-jcl/pom.xml.releaseBackup
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-fake-jcl/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-fake-log4j/pom.xml.releaseBackup
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-fake-log4j/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-log4j-CVE/pom.xml.releaseBackup
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-log4j-CVE/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-conflict/hh-cpt-project-common/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-conflict/hh-cpt-project-demo1/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-conflict/hh-cpt-project-demo2/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-conflict/hh-cpt-project-test/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-dependency/pom.xml.releaseBackup
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-dependency/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-maven-plugin/gencode-maven-plugin/target/
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-test/pom.xml.releaseBackup
        [WARNING] Ignoring unrecognized line: ?? hh-cpt-test/target/
        [WARNING] Ignoring unrecognized line: ?? pom.xml.releaseBackup
        [WARNING] Ignoring unrecognized line: ?? release.properties
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git commit --verbose -F /var/folders/g8/1p6zv3xs00x2b9h8fd6h8nr80000gn/T/maven-scm-1390995692.commit
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git symbolic-ref HEAD
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [INFO] Executing: /bin/sh -c cd /Users/mac/Documents/idea-projects/haohuo-component && git push https://gitee.com/xhsgg12302/haohuo-component refs/heads/master:refs/heads/master
        [INFO] Working directory: /Users/mac/Documents/idea-projects/haohuo-component
        [INFO] [prepare] 17/17 end-release
        [INFO] Release preparation complete.
        [INFO] ------------------------------------------------------------------------
        [INFO] Reactor Summary for haohuo-component 1.1.1-SNAPSHOT:
        [INFO] 
        [INFO] haohuo-component ................................... SUCCESS [01:39 min]
        [INFO] hh-cpt-executor .................................... SKIPPED
        [INFO] hh-cpt-test ........................................ SKIPPED
        [INFO] log4j .............................................. SKIPPED
        [INFO] commons-logging .................................... SKIPPED
        [INFO] hh-cpt-log4j-CVE ................................... SKIPPED
        [INFO] hh-cpt-maven-dependency ............................ SKIPPED
        [INFO] ------------------------------------------------------------------------
        [INFO] BUILD SUCCESS
        [INFO] ------------------------------------------------------------------------
        [INFO] Total time:  01:39 min
        [INFO] Finished at: 2023-04-07T11:56:37+08:00
        [INFO] ------------------------------------------------------------------------
        ➜  haohuo-component git:(master) ✗ 
        ```
        </details>

    + ### maven-gpg-plugin

        ?> 可以参考[官方](https://maven.apache.org/plugins/maven-gpg-plugin/usage.html)指定配置，建议使用env配置方式。

        > [!ATTENTION]
        以下配置方式由于放置敏感（即使加密过）数据在settings.xml中，被官方认为是`pseudo security`伪安全的。所以在版本`3.1.0`之后，这个插件对settings.xml中配置的`passphrase`支持出现bug了。大概原因是因为`3.1.0`之后的版本移除了`'/META-INF/plexus/components.xml'`，导致查找主密码的文件查找使用了**DefaultSecDispatcher**中默认的`~/.settings-security.xml`,而不是`~/.m2/settings-security.xml`,导致解密失败，近而影响到签名。
        <br><br>根据提交到apache的issues,这个问题会在`3.2.3`中进行修复，目前已经合并PR，但未发版本，据说很快会有 :smirk: 。具体详情可以参考 [MGPG-121](https://issues.apache.org/jira/browse/MGPG-121)。

        <!-- tabs:start -->
        ##### **settings.xml**
        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
            <!--
                https://maven.apache.org/settings.html#quick-overview
                文档中介绍说可以使用所有的system-properties(since Maven 3.0)或者 环境变量（${env.HOME}）
                特别说明： 在此文件通过<properties></properties>定义的变量不能用于插值[interpolation]。

                可以使用help插件来验证 mvn help:evaluate  -> ${settings.localRepository}
            -->
            <localRepository>${user.home}/Documents/maven_repo</localRepository>

            <pluginGroups>
                <pluginGroup>com.haohuo.framework</pluginGroup>
            </pluginGroups>

            <proxies />
            <mirrors />

            <!-- servers
            | This is a list of authentication profiles, keyed by the server-id used within the system.
            | Authentication profiles can be used whenever maven must make a connection to a remote server.
            |-->
            <!--
                password encryption: https://maven.apache.org/guides/mini/guide-encryption.html
            -->
            <servers>
                <server>
                    <id>gpg.passphrase</id>
                    <!--<keyID>48E1F1185160B400</keyID>-->
                    <passphrase>{AcPACqkxKm8H9FSK3OnqmM9+a1VQ7po8KQuQ+38pzjY=}</passphrase>
                </server>
            </servers>

            <profiles>
                <profile>
                    <id>father</id>
                    <activation><activeByDefault>true</activeByDefault></activation>
                    <properties>
                        <gpg.keyname>48E1F1185160B400</gpg.keyname>
                    </properties>
                </profile>
            </profiles>
        </settings>
        ```
        ##### **pom.xml**
        ```xml
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-gpg-plugin</artifactId>
            <version>3.2.3-SNAPSHOT</version>
            <executions>
                <execution>
                    <id>sign-artifacts</id>
                    <phase>verify</phase>
                    <goals>
                        <goal>sign</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```
        <!-- tabs:end -->

    + ### -----------------

    + ### spring-boot-maven-plugin

        ```xml
        <plugin>
            <!--
            package org.springframework.boot.maven;
            @Mojo(
                name = "repackage",
                defaultPhase = LifecyclePhase.PACKAGE,
                requiresProject = true,
                threadSafe = true,
                requiresDependencyResolution = ResolutionScope.COMPILE_PLUS_RUNTIME,
                requiresDependencyCollection = ResolutionScope.COMPILE_PLUS_RUNTIME
            )
            public class RepackageMojo extends AbstractDependencyFilterMojo {
            -->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>2.0.3.RELEASE</version>
            <configuration>
                <mainClass>${Main.class}</mainClass>
            </configuration>
            <executions>
                <execution>
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

    + ### gencode-maven-plugin

        !> [源代码](https://github.com/12302-haohuo/haohuo-component/tree/master/hh-cpt-maven-plugin/gencode-maven-plugin)

        ?> **version:1.4.3**
        <br> `<pluginGroups><pluginGroup>com.haohuo.framework</pluginGroup></pluginGroups>`
        <br><br> `mvn gencode:decode  -DaccessKey=*** -Dsettings="$HOME/software/apache-maven-3.3.9/conf/settings.xml"`
        <br> `mvn gencode:decode-single  -DaccessKey=*** -DencryptString='******'`
        <br> `mvn gencode:decode-single  -DaccessKey=*** -Dmaster='***' -DencryptString='******'`

        ```xml
        <profile>
            <id>code-generator</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>

            <pluginRepositories>
                <pluginRepository>
                    <id>gen-repo_id</id>
                    <name>gen-repo-name</name>
                    <url>https://mvn.wtfu.site/nexus/content/groups/public/</url>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                    <releases><enabled>false</enabled></releases>
                </pluginRepository>
            </pluginRepositories>

            <build>
                <plugins>
                    <plugin>
                        <groupId>com.haohuo.framework</groupId>
                        <artifactId>gencode-maven-plugin</artifactId>
                        <version>1.4.3</version>
                        <!-- 
                            # 查看支持的goal
                            mvn gencode:help [-Ddetail -Dgoal=xxx]
                        -->

                        <configuration>
                            <author>gen-code</author>
                            <info>false</info>
                            <swagger2>true</swagger2>
                            <output>${basedir}/src/main/java</output>
                            <username>username</username>
                            <password>{jXe28vG2lYEHY+7IlnlBVT0Mb3WSP+XUcKT1mlJrDHY=}</password>
                            <url><![CDATA[
                            jdbc:mysql://aliyuncs.com:3306/frame-dev?useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&useSSL=false&serverTimezone=GMT%2B8&allowPublicKeyRetrieval=true
                                    ]]></url>
                            <companySuffix>com.msb</companySuffix>
                            <moduleName>frame.admin</moduleName>
                            <contains>entity, mapper, service, impl, xml</contains>
                            <entityPkgName>domain</entityPkgName>

                            <tables>youzan_trade_record,</tables>

                            <settings>/Users/mac/software/apache-maven-3.3.9/conf/settings.xml</settings>
                            <accessKey>***</accessKey>

                            <mailParamsVo>
                                <from>12302@126.com</from>
                                <to>12302@tom.com</to>
                                <smtp>smtp.126.com</smtp>
                            </mailParamsVo>
                        
                        </configuration>
                    </plugin>
                </plugins>
            </build>
        </profile>
        ```

    + ### github-release-maven-plugin

        ```xml
        <!--
            https://github.com/RagedUnicorn/github-release-maven-plugin 
            https://github.com/xhsgg12302/aria2/releases
        -->
        <plugin>
            <groupId>com.haohuo.framework</groupId>
            <artifactId>github-release-maven-plugin</artifactId>
            <version>1.0.8-SNAPSHOT</version>
            <executions>
                <execution>
                    <id>default-cli</id>
                    <configuration>
                        <owner>xhsgg12302</owner>
                        <repository>aria2</repository>
                        <server>github-release-oauth</server>
                        <tagName>${project.version}</tagName>
                        <name>example-release</name>
                        <targetCommitish>master</targetCommitish>
                        <!--<body>release description overwritten by release notes</body>-->
                        <releaseNotes>README</releaseNotes>
                        <assets>
                            <asset>configure</asset>
                            <asset>aria2.tar.gz</asset>
                        </assets>
                    </configuration>
                </execution>
            </executions>
        </plugin>
        ```


## Reference
* https://xxgblog.com/2015/10/23/wagon-maven-plugin/
* [中文镜像](https://maven.org.cn/)
