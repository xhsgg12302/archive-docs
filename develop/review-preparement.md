## TODO
	
	- [x] 垃圾回收器和算法
	- [ ] spring,springmvc,springboot,springcloud
	- [x] 熔断，限流
	- [ ] netty
	- [ ] 自己代码的一些实现【activemq】【线程网络监听】【nettydemo】
	- [ ] beanfactory ,factorybean 
	- [ ] spring bean 初始化，声明周期
	- [ ] springmvc 流程
	- [ ] mysql 索引【btree b+ tree 】 ,,索引存储结构
	- [ ] redis 存储结构
	- [ ] 线程池拒绝策略
	- [ ] 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

	```
	deleting proxy.pac
	deleting include_robots
	deleting include_ico
	deleting favicon.ico
	deleting docker.html
	deleting _service_.html
	deleting 50x.html
	deleting 404.html
	./
	```


	```java
	public class HelloWorld{
		public static void main(String[] args){
			System.out.println("Hello world!");
		}
	}
	```

## TODO 2
	
	- [ ] String.intern(), JVM堆栈。
	- [ ] 设计模式
	- [ ] 消息队列
	- [ ] redis,雪崩，击穿。mysql 数据一致性
	- [ ] tomcat。
	- [ ] spring mvc ,spring.

## 自我介绍，
    面试官你好，我叫王堂福，2018毕业于牡丹江师范学院软件工程专业。
    今天面试的岗位是Java软件开发。
    
    在日常工作中，除了编写相应的模块代码和bug修复以外，还能够优化系统结构或者处理逻辑等。
    自己除了日常工作中的Java编码外，还喜欢学习嵌入式，汇编，网络通信等知识。
    
    以上就是我的自我介绍。


## 一些日常问题：

- 为什么从上一家公司离职？

- 你还有什么要问我的吗？
    + 从刚才的面试状态来说，我要胜任这份工作，还有哪些需要补充的？
    + 目前在做的项目或者使用的一些框架
    + 公司的有没有什么晋升机制？

- HR：
    1. 五险一金
    2. 每个月的工资构成（底薪+绩效+餐补+交通补等等）
    3. 12个月的基本薪资+季度奖金+年终奖等等
    4. 涨薪机制（一年有多少次考核晋升调薪机会、KPI考核）、期权等等。
    5. 加班，出差补助？
    6. 报道需求（证明，体检）【事先准备】

- leetcode 算法



## 面试大纲


1. ## jvm

    * ### 内存模型
    
        + 方法区
            
            > class类信息，静态变量和常量池（编译在.class文件中），常量池是方法区的一部分</p> Integer常量池，自动装箱，拆箱操作，【-128～127】之间的值在常量池中获取</p>
            String常量池，"a"+"b"，jvm自动优化"ab", rst + "b" --> StringBuilder().append.toString() 会return new String(). [final rst + "b"]不会返回String对象，另外还有一个String.intern()方法：这个方法会先在常量池中查看是否有对应的"***"字符，如果有的话，返回引用。没有的话，加入一个字符串在常量池并返回引用 
            
        + 堆
        
            > 年轻代（伊甸园4/5 + 存活区【TO + FROM】）1/3  Minor GC + 年老代 FULL GC | System.gc() </p>
            常用JVM参数设置：新生代初始容量大小，新生代最大容量，新生代和年老代的比值，伊甸园和幸存区比值，Tenuring（任期）比例，dump到文件，文件位置，后续操作等
            
        + 虚拟机栈
            - 局部变量表
            
                > 帧栈中，由局部变量表中存储数据</p>
                局部变量表所需的内存空间在编译期间完成分配</p>
                存储的单位是32bit【SLOT】,大于32的可以分配连续的槽 </p>
                根据索引的方式定位局部变量表,下标从0开始</p>
                静态方法和非静态方法的index 0 表示的含义不一样，非静态表示当前对象的引用</p>
                slot复用，会影响GC.
                
            - 操作数栈
            
                > 先进后出的数据存储结构，可以存储任意的Java数据类型。用来进行算术运算和参数传递等操作以及用来计算的临时存储区域。【iload_0,iadd,istore_2,iretrun】</p>
                有可能引发的异常【StackoverflowError 递归调用 | OutOfMemoryError 动态扩展】</p>
                通过 -Xss 设置线程栈大小，通常几百K.
                
            - 动态链接
            - 方法出口
        + 本地方法栈
        
            > 类比虚拟机栈，不过这个是针对本地语言(C/C++)，方法一般由native 修饰。
            
        + 程序计数器
            > 一块较小的内存空间，可以看作是当前线程所需执行字节码的行号指示器，通常由执行引擎来执行程序计数器所指向的行号内容。更确切的说，一个线程的执行，是通过字节码解释器改变当前线程的计数器的值，来获取下一条需要执行的字节码指令，从而确保线程的正确执行。
        
    * ### 优化
        + 垃圾回收器
            ```
            https://blog.csdn.net/high2011/article/details/80177473
            ```
        
    
