## Gradle入门到实战

* ### Groovy语言的学习

    ?> 学习Geadle我们需要先来学习一下Groovy，很多同学估计听说过Groovy语言，有的也许没听说过，其实我们每天在配置Gradle的时候就是在跟Groovy接触了。Groovy是一门jvm语言，最终编译成class文件然后在jvm上执行，Java语言的特性Groovy都支持，我们可以混写Java和Groovy，就是在同一文件中可以混写， 注意，这里与Java和Kotlin可不一样，Java和Kotlin是可以互相调用，而不能在同一文件混写 。
    <br><br>Groovy的优势是什么呢？Groovy增强了Java的很多功能。比如解析xml文件，文件读写等，Groovy只需要几行代码就能搞定，而如果用Java则需要几十行代码，简单说就是使用Groovy实现某些功能比Java要少些很多代码。
    <br><br>好了，说了那么多"废话"，下面就具体来了解一下Groovy语言，我们只需要了解其中核心的一些使用就可以了，没必要完全了解，作为一门语言Groovy也算比较复杂的，但是我们学习Groovy是为了编写Gradle插件那就只需要了解其核心部分就可以了，其实从我的经验来说学习Groovy更多是为了写更少的代码来实现一些功能，否则我直接用Java写不就完了。

    1. #### Groovy的变量和方法声明

        + Groovy使用def来声明变量，声明变量可以不加类型，运行的时候可以自动推断出变量类型，并且声明语句可以不加分号结尾，如下：

            ```groovy
            def  i = 10
            def  str = "hello groovy"
            def  d = 1.25
            ```

        + def同样用来定义函数，Groovy中函数返回值可以是无类型的：

            ```groovy
            def showName(){
                "wang lei" //最后一行执行结果默认作为函数返回值，可以不加return
            }
            ```

        + 如果我们指定了函数的返回值类型，那么可以不加def关键字：

            ```groovy
            //定义函数
            String showName(){
                "wang lei" //最后一行执行结果默认作为函数返回值
            }
            ```

    2. #### Groovy的字符串

        !> Groovy中字符串分为三种，我们一个个来看。

        + 单引号字符串，单引号字符串不会对$号进行转义，原样输出，如下：

            ```groovy
            def i = 10
            def str1 = 'i am $i yuan'//单引号不会转义
            println str1 //输出  i am $i yuan
            ```

        + 双引号字符串，双引号字符串会对$号进行转义，如下：

            ```groovy
            def i = 10
            def str2 = "i am $i yuan"//双引号会转义
            println str2 //输出  i am 10 yuan
            ```
      
        + 三引号字符串，三引号字符串支持换行，原样输出，如下：

            ```groovy
            //三个引号原样输出
            def str3 = '''
                public static void main(){
                    println "miss you"
                }
            '''
            println str3
            ```

    3. #### Groovy的数据类型

        _Groovy数据类型主要包括以下三种：_

        + **Java中基本数据类型**

            ?> Groovy中的所有事物都是对象。int，double等Java中的基本数据类型，在Groovy中对应的是它们的包装数据类型。比如int对应为Integer。比如：

            ```groovy
            def x = 23
            println x.getClass().getCanonicalName()//java.lang.Integer

            def f = true
            println f.getClass().getCanonicalName()//java.lang.Boolean
            ```

        + **增强的List,Map,Range容器类**

            - list: 链表，其底层对应Java中的List接口，一般用ArrayList作为真正的实现 类。

                ```groovy
                def myList = [5,'werw',true]//看到了吧，可以放任意数据，就是那么任性
                myList.add(34)
                //myList.add(12,55)//这样会报角标越界异常
                myList[6] = true
                println myList.size()
                //遍历
                myList.each{
                    println "item: $it"
                }
                myList.eachWithIndex {
                    it, i -> // `it` is the current element,`i` is the index
                    println "$i: $it"
                }
                println myList.getClass().getCanonicalName()

                // 单独说明一下，List还有一种方式可以加入元素：<<操作。如下：
                // 文档地址： https://groovy-lang.org/syntax.html#_lists
                1myList << 34//这样也是加入元素
                ```

            - Map: 

                ```groovy
                //Map类
                def map = ['key1':true,name:"str1",3:"str3",4:"str4"]
                println map.name // 相当于 map.get('name')
                println map.get(4)
                map.age = 14 //加入 age:14 数据项
                println map.age //输出14
                println map.getClass().getCanonicalName()
                //遍历
                map.each{
                    key,value -> 
                    println key +"--"+ value
                    }
                    
                map.each{
                    it -> 
                    println "$it.key ::: $it.value"
                    }
                // `entry` is a map entry, `i` the index in the map
                map.eachWithIndex { entry, i ->
                    println "$i - Name: $entry.key Age: $entry.value"
                }

                // Key, value and i as the index in the map
                map.eachWithIndex { key, value, i ->
                    println "$i - Name: $key Age: $value"
                }
                    
                println map.containsKey('key1')  //判断map中是否包含给定的key
                println map.containsValue(1)  //判断map中是否包含给定的value
                //返回所有
                println map.findAll{
                    entry ->
                        entry.value instanceof String//value是String类型的
                }
                //返回第一个符合要求的
                println map.find{
                    entry ->
                        entry.key instanceof Integer && entry.key > 1
                } //返回符合条件一个entry

                //清空
                map.clear()

                println map.size()
                ```

            - Ranges: 简单来说Ranges就是对List的扩展，用..或者..<来定义，具体使用如下：

                ```groovy
                //Range
                def Range1 = 1..10//包括10
                println Range1.contains(10)
                println Range1.from +"__"+Range1.to
                def Range2 = 1..<10//不包括10
                println Range2.contains(10)
                println Range2.from +"__"+Range2.to
                //遍历
                for (i in 1..10) {
                    println "for ${i}"
                }

                (1..10).each { i ->
                    println "each ${i}"
                }
                def years = 23
                def interestRate
                switch (years) {
                    case 1..10: interestRate = 0.076; break;
                    case 11..25: interestRate = 0.052; break;
                    default: interestRate = 0.037;
                }
                println interestRate//0.052
                ```

        + **闭包**

            ?> 闭包Closure一开始接触的时候真是有点蒙圈的感觉，这是什么玩意，Kotlin中也有这玩意，接触多了也就慢慢习惯了，其实就是一段可执行的代码，我们可以像定义变量一样定义可执行的代码。关于闭包，刚接触肯定不习惯，自己一定要花时间慢慢体会，闭包在Gradle中大量使用，后面讲Gradle的时候会大量接触闭包的概念。

            ```groovy
            // 闭包一般定义如下：
            def xxx = {
                paramters -> 
                   code
            }

            // 比如，我们定义一下闭包：
            def mClosure = {
                p1,p2 ->
                    println p1 +"..."+p2
            }

            // p1,p2也可以指定数据类型，如下：
            //闭包定义
            // ->前是参数定义， 后面是代码
            def mClosure = {
                String p1, int p2 ->
                    println p1 +"..."+p2
            }

            // 调用上述定义的闭包：
            mClosure("qwewe",100)
            mClosure.call("qwewe",100)

            // 看到这里有C经验的是不是瞬间想到函数指针了，连调用都非常像。定义闭包我们也可以不指定参数，如不指定则默认包含一个名为it的参数：
            def hello = { "Hello, $it!" }
            println hello("groovy")//Hello, groovy!

            // ===> 1.注意一点，在Groovy中，当函数的最后一个参数是闭包或者只有一个闭包参数，可以省略调用时的圆括号。比如：
            def fun(Closure closure){
                println "fun1"
                closure()
            }
            //调用方法
            fun({
                println "closure"
            })
            //可以不写()直接调用
            fun {
                println "closure"
            }

            // ===> 2. 或者如下
            def fun(int a , Closure closure){
                println "fun1 $a"
                closure()
            }
            //调用方法
            fun(1, {
                println "closure"
            })
            //可以不写()直接调用，这就有点奇葩了
            fun 1, {
                println "closure"
            }
            ```
    
    4. #### Groovy中的文件操作

        ?> 假设对in.txt与out.txt文件进行IO操作。

        + **读文件**

            读文件有三种操作：一次读取一行，读取全部返回字节数据，数据流的形式读取，我们分别看一下：

            - 一次读取一行：

                ```groovy
                def src = new File("in.txt")
                //每次读取一行
                src.eachLine{
                    //it就是每一行数据
                    it ->
                        println it
                }
                ```
            - 读取全部返回字节数据：

                ```groovy
                //一次读取全部：字节数组
                def bytes = src.getBytes()
                def str = new String(bytes)
                println "str::"+str
                ```
            - 数据流的形式读取:

                ```groovy
                //返回流，不用主动关闭
                src.withInputStream{
                    inStream ->
                       //操作。。。。
                }
                ```

        + **写文件**

            ?> 写文件的操作同样很简单，我们看下将in.txt文件拷贝到out.txt中怎么操作：

            ```groovy
            //写数据
            def des = new File("out.txt")
            des.withOutputStream{
                os->
                    os.leftShift(bytes)//左移在这里可以起到写入数据的作用
            }
            ```

            ?> 我们也可以逐行写入数据：

            ```groovy
            des.withWriter('utf-8') { writer ->
                writer.writeLine 'i'
                writer.writeLine 'am'
                writer.writeLine 'hello world'
            }
            ```
            
            ?> 我们也可以这样写入数据：

            ```groovy
            des << '''i love groovy'''
            ```
    
    5. #### XML解析

        <!-- tabs:start -->
        ##### **需要解析的xml文档**

        ```xml
        <response version-api="2.0">
            <value>
                <persons>
                    <person id = "1">
                        <name>zhangsan</name>
                        <age>12</age>
                    </person>
                    <person id = "2">
                        <name>lisi</name>
                        <age>23</age>
                    </person>
                </persons>
            </value>
        </response>
        ```

        ##### **groovy解析代码**

        ```groovy
        //xml解析
        def xmlFile = new File("test.xml")
        def parse = new XmlSlurper()
        def result =parse.parse(xmlFile)

        def p0 = result.value.persons.person[0]
        println p0['@id']
        println p0.name
        println p0.age
        ```
        <!-- tabs:end -->
        
    6. #### JSON处理

        <!-- tabs:start -->
        ##### **解析代码**
        ```groovy
        //Person类
        class Person{
            def first
            def last
        }

        def p = new Person(first: 'hello world',last: '456')
        //转换对象为json数据
        def jsonOutput = new JsonOutput()
        def json = jsonOutput.toJson(p)
        println(json)
        //格式化输出
        println(jsonOutput.prettyPrint(json))

        //解析json
        def slurper = new JsonSlurper()
        Person person = slurper.parseText(json)
        println person.first
        println person.last
        ```
        ##### **输出**
        ```shell
        {"first":"hello world","last":"456"}
        {
            "first": "hello world",
            "last": "456"
        }
        hello world
        456
        ```
        <!-- tabs:end -->

    ##### Reference
        * https://groovy-lang.org/documentation.html

        


