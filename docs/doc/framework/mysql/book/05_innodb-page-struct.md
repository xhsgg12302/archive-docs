* ## 不同类型的页简介
?> 前边我们简单提了一下 页 的概念，它是 InnoDB 管理存储空间的基本单位，一个页的大小一般是 16KB 。InnoDB 为了不同的目的而设计了许多种不同类型的 页 ，比如存放表空间头部信息的页，存放 Insert Buffer信息的页，存放 INODE 信息的页，存放 undo 日志信息的页等等等等。当然了，如果我说的这些名词你一个都没
有听过，就当我放了个屁吧～ 不过这没有一毛钱关系，我们今儿个也不准备说这些类型的页，我们聚焦的是那些存放我们表中记录的那种类型的页，官方称这种存放记录的页为索引（ INDEX ）页，鉴于我们还没有了解过索引是个什么东西，而这些表中的记录就是我们日常口中所称的 数据 ，所以目前还是叫这种存放记录的页为 数据页吧。

* ## 数据页结构的快速预览

    <!-- panels:start -->
    <!-- div:left-panel-40 -->
    ?> 数据页代表的这块 16KB 大小的存储空间可以被划分为多个部分，不同部分有不同的功能，各个部分如图所示：

    ![](/.images/doc/framework/mysql/book/05_innodb_page_struct/ips-01.png ':size=65%')
    <!-- div:right-panel-60 -->
    ?> 从图中可以看出，一个 InnoDB 数据页的存储空间大致被划分成了 7 个部分，有的部分占用的字节数是确定的，有的部分占用的字节数是不确定的。下边我们用表格的方式来大致描述一下这7个部分都存储一些啥内容（快速的瞅一眼就行了，后边会详细唠叨的）：

    ![](/.images/doc/framework/mysql/book/05_innodb_page_struct/ips-02.png ':size=100%')
    <!-- panels:end -->