2. ## 数据库 
    * ### [redis 链接](https://note.youdao.com/web/#/file/recent/markdown/WEB3ce6a1aeea0f04cf6dee812f62a06746/)
    
    * ### 事务介绍（transaction）

        > 事务：一个最小的不可再分的工作单元；通常一个事务对应一个完成的业务（例如银行账户转账业务）</p>
        一个完成的业务需要批量的DML（insert，update，delete）语句共同完成  </p>
        事务只和DML语句有关，或者说DML语句才有事务。这个和业务逻辑有关，业务逻辑不同DML语句的个数不同
        
        > 事务的四大特征（ACID） </p>
        原子性（A）：事务是最小单位，不可再分 </p>
        一致性（C）：事务执行前后数据库状态保持一致 </p>
        隔离性（I）：事务A和事务B之间具有隔离性，一个操作不会影响到另外一个 </p>
        持久性（D）：是事务的保证，事务终结的标志（内存的数据持久到硬盘文件中） </p>
        
        > 事务的执行，状态，和操作  </p>
        在mysql中，默认事务是自动提交的，也就是说，只要执行了一条DML语句就开启了事务，并且提交了事务 </p>
        以上自动提交机制是可以关闭的
        
        > 事务成功 </p>
        mysql>start transaction;#手动开启事务 </p>
        mysql>insert/update/delete </p>
        mysql>commit;#commit之后即可改变磁盘文件中的数据
        
        > 事务失败 </p>
        mysql>start transaction; </p>
        mysql>insert/update/delete; </p>
        mysql>rollback;
        
        > 事务操作 </p>
        set autocommit=0;#当前session禁用自动提交事务，自此每次都需要显示的commit </p>
        show variables like '%autocom%'; </p>
        start transaction; </p>
        commit; </p>
        rollback; </p>
        select @@tx_isolation; </p>
        select @@global.tx_isolation; </p>
        set [global] transaction isolation level repeatable read; </p>
        设置隔离级别一定在事务开启之前，并且上面设置支队当前连接或者黑窗口有效。
        
    * ### mysql数据库提供的四种事务隔离级别
        
        1. 读未提交（read uncommitted）
            > 事务A和事务B，事务A未提交的数据，事务B可以读到 </p>
            这里读取到的数据叫做“脏数据” </p>
            这种隔离级别最低，这种级别一般在理论上存在，数据库的一般都高于这个级别
            
        2. 读已提交（read committed）
            > 事务A和事务B，事务A提交的数据，事务B才能读取到 </p>
            这种隔离级别可以避免脏数据 </p>
            这种级别会导致不可重复读,oracle数据库默认隔离级别 </p>
            @不可重复读：B读取一条记录，A修改后提交，B可以读取到，发现和刚才不一致
            
        3. 可重复读（repeatable read）
            > 事务A和事务B，事务A提交之后的数据，事务B读取不到 </p>
            这个隔离级别可以可以避免不可重复读 </p>
            但是会出现幻读，mysql默认隔离级别。 </p>
            @幻读：A事务插入一条记录x并且提交，B读取记录，发现为empty。由于B读取不到，所以继续插入x，此时会报重复主键异常。B感觉很虚幻，明明刚才没有。
            
        4. 串行化（serializable）
            > 事务A和事务B，事务A在操作数据库时，事务B只能排队等待 </p>
            这中隔离级别很少使用，吞吐量太低，用户体验太差
            
    * ### 主从复制
        - 概念
            > 将主数据库中的DDL和DML操作通过二进制日志传输到从数据库上，然后将这些日志重新执行；从而使得主从数据库保持一致。 </p>
            基本原理：mysql支持单向，异步复制，复制过程中一个服务器充当主服务器，而一个或多个充当从服务器，当一个从服务器连接到主服务器时，它通知主服务器从最后一次更新成功的位置，从服务器接受从那时更新的任何数据，并在本地执行相同的更新。
        
        - 作用
            > 主数据库出现问题，可以切换到从数据库 </p>
            可以进行数据库层面的读写分离 </p>
            可以在从数据库上进行日志备份。
            
        - 过程
            > Binary log: 主数据库的二进制日志 </p>
            Relay log: 从服务器的中继日志 </p>
            第一步：master在每个事务更新数据完成之前，将该操作记录串行的写到binlog中 </p>
            第二部：slave开启一个I/O thread,该线程在master打开一个普通链接，主要工作是binlog dump process.如果读取的进度已经跟上了master，就进入睡眠状态并等待master产生新的事件，I/O thread 的最终目的是将这些时间写入到中继日志中。 </p>
            第三步：从服务器的SQLthread读取中继日志，并顺序执行该日志中的sql事件。
            
        - 具体操作
        
            + 主从复制分别做一下操作
                - 1.1，版本一致
                - 1.2，初始化表，并在后台启动mysql
                - 1.3，修改root的密码
                
            + 修改主服务器master
                ```
                #vim /etc/my.cnf
                [mysqld]
                log-bin=mysql-bin //[必须]启动二进制日志
                server-id=222   //[必须]服务器的唯一ID，默认是1，一般取IP最后一个字节
                ```
            + 修改从服务器slave
                ```
                #vim /etc/my.cnf
                [mysqld]
                log-bin=mysql-bin //[不是必须]启动二进制日志
                server-id=226   //[必须]服务器的唯一ID，默认是1，一般取IP最后一个字节
                ```
            + 重启两台服务器的mysql
                ```
                service mysql restart
                ```
            + 在主服务器上建立账户并授权slave
                ```
                GRANT REPLICATION SLAVE ON *.* to 'mysync'@'%' identified by '123456'
                一般不用root账号，表示所有客户端都可能连，只要账号密码正确，此处%可以用具体客户端IP代替，加强安全。
                ```
            + 登录主服务器的mysql，查询master状态
                ```
                show master status\G
                执行完此步骤后不要再操作主服务器mysql，防止主服务器状态值变化
                ```
            + 配置从服务器slave
                ```
                change master to master_host='192.168.145.222',master_user='mysync',master_password='123456',master_log_file='mysql-bin.0000004',master_log_pos=308;
                
                start slave;
                ```
            + 检查从服务器复制功能状态
                ```
                show slave status\G
                注意，Slave_IO_RUNNING,Slave_SQL_RUNNING 进程必须正常运行，即YES状态，否则都是错误的状态。
                ```
            
    * ### 读写分离
        - 在主从复制的基础上进行读写分离
        - 两种路有方法，第一种是第三方工具（MaxScale,amonde,Mysql_Proxy），第二中是应用路有
        
    * ### 存储过程，触发器
    * ### 数据库索引
        > 一般使用平衡树（多路搜索树）【BTREE，B+Tree】
        > 建立索引后，会影响where后面字段的查询速度，不用全表扫描，一般在where和join中的出现的列需要建立索引。但是并不是所有情况 ，因为mysql只对，<,<=,=,>,>=，Between，in，以及某些时候的like才会使用索引。（%,_）
    
        - 普通索引（hash索引，全文索引，R_TREE索引）
        - 唯一索引（索引列值必须唯一，但允许有NULL值）
        - 主键索引（一种特殊的唯一索引，不允许有NULL值）
        - 复合索引（多个单值索引，自动选取它认为最有效的。【[最左前缀](https://blog.csdn.net/sinat_41917109/article/details/88944290)】）
        - [聚集索引，非聚集索引](https://blog.csdn.net/qq_35818427/article/details/103886635)
        - 注意事项（会使索引失效）
            + 不要在索引列上进行计算
            + 不要使用Not in,<>等操作
        - 不足之处（1，影响更新的速度，2.需要给索引建立额外的磁盘空间）
        
    * ### mysql优化
        - 选取最适用的字段属性
            ```
            MySQL可以很好的支持大数据量的存取，但是一般说来，数据库中的表越小，在它上面执行的查询也就会越快。因此，在创建表的时候，为了获得更好的性能，我们可以将表中字段的宽度设得尽可能小。

            例如，在定义邮政编码这个字段时，如果将其设置为CHAR(255),显然给数据库增加了不必要的空间，甚至使用VARCHAR这种类型也是多余的，因为CHAR(6)就可以很好的完成任务了。同样的，如果可以的话，我们应该使用MEDIUMINT而不是BIGIN来定义整型字段。
            
            另外一个提高效率的方法是在可能的情况下，应该尽量把字段设置为NOTNULL，这样在将来执行查询的时候，数据库不用去比较NULL值。
            
            对于某些文本字段，例如“省份”或者“性别”，我们可以将它们定义为ENUM类型。因为在MySQL中，ENUM类型被当作数值型数据来处理，而数值型数据被处理起来的速度要比文本类型快得多。这样，我们又可以提高数据库的性能。
            ```
        - 适用连接（JOIN）来代替子查询
            ```
            MySQL从4.1开始支持SQL的子查询。这个技术可以使用SELECT语句来创建一个单列的查询结果，然后把这个结果作为过滤条件用在另一个查询中。例如，我们要将客户基本信息表中没有任何订单的客户删除掉，就可以利用子查询先从销售信息表中将所有发出订单的客户ID取出来，然后将结果传递给主查询，如下所示：

            DELETE FROM customerinfo
            WHERE CustomerIDNOT in ( SELECT CustomerID FROM salesinfo)
            使用子查询可以一次性的完成很多逻辑上需要多个步骤才能完成的SQL操作，同时也可以避免事务或者表锁死，并且写起来也很容易。但是，有些情况下，子查询可以被更有效率的连接（JOIN）..替代。例如，假设我们要将所有没有订单记录的用户取出来，可以用下面这个查询完成：
            
            SELECT * FROM customerinfo
            WHERE CustomerIDNOT in ( SELECT CustomerID FROM salesinfo)
            如果使用连接（JOIN）..来完成这个查询工作，速度将会快很多。尤其是当salesinfo表中对CustomerID建有索引的话，性能将会更好，查询如下：
            SELECT * FROM customerinfo
            LEFTJOIN salesinfo 
            ON customerinfo.CustomerID=salesinfo.CustomerID
            WHERE salesinfo.CustomerID IS NULL
            连接（JOIN）..之所以更有效率一些，是因为MySQL不需要在内存中创建临时表来完成这个逻辑上的需要两个步骤的查询工作。
            ```
        - 使用联合（union）来代替手动创建的临时表
            ```
            MySQL从4.0的版本开始支持union查询，它可以把需要使用临时表的两条或更多的select查询合并的一个查询中。在客户端的查询会话结束的时候，临时表会被自动删除，从而保证数据库整齐、高效。使用union来创建查询的时候，我们只需要用UNION作为关键字把多个select语句连接起来就可以了，要注意的是所有select语句中的字段数目要想同。下面的例子就演示了一个使用UNION的查询。
            
            SELECT Name,Phone FROM client
            UNION
            SELECT Name,BirthDate FROM author
            UNION
            SELECT Name,Supplier FROM product
            ```
        - 事务
            ```
            尽管我们可以使用子查询（Sub-Queries）、连接（JOIN）和联合（UNION）来创建各种各样的查询，但不是所有的数据库操作都可以只用一条或少数几条SQL语句就可以完成的。更多的时候是需要用到一系列的语句来完成某种工作。但是在这种情况下，当这个语句块中的某一条语句运行出错的时候，整个语句块的操作就会变得不确定起来。设想一下，要把某个数据同时插入两个相关联的表中，可能会出现这样的情况：第一个表中成功更新后，数据库突然出现意外状况，造成第二个表中的操作没有完成，这样，就会造成数据的不完整，甚至会破坏数据库中的数据。要避免这种情况，就应该使用事务，它的作用是：要么语句块中每条语句都操作成功，要么都失败。换句话说，就是可以保持数据库中数据的一致性和完整性。事物以BEGIN关键字开始，COMMIT关键字结束。在这之间的一条SQL操作失败，那么，ROLLBACK命令就可以把数据库恢复到BEGIN开始之前的状态。

            BEGIN;
             INSERT INTO salesinfo SET CustomerID=14;
             UPDATE inventory SET Quantity=11 WHERE item='book';
            COMMIT;
            
            事务的另一个重要作用是当多个用户同时使用相同的数据源时，它可以利用锁定数据库的方法来为用户提供一种安全的访问方式，这样可以保证用户的操作不被其它的用户所干扰。
            ```
        - 锁定表
            ```
            尽管事务是维护数据库完整性的一个非常好的方法，但却因为它的独占性，有时会影响数
            据库的性能，尤其是在很大的应用系统中。由于在事务执行的过程中，数据库将会被锁定
            ，因此其它的用户请求只能暂时等待直到该事务结束。如果一个数据库系统只有少数几个
            用户来使用，事务造成的影响不会成为一个太大的问题；但假设有成千上万的用户同时访
            问一个数据库系统，例如访问一个电子商务网站，就会产生比较严重的响应延迟。

            其实，有些情况下我们可以通过锁定表的方法来获得更好的性能。下面的例子就用锁定表
            的方法来完成前面一个例子中事务的功能。
            
            LOCK TABLE inventory WRITE SELECT Quantity FROM inventory WHERE Item='book';
            ...
            UPDATE inventory SET Quantity=11 WHERE Item='book';
            UNLOCK TABLES
            这里，我们用一个select语句取出初始数据，通过一些计算，用update语句将新值更新到
            表中。包含有WRITE关键字的LOCKTABLE语句可以保证在UNLOCKTABLES命令被执行之前，不
            会有其它的访问来对inventory进行插入、更新或者删除的操作。
            ```
        - 使用外键
            ```
            锁定表的方法可以维护数据的完整性，但是它却不能保证数据的关联性。这个时候我们就
            可以使用外键。

            例如，外键可以保证每一条销售记录都指向某一个存在的客户。在这里，外键可以把custo
            merinfo表中的CustomerID映射到salesinfo表中CustomerID，任何一条没有合法CustomerI
            D的记录都不会被更新或插入到salesinfo中。
            
            CREATE TABLE customerinfo(
                CustomerID INT NOT NULL,PRIMARYKEY(CustomerID)
            )TYPE=INNODB;
            CREATE TABLE salesinfo( 
                SalesID INT NOT NULL,
                CustomerID INT NOT NULL,
                PRIMARYKEY(CustomerID,SalesID),
                FOREIGNKEY(CustomerID) REFERENCES customerinfo(CustomerID)
                ON DELETE CASCADE
            )TYPE=INNODB;　
            注意例子中的参数“ONDELETECASCADE”。该参数保证当customerinfo表中的一条客户记录
            被删除的时候，salesinfo表中所有与该客户相关的记录也会被自动删除。如果要在MySQL
            中使用外键，一定要记住在创建表的时候将表的类型定义为事务安全表InnoDB类型。该类
            型不是MySQL表的默认类型。定义的方法是在CREATETABLE语句中加上TYPE=INNODB。如例中
            所示。
            ```
        - 使用索引
            ```
            索引是提高数据库性能的常用方法，它可以令数据库服务器以比没有索引快得多的速度检
            索特定的行，尤其是在查询语句当中包含有MAX(),MIN()和ORDERBY这些命令的时候，性能
            提高更为明显。

            那该对哪些字段建立索引呢？
            
            一般说来，索引应建立在那些将用于JOIN,WHERE判断和ORDERBY排序的字段上。尽量不要对
            数据库中某个含有大量重复的值的字段建立索引。对于一个ENUM类型的字段来说，出现大
            量重复值是很有可能的情况
            
            例如customerinfo中的“province”..字段，在这样的字段上建立索引将不会有什么帮
            助；相反，还有可能降低数据库的性能。我们在创建表的时候可以同时创建合适的索引，
            也可以使用ALTERTABLE或CREATEINDEX在以后创建索引。此外，MySQL从版本3.23.23开始支
            持全文索引和搜索。全文索引在MySQL中是一个FULLTEXT类型索引，但仅能用于MyISAM类型
            的表。对于一个大的数据库，将数据装载到一个没有FULLTEXT索引的表中，然后再使用ALT
            ERTABLE或CREATEINDEX创建索引，将是非常快的。但如果将数据装载到一个已经有FULLTEX
            T索引的表中，执行过程将会非常慢。
            ```
        - 优化查询语句
            ```
            绝大多数情况下，使用索引可以提高查询的速度，但如果SQL语句使用不恰当的话，索引将无法发挥它应有的作用。

            下面是应该注意的几个方面。
            
            　　a、 首先，最好是在相同类型的字段间进行比较的操作
            
            　　在MySQL3.23版之前，这甚至是一个必须的条件。例如不能将一个建有索引的INT字段和BIGINT字段进行比较；但是作为特殊的情况，在CHAR类型的字段和VARCHAR类型字段的字段大小相同的时候，可以将它们进行比较。
            
            　　b、 其次，在建有索引的字段上尽量不要使用函数进行操作
            
            　　例如，在一个DATE类型的字段上使用YEAE()函数时，将会使索引不能发挥应有的作用。所以，下面的两个查询虽然返回的结果一样，但后者要比前者快得多。
            
            　　ｃ、第三，在搜索字符型字段时，我们有时会使用LIKE关键字和通配符，这种做法虽然简单，但却也是以牺牲系统性能为代价的
            
            例如下面的查询将会比较表中的每一条记录。
            
            SELECT * FROM books WHERE name like "MySQL%"
            但是如果换用下面的查询，返回的结果一样，但速度就要快上很多：
            
            SELECT * FROM books WHERE name＞="MySQL" and name＜"MySQM"
            最后，应该注意避免在查询中让MySQL进行自动类型转换，因为转换过程也会使索引变得不起作用。
            ```
        
    * ### 执行计划（explain）
        > 通过explain命令我们可以知道以下信息：表的读取顺序，数据读取操作的类型，哪些索引可以使用，哪些索引实际使用了，表之间的引用，每张表有多少行被优化器查询等信息。

    * ### [reference](https://www.cnblogs.com/zhoujinyi/p/3437475.html)

3. ## java基础

    * ### 线程，线程池，FutureTask
    
    * ### 过滤器和拦截器
    
    * ### ElasticSearch
    
    * ### netty [ [doug lea描述] ](http://gee.cs.oswego.edu/dl/cpjslides/nio.pdf)
        - netty 是什么？
            > Netty 是一个 基于 NIO的client-server(客户端服务器)框架，使用它可以快速简单地开发网络应用程序。</p>
            它极大地简化并优化了 TCP 和 UDP套接字服务器等网络编程,并且性能以及安全性等很多方面甚至都要更好。</p>
            支持多种协议 如 FTP，SMTP，HTTP 以及各种二进制和基于文本的传统协议。
            
        - 为什么要用netty？
            > 统一的 API，支持多种传输类型，阻塞和非阻塞的。</p>
            简单而强大的线程模型。</p>
            自带编解码器解决 TCP 粘包/拆包问题。</p>
            自带各种协议栈。</p>
            真正的无连接数据包套接字支持。</p>
            比直接使用 Java 核心 API 有更高的吞吐量、更低的延迟、更低的资源消耗和更少的内存复制。</p>
            安全性不错，有完整的 SSL/TLS 以及 StartTLS 支持。</p>
            社区活跃
            成熟稳定，经历了大型项目的使用和考验，而且很多开源项目都使用到了 Netty， 比如我们经常接触的 Dubbo、RocketMQ 等等。

        - netty 应用场景
            > 作为 RPC 框架的网络通信工具 </p>
            实现一个自己的 HTTP 服务器  </p>
            实现一个即时通讯系统 </p>
            实现消息推送系统 
            
        - netty 核心组件有哪些？分别有什么作用
        - EventloopGroup了解么？和Eventloop有什么关系？
        - BootStrap和ServerBootStrap了解么？
        - NioEventLoopGroup 默认的构造函数会起多少线程？
        - Netty 线程模型了解吗？
        - netty 服务端和客户端的启动过过程了解吗？
        - netty 长链接和心跳机制？
        - netty 零拷贝？
        - netty中空轮询BUG
            > 原因：Selector轮询结果为空，且没有wakeup或者新消息处理，就会触发空轮询，cpu100% </p>
            解决：对select操作周期进行统计，设置阈值（512），超过的话，将原来Selector上面注册的SocketChannel移至新的Selector上面，并把旧的关闭。
    
    * ### 玛氏病IO总结
    
    * ### zookeeper
        - zookeeper概述
            > 分布式协调服务框架
            
        - 常用命令
            | command | function |
            -|-
            | help | 显示所有命令操作 |
            | ls path [watch] | 使用ls 命令来查看当前znode中所包含的内容 |
            | ls2 path [watch] | 查看当前节点数据，并能看到更新次数等数据 |
            | create | 普通创建 -s 含有序列，-e 临时（重启或者超时消失）|
            | get path [watch] | 获得节点值 |
            | set | 设置节点具体值 |
            | stat | 查看节点状态 |
            | delete | 删除节点 | 
            | rmr | 递归删除节点 | 
        - 应用场景
            + 统一命名服务（类似于dns）
            + 统一配置管理（将配置注册到znode上，各个客户端进行监听）
            + 统一集群管理（将服务器信息写入一个znode中，继续监听）
            + 软负载均衡（）
            + 分布式锁
        - zookeeper内部原理
            + 3.1 选举机制
            + 节点类型
                ```
                1，持久（Persistent）客户端于zk断开连接后，创建的节点不删除
                2，短暂（Ephemeral）于zk断开后，创建的节点自动删除
                顺序编号
                ```
            + Stat结构体
            + 监听原理
                ```
                创建zk客户端后会出有两个线程，一个负责网路通信，一个负责监听
                通过connect线程将监听事件发给zk服务器，进而保存到监听列表中
                如果发生变化，则通知监听的客户端。
                listener线程调用process（）方法。
                
                常见的监听：
                1.监听节点数据的变化（get path [watch]）
                2.监听自节点增减的变化（ls path [watch]）
                ```
            + 写数据流程
                ```
                1.client向zk的server1 上写数据，发送一个写请求。
                2.如果server1不是leader，那么它会把接受的请求进一步转发给leader，这个leader将请求广播给其他zk，他们会将写请求加入待写队列。并向leader发送成功信息
                3.当leader收到半数以上的成功信息。说明写操作可以执行。leader会向其他发送提交信息。然后他们会将待写队列中的信息真正操作。此时写成功
                4.【server1】进一步通知client数据写入成功。
                ```
            
        - 真实面试题
            + 简述zookeeper的选举机制
            + zookeeper的监听原理是什么？
            + zookeeper的部署方式有哪几种？
            + zookeeper常用命令
    
    * ### tomcat
    
        - 整体架构
            + Connector（【coyote】处理socket连接，负责网络字节流）
            + Container（【Catalina】加载servlet处理具体请求）
        - 启动流程
            > 先是初始化</p>
            BootStrap->Calaline->Server->Service </p>
            BootStrap->Calaline->Server->Service->[Engine->Host->Context] </p>
            BootStrap->Calaline->Server->Service->[Executor] </p>
            BootStrap->Calaline->Server->Service->[ProtocalHandler] </p>
            > 然后调用start启动整个tomcat
            
        - I/O模型和协议
            + BIO，NIO，AIO（NIO2），APR
            + HTTP/1.1，HTTP/2，AJP（用于和apache服务器集成用来处理静态资源）
        - Jasper引擎
            > index.jsp -> jspServlet(找到文件) -> 渲染成servlet.class-> 加载调用
            
        - Context
            > 官方不建议直接在server.xml中配置，因为，server.xml文件只会在tomcat重新的时候重新加载。
            
            + 1, In an individual file at /META-INF/context.xml inside the application files
            + 2, In individual files (with a ".xml" extension) in the $CATALINA_BASE/conf/[enginename]/[hostname]/ directory
            + 3, Inside a Host element in the main conf/server.xml.
        
        - 安全
            + 删除webapp下面的管理页面和其他项目
            + 禁用server-user.xml里面的权限或者直接删除
            + 修改tomcat默认关机指令，或者禁用
            + 配置错误页面。
    
    
    * ### 各种锁，自旋锁，偏向锁（CAS）
        > 偏向锁 --> 自旋锁（无锁）（CAS）--> sync（重量级锁） </p>
        偏向锁设计涞源，根据工业统计，百分之七八十的情况下都是单线程在操作，剩下的情况是多个线程，所以首次过来使用的线程会将自己的线程ID写入对象头，表示正在占用，剩余线程过来的时候，去除此标志，然后自旋等待。据说设计挺复杂。 </p>
        JUC里面大量使用自旋锁，比如AtomicInteger方法自增的时候，调用increaseandset()-->native方法，通过查看hotspotCpp源码，发现这个方法体里面是汇编实现的，通过汇编指令，comxchan,单个cpu的话，这个操作就是原子性的，但是多个处理器的话，就保证不了了，所以汇编代码里面又添加了前缀指令，lock-if_mp,专门处理，mutil process这种情况。 </p>
        JDK1.5以后，sync优化了，存在锁升级的过程。所以能用synchronized解决的，尽量用synchronized。
    
    
    * ### I/O
    
        | type | implements |
        -|-
        | InputStream | ByteArrayInputStream </p> PipedInputStream </p> FileInputStream </p> ObjectInputStream </p> FilterInputStream --> BufferedInputStream </p> --------------------> DataInputStream |
        | OutputStream| ByteArrayOutputStream </p> PipedOutputStream </p> FileOutputStream </p> ObjectOutputStream </p> FilterOutputStream --> BufferedOutputStream </p> ----------------------> DataOutputStream </p> ----------------------> ==PrintStream==|
        | Reader | CharArrayReader </p> PipedReader </p> FilterReader </p> BufferedReader </p> InputStreamReader --> FileReader |
        | Writer | CharArrayWriter </p> PipedWriter  </p> FilterWriter </p> BufferedWriter </p> OutputStreamWriter --> FileWriter </p> ==PrintWriter== |
    
    * ### 深浅拷贝（两种方式）
        + 实现Cloneable接口并重写clone方法
            ```
            public class Student implements Cloneable{
                private int sno ;
                private String name;
            
                //getter ,setter 省略
            
                @Override
                public Object clone() throws CloneNotSupportedException {
                    Student s = null;
                    try{
                        s = (Student)super.clone();
                    }catch (Exception e){
                        e.printStackTrace();
                    }
                    return s;
                }
            
            }
            
            此种克隆方式如果对象里面存在引用的话，会出现浅拷贝，比如新增一个Teacher属性
            解决方式，将Teacher对象也处理一下，然后修改Student中的clone逻辑
            ```
        + 通过序列化的方式完成深拷贝
            ```
            public class MyUtil {
            
                private MyUtil() {
                    throw new AssertionError();
                }
            
                @SuppressWarnings("unchecked")
                public static <T extends Serializable> T clone(T obj) throws Exception {
                    ByteArrayOutputStream bout = new ByteArrayOutputStream();
                    ObjectOutputStream oos = new ObjectOutputStream(bout);
                    oos.writeObject(obj);
            
                    ByteArrayInputStream bin = new ByteArrayInputStream(bout.toByteArray());
                    ObjectInputStream ois = new ObjectInputStream(bin);
                    return (T) ois.readObject();
            
                    // 说明：调用ByteArrayInputStream或ByteArrayOutputStream对象的close方法没有任何意义
                    // 这两个基于内存的流只要垃圾回收器清理对象就能够释放资源，这一点不同于对外部资源（如文件流）的释放
                }
            }
            
            需要注意的是，Teacher对象也需要实现序列化接口。
            ```

4. ## spring啊（IOC，AOP）

    * ### springboot启动流程
        > https://www.jianshu.com/p/603d125f21b3

    * ### spring bean 初始化流程
        > 一二三级缓存
        
    * ### srping 事务
        >  事务传播机制(七种)
        
        name | intro
        -- | --
        propagation_never |  没有正常执行，有抛异常
        propagation_not_supported | 没有正常执行，有挂起
        propagation_supports | 没有正常执行，有就用
        propagation_requires_new | 没有新建，有新建&&挂起原来的
        propagation_nested  | 没有新建，有内嵌原来的
        propagation_required | 没有新建，有加入原来的
        propagation_mandatory | 没有抛异常，有使用原来的
        
    * ### spring循环依赖
    * ### spring异常
    
    * ### FactoryBean,BeanFactory
        > BeanFactory是Spring容器的顶级接口，生产和管理bean，是容器的基本规范。</p>
        FactoryBean 用来实现一个工厂bean 对象。自定义复杂bean逻辑，通过getObject 获取修饰的bean对象，使用'&'可以获取到这个bean对象。

5. ## mybatis

6. ## 熔断，限流