* ### 认识 gradle

    + #### 初识

        ?> Gradle是一个开源的构建自动化工具，既然是用于项目构建，那它肯定要制定一些规则，在Gradle中每一个待编译的工程都称为Project，每个Project都可以引入自己需要的插件，引入插件的目的其实就是引入插件中包含的Task，一个Project可以引入多个插件，每个插件同样可以包含一个或多个Task，关于插件和Task我们后面还会单独细说。

        ![](/.images/devops/build/gradle/gradle-01.png '工程目录结构 :size=40%')

    + #### 模块

        ?> 工程my-gradle包含五个module：app，groovy-test, lib1，lib4, springboot。其中app是App module，而lib1，lib4均是library module。
        <br>相信现在大部分都是这种多module项目了，对于Gradle来说这种叫做multiprojects，看这意思是多projects的项目，那么对于Gradle来说有多少个项目呢？
        <br>我们可以通过`gradle projects`命令来查看有多少个projects：

        ```shell
        > Task :projects

        ------------------------------------------------------------
        Root project
        ------------------------------------------------------------

        Root project 'my-gradle'
        +--- Project ':app'
        +--- Project ':groovy-test'
        +--- Project ':lib1'
        +--- Project ':lib4'
        \--- Project ':springboot'
        ```

    + #### settings.gradle

        ?> 那Gradle怎么知道有多少个Project呢？在我们创建工程的时候在根目录下有个settings.gradle文件，我们看下其中内容：

        ```gradle
        rootProject.name = 'my-gradle'
        include 'app'
        include 'lib1'
        include 'lib4'
        include 'groovy-test'
        include 'springboot'
        ```

        ?> 默认只有app模块，之后我们新建的lib1,lib4模块都会自动包含进来，Gradle就是通过查看这个配置文件来确定我们工程有多少了project的，我们修改settings.gradle文件,删除 include 'lib4',然后在执行gradle projects命令，就不会出现':lib4'了。

    + #### build.gradle

        ?> 好了，现在我们对settings.gradle有了初步的认识，我们在看看build.gradle文件的作用，工程根目录下有个build.gradle，每个module下也有自己的build.gradle文件：
        记得刚接触Gradle的时候这几个build.gradle文件真是让我蒙圈，怎么这么多，都干什么的，相信很多同学刚开始都有这疑问。
        <br><br>上面我们提到my-gradle叫做Root project，其余四个moudule均叫做Project，其实在Gradle中GradleLearn被当做三个module的父级project，在父project的build.gradle中配置的信息可以作用在子project中，比如根目录build.gradle内容片段如下：

        ```gradle
        buildscript {
            repositories {
                google()
                jcenter()
            }
            dependencies {
                classpath 'com.android.tools.build:gradle:3.3.1'
            }
        }

        // 这里的意思就是配置此项目及其每个子项目的仓储库为google()与 jcenter()，这样我们就不用在每个子project的build.gradle中再单独配置了.
        // 否则我们需要为每个子project单独配置一下需要引入的插件的仓储库。
        allprojects {
            repositories {
                google()
                jcenter()
            }
        }

        task clean(type: Delete) {
            delete rootProject.buildDir
        }
        ```

        根project的build.gradle文件并不是必须的，我们甚至可以删除掉，其存在的意义主要就是将子project公共的配置提取到父build.gradle来统一管理，有点像“基类”的意思。
    
    + #### .gradle文件本质

        ?> 大家有没有想过这样一个问题为什么settings.gradle可以include子模块，又为什么build.gradle可以写成如上那样的配置呢。其实我们在settings.gradle与build.gradle中看似配置的信息其实都是调用对应对象的方法或者脚本块设置对应信息。
        <br><br>对应对象是什么？其实settings.gradle文件最终会被翻译为Settings对象，而build.gradle文件最终会被翻译为Project对象，build.gradle文件对应的就是每个project的配置。
        <br><br>Settings与Project在Gradle中都有对应的类，也就是说只有Gradle这个框架定义的我们才能用，至于Settings与Project都定义了什么我们只能查看其官方文档啊。

        - ##### Settings对象

            ?> 比如[Settings](https://docs.gradle.org/current/dsl/org.gradle.api.initialization.Settings.html)类中定义了include方法：

            ![](/.images/devops/build/gradle/gradle-02.png ':size=80%')

            ?> `include`方法[API](https://docs.gradle.org/current/dsl/org.gradle.api.initialization.Settings.html#org.gradle.api.initialization.Settings:include(java.lang.Iterable))说明

            ![](/.images/devops/build/gradle/gradle-03.png ':size=80%')

            ?> 看到了吧，我们每一个配置都是调用对应对象的方法。我还发现Settings类中定义了如下方法：`findProject(projectDir)`、`findProject(path)`方法。
            <br><br>那我们在**setting.gradle**文件里面追加上如下代码试一下：

            ```gradle
            ...
            def pro = findProject(':app')
            println '----------------------------'
            println pro.getPath()
            println '----------------------------'
            ```

            运行`gradle classes`结果如下:(输出了app这个project的信息。)

            ![](/.images/devops/build/gradle/gradle-04.png ':size=60%')

        - ##### Project对象

        ?> 每个build.gradle文件都会被翻译为对应的Project对象，build.gradle文件最重要的作用就是：

            1. 引入插件并配置相应信息
            2. 添加依赖信息
        
        引入插件重要的就是引入插件中包含的tasks，至于插件与tasks后面会详细讲到。
        <br>那怎么引入插件呢？
        
        [Project](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html)定义了[apply](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:apply(java.util.Map))函数供我们调用：

        ![](/.images/devops/build/gradle/gradle-05.png ':size=90%')

        平时我们看到的如下：`apply plugin: 'com.android.library'`,最终都是调用那个的上面apply方法。
        
        在Project类中有个很重要的方法，如下：

        ![](/.images/devops/build/gradle/gradle-06.png ':size=80%')

        这个方法在配置完当前project后立刻调用，并且传给我们project参数，我们调用试试，app module的build.gradle添加如下代码：

        ```gradle
        afterEvaluate {
            project ->
                println "app module -----> $project.name"
        }
        ```

        同样，运行`gradle classes`结果如下:

        ![](/.images/devops/build/gradle/gradle-07.png ':size=60%')

        - ##### Gradle对象

        ?> 在我们执行gradle相关命令的时候，Gradle框架会为我们创建一个gradle对象，并且整个工程只有这一个gradle对象，这个对象不像上面两个一样有对应文件，这个对象一般不需要我们配置什么，主要用于给我们提供一些工程信息，具体有哪些信息呢？查看文档中Gradle类包含以下信息：
        
        ![](/.images/devops/build/gradle/gradle-08.png ':size=80%')

        我们可以打印一些信息看看，在根build.gradle中添加如下代码：

        ```gradle
        println "homeDir = $gradle.gradleHomeDir"
        println "UserHomeDir = $gradle.gradleUserHomeDir"
        println "gradleVersion = $gradle.gradleVersion"
        ```

        运行`gradle classes`结果如下:

        ![](/.images/devops/build/gradle/gradle-09.png ':size=60%')


* ### gradle 工作时序

    ?> Gradle执行分为三个过程：

    1. #### Initiliazation

        *初始化阶段只要为每个module创建project实例。这个阶段settings.gradle文件会被解析执行*
    
    2. #### Configration

        *这个阶段解析每个模块的build.gradle文件，这个阶段完成后整个项目的tasks执行顺序也就确定了并且task准备就绪处于待执行状态，整个tasks任务会构成一个有向无环图。*
    
    3. #### 执行任务

        *这阶段就是按照顺序执行具体任务了。*

    + #### 测试流程

        ?> 在每个阶段我们可以通过gradle对象添加回调监听。我们在settings.gradle文件与每个module的build.gradle文件添加如下信息：

        <!-- tabs:start -->
        ##### **settings.gradle**
        ```gradle
        println "settings start"
        include ':app', ':lib1', ':lib4'
        println "settings end"
        ```

        ##### **build.gradle(root)**
        ```gradle
        afterEvaluate{
            project ->
                println "root module afterEvaluate -----> $project.name"
        }

        gradle.beforeProject{
            project ->
                println "beforeProject $project.name"
        }

        gradle.afterProject{
            project ->
                println "afterProject $project.name"
        }

        gradle.taskGraph.whenReady {
            println "taskGraph.whenReady"
        }

        gradle.buildFinished{
            result ->
                println "buildFinished"
        }
        ```

        ##### **build.gradle(app)**
        ```gradle
        println "app start"

        afterEvaluate{
            project ->
                println "app module afterEvaluate -----> $project.name"
        }

        println "app end"
        ```

        ##### **build.gradle(lib1)**
        ```gradle
        println "lib1 start"

        afterEvaluate{
            project ->
                println "lib1 module afterEvaluate -----> $project.name"
        }

        println "lib1 end"
        ```
        <!-- tabs:end -->
    
        ![](/.images/devops/build/gradle/gradle-10.png ':size=60%')
        ![](/.images/devops/build/gradle/gradle-11.png ':size=38%')

        !> 注意：root build.gradle中没有出现**beforeProject,afterProject**是因为需要解析配置这个文件后才会给gradle添加回调监听。如果放在`settings.gradle`就正常了。


* ### gralde 中的task

* ### gradle 中任务的增量构建

* ### 自定义插件

* ### 总结

## Reference
* https://www.cnblogs.com/leipDao/p/10385155.html
* https://blog.csdn.net/qq_16386581/article/details/86982278