* ## 记录在页中的存储

    ?> 在页的7个组成部分中，我们自己存储的记录会按照我们指定的 行格式 存储到 User Records 部分。但是在一开始生成页的时候，其实并没有 User Records 这个部分，每当我们插入一条记录，都会从 Free Space 部分，也就是尚未使用的存储空间中申请一个记录大小的空间划分到 User Records 部分，当 Free Space 部分的空间全部被 User Records 部分替代掉之后，也就意味着这个页使用完了，如果还有新的记录插入的话，就需要去申请新的页了，这个过程的图示如下：

    ![](/.images/doc/framework/mysql/book/05_innodb_page_struct/ips-03.png ':size=90% :align=center')

    > [!WARNING]为了更好的管理在 User Records 中的这些记录， InnoDB 可费了一番力气呢，在哪费力气了呢？不就是把记录按照指定的行格式一条一条摆在 User Records 部分么？其实这话还得从记录行格式的`记录头信息`中说起。

    + ### 记录头信息的秘密

        ?> 为了故事的顺利发展，我们先创建一个表：
        <br>`CREATE TABLE page_demo(c1 INT, c2 INT, c3 VARCHAR(10000), PRIMARY KEY (c1) ) CHARSET=ascii ROW_FORMAT=Compact;`
        <br><br>这个新创建的 page_demo 表有3个列，其中 c1 和 c2 列是用来存储整数的， c3 列是用来存储字符串的。需要注意的是，<span style='color: blue'>我们把 c1 列指定为主键，所以在具体的行格式中InnoDB就没必要为我们去创建那个所谓的 row_id 隐藏列了</span>。而且我们为这个表指定了 ascii 字符集以及 Compact 的行格式。所以这个表中记录的行格式示意图就是这样的：

        ![](/.images/doc/framework/mysql/book/05_innodb_page_struct/ips-04.png ':size=90% :align=center')

        ?> 从图中可以看到，我们特意把**记录头信息**的`5个字节(40bit)`的数据给标出来了，说明它很重要，我们再次先把这些**记录头信息**中各个属性的大体意思浏览一下（我们目前使用`Compact`行格式进行演示）：

        ![](/.images/doc/framework/mysql/book/05_innodb_page_struct/ips-05.png ':size=85% :align=center')

        <!-- panels:start -->
        <!-- div:title-panel -->
        ##### page_demo表行格式
        <!-- div:left-panel-30 -->
        ?> 由于我们现在主要在唠叨 记录头信息 的作用，所以为了大家理解上的方便，我们只在 page_demo 表的行格式演示图中画出有关的头信息属性以及 c1 、 c2 、 c3 列的信息（其他信息没画不代表它们不存在啊，只是为了理解上的方便在图中省略了～），简化后的行格式示意图就是这样：
        <!-- div:right-panel-50 -->
        ![](/.images/doc/framework/mysql/book/05_innodb_page_struct/ips-06.png ':size=100%')
        <!-- panels:end -->

        <!-- panels:start -->
        <!-- div:title-panel -->
        ##### page_demo表行格式
        <!-- div:left-panel-50 -->
        ?> 下边我们试着向 page_demo 表中插入几条记录：
        <br><br>`INSERT INTO page_demo VALUES(1, 100, 'aaaa'), (2, 200, 'bbbb'), (3, 300, 'cccc'), (4, 400, 'dddd');`
        <br><br>为了方便大家分析这些记录在 页 的 User Records 部分中是怎么表示的，我把记录中头信息和实际的列数据都用十进制表示出来了（其实是一堆二进制位），所以这些记录的示意图就是：
        <!-- div:right-panel-50 -->
        ![](/.images/doc/framework/mysql/book/05_innodb_page_struct/ips-07.png ':size=100%')
        <!-- panels:end -->

        ?> 看这个图的时候需要注意一下，各条记录在 User Records 中存储的时候并没有空隙，这里只是为了大家观看方便才把每条记录单独画在一行中。我们对照着这个图来看看记录头信息中的各个属性是啥意思：
        <br><br>`delete_mask`:
        <br>这个属性标记着当前记录是否被删除，占用1个二进制位，值为 0 的时候代表记录并没有被删除，为 1 的时候代表记录被删除掉了。啥？被删除的记录还在 页 中么？是的，摆在台面上的和背地里做的可能大相径庭，你以为它删除了，可它还在真实的磁盘上[摊手]（忽然想起冠希～）。这些被删除的记录之所以不立即从磁盘上移除，是因为移除它们之后把其他的记录在磁盘上重新排列需要性能消耗，所以只是打一个删除标记而已，所有被删除掉的记录都会组成一个所谓的 垃圾链表 ，在这个链表中的记录占用的空间称之为所谓的 可重用空间 ，之后如果有新记录插入到表中的话，可能把这些被删除的记录占用的存储空间覆盖掉。<span style='color: blue'> 将这个delete_mask位设置为1和将被删除的记录加入到垃圾链表中其实是两个阶段，我们后边在介绍事务的时候会详细唠叨删除操作的详细过程，稍安勿躁。</span>
        <br><br>`min_rec_mask`:
        <br>B+树的每层非叶子节点中的最小记录都会添加该标记，什么是个 B+ 树？什么是个非叶子节点？好吧，等会再聊这个问题。反正我们自己插入的四条记录的 min_rec_mask 值都是 0 ，意味着它们都不是 B+ 树的非叶子节点中的最小记录。
        <br><br>`n_owned`:
        <br>这个暂时保密，稍后它是主角～
        <br><br>`heap_no`:
        <br>这个属性表示当前记录在本 页 中的位置，从图中可以看出来，我们插入的4条记录在本 页 中的位置分别是： 2 、 3 、 4 、 5 。是不是少了点啥？是的，怎么不见 heap_no 值为 0 和 1 的记录呢？这其实是设计 InnoDB 的大叔们玩的一个小把戏，他们自动给每个页里边儿加了两个记录，由于这两个记录并不是我们自己插入的，所以有时候也称为 [伪记录](#伪记录infimumsupremum) 或者 虚拟记录 。这两个伪记录一个代表 最小记录 ，一个代表 最大记录 ，等一下哈~，记录可以比大小么？<br>是的，记录也可以比大小，对于一条完整的记录来说，比较记录的大小就是比较 主键 的大小。比方说我们插入的4行记录的主键值分别是： 1 、 2 、 3 、 4 ，这也就意味着这4条记录的大小从小到大依次递增。<span style='color: blue'> 请注意我强调了对于`一条完整的记录`来说，比较记录的大小就相当于比的是主键的大小。后边我们还会介绍只存储一条记录的部分列的情况，敬请期待～</span>
        <br><br>`record_type`:
        <br>这个属性表示当前记录的类型，一共有4种类型的记录， 0 表示普通记录， 1 表示B+树非叶节点记录， 2 表示最小记录， 3 表示最大记录。从图中我们也可以看出来，我们自己插入的记录就是普通记录，它们的record_type 值都是 0 ，而最小记录和最大记录的 record_type 值分别为 2 和 3 。<span style="color: blue">至于 record_type 为 1 的情况，我们之后在说索引的时候会重点强调的。</span>
        <br><br>`next_record`:
        <br>这玩意儿非常重要，<span style="color: blue">它表示从当前记录的真实数据到下一条记录的真实数据的地址偏移量</span>。比方说第一条记录的 next_record 值为 32 ，意味着从第一条记录的真实数据的地址处向后找 32 个字节便是下一条记录的真实数据。如果你熟悉数据结构的话，就立即明白了，这其实是个**链表**，可以通过一条记录找到它的下一条记录。
        <br>但是需要注意注意再注意的一点是， 下一条记录<span style="color: red">指得并不是按照我们插入顺序的下一条记录，而是按照主键值由小到大的顺序的下一条记录。而且规定 Infimum记录（也就是最小记录） 的下一条记录就是本页中主键值最小的用户记录，而本页中主键值最大的用户记录的下一条记录就是Supremum记录（也就是最大记录）</span>，为了更形象的表示一下这个next_record起到的作用，我们用箭头来替代一下[next_record](#展示next_record) 中的地址偏移量：

        <!-- panels:start -->
        <!-- div:title-panel -->
        ##### 伪记录(Infimum+Supremum)
        <!-- div:left-panel-40 -->
        ?> 但是不管我们向 页 中插入了多少自己的记录，设计 InnoDB 的大叔们都规定他们定义的两条伪记录分别为最小记录与最大记录。这两条记录的构造十分简单，
        <br>`都是由5字节大小的 记录头信息 和8字节大小的一个固定的部分组成的.`
        <br><br>另外由于这两条记录不是我们自己定义的记录，所以它们并不存放在页的 User Records 部分，他们被单独放在一个称为 `Infimum + Supremum` 的部分，如图所示：
        <br><br>`从图中我们可以看出来，最小记录和最大记录的 heap_no 值分别是 0 和 1 ，也就是说它们的位置最靠前。`
        <!-- div:right-panel-60 -->
        ![](/.images/doc/framework/mysql/book/05_innodb_page_struct/ips-08.png ':size=100%')
        <!-- panels:end -->

        <!-- panels:start -->
        <!-- div:title-panel -->
        ##### 展示next_record
        ?> 从图一中可以看出来，我们的记录按照主键从小到大的顺序形成了一个单链表。 最大记录 的 next_record 的值为 0 ，这也就是说最大记录是没有 下一条记录 了，它是这个单链表中的最后一个节点。
        <br><br>如果从中删除掉一条记录，这个链表也是会跟着变化的，比如我们把第2条记录删掉：`DELETE FROM page_demo WHERE c1 = 2;`如图二
        <br><br>从图中可以看出来，删除第2条记录前后主要发生了这些变化：
        <br>1). 第2条记录并没有从存储空间中移除，而是把该条记录的 delete_mask 值设置为 1 。
        <br>2). 第2条记录的 next_record 值变为了0，意味着该记录没有下一条记录了。
        <br>3). 第1条记录的 next_record 指向了第3条记录。
        <br>4). 还有一点你可能忽略了，就是 最大记录 的 n_owned 值从 5 变成了 4 ，关于这一点的变化我们稍后会详细说明的。
        <br><span style="color: blue">所以，不论我们怎么对页中的记录做增删改操作，InnoDB始终会维护一条记录的单链表，链表中的各个节点是按照主键值由小到大的顺序连接起来的。</span>
        <br><br>再来看一个有意思的事情，因为主键值为 2 的记录被我们删掉了，但是存储空间却没有回收，如果我们再次把这条记录插入到表中，会发生什么事呢？
        <br>`INSERT INTO page_demo VALUES(2, 200, 'bbbb');`;
        <br>从图三中可以看到， InnoDB 并没有因为新记录的插入而为它申请新的存储空间，而是直接复用了原来被删除记录的存储空间。

        > [!ATTENTION]1). 你会不会觉得next_record这个指针有点儿怪，为啥要指向记录头信息和真实数据之间的位置呢？为啥不干脆指向整条记录的开头位置，也就是记录的额外信息开头的位置呢？因为这个位置刚刚好，向左读取就是记录头信息，向右读取就是真实数据。我们前边还说过变长字段长度列表、NULL值列表中的信息都是逆序存放，这样可以使记录中位置靠前的字段和它们对应的字段长度信息在内存中的距离更近，可能会提高高速缓存的命中率。当然如果你看不懂这句话的话就不要勉强了，果断跳过～
        <br><br>2).当数据页中存在多条被删除掉的记录时，这些记录的next_record属性将会把这些被删除掉的记录组成一个垃圾链表，以备之后重用这部分存储空间。

        ![](/.images/doc/framework/mysql/book/05_innodb_page_struct/ips-09.png ':size=33%')
        ![](/.images/doc/framework/mysql/book/05_innodb_page_struct/ips-10.png ':size=33%')
        ![](/.images/doc/framework/mysql/book/05_innodb_page_struct/ips-11.png ':size=33%')
        <!-- panels:end -->

* ## Page Directory(页目录)

* ## Page Header(页面头部)

* ## File Header(文件头部)

* ## File Trailer