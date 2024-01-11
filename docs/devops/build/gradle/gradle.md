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
        
    6. #### Json

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
    7. #### Reference
        * https://groovy-lang.org/documentation.html

        


### 认识 gradle

### gradle 工作时序

### gralde 中的task

### gradle 中任务的增量构建

### 自定义插件

### 总结

## Reference
* https://blog.csdn.net/qq_16386581/article/details/86982278