
* ## 开场序言

    > [!CAUTION] 对于我们这些 MySQL 的使用者来说， MySQL 其实就是一个软件，平时用的最多的就是查询功能。DBA 时不时丢过来一些慢查询语句让优化，我们如果连查询是怎么执行的都不清楚还优化个毛线，所以是时候掌握真正的技术了。我们在第一章的时候就曾说过， MySQL Server 有一个称为 **查询优化器** 的模块，一条查询语句进行语法解析之后就会被交给查询优化器来进行优化，优化的结果就是生成一个所谓的 **执行计划** ，这个执行计划表明了应该使用哪些索引进行查询，表之间的连接顺序是啥样的，最后会按照执行计划中的步骤调用存储引擎提供的方法来真正的执行查询，并将查询结果返回给用户。不过查询优化这个主题有点儿大，在学会跑之前还得先学会走，所以本章先来瞅瞅 MySQL 怎么执行单表查询（就是 FROM 子句后边只有一个表，最简单的那种查询～）。不过需要强调的一点是，在学习本章前务必看过前边关于记录结构、数据页结构以及索引的部分，如果你不能保证这些东西已经完全掌握，那么本章不适合你。
    <br><br>为了故事的顺利发展，我们先得建个如下的表：
    <br>我们为这个 *single_table* 表建立了1个聚簇索引和4个二级索引，分别是：
    <br><span style='padding-left:2em'>`1.` 为 id 列建立的聚簇索引。
    <br><span style='padding-left:2em'>`1.` 为 key1 列建立的 idx_key1 二级索引。
    <br><span style='padding-left:2em'>`1.` 为 key2 列建立的 idx_key2 二级索引，而且该索引是唯一二级索引。
    <br><span style='padding-left:2em'>`1.` 为 key3 列建立的 idx_key3 二级索引。
    <br><span style='padding-left:2em'>`1.` 为 key_part1 、 key_part2 、 key_part3 列建立的 idx_key_part 二级索引，这也是一个联合索引。
    <br><br>然后我们需要为这个表插入10000行记录，除 id 列外其余的列都插入随机值就好了，具体的插入语句我就不写了，自己写个程序插入吧（id列是自增主键列，不需要我们手动插入）。

    ```sql
    CREATE TABLE single_table (
        id INT NOT NULL AUTO_INCREMENT,
        key1 VARCHAR(100),
        key2 INT,
        key3 VARCHAR(100),
        key_part1 VARCHAR(100),
        key_part2 VARCHAR(100),
        key_part3 VARCHAR(100),
        common_field VARCHAR(100),
        PRIMARY KEY (id),
        KEY idx_key1 (key1),
        UNIQUE KEY idx_key2 (key2),
        KEY idx_key3 (key3),
        KEY idx_key_part(key_part1, key_part2, key_part3)
    ) Engine=InnoDB CHARSET=utf8;
    ```

* ## 访问方法（access method）的概念

    > [?] 想必各位都用过高德地图来查找到某个地方的路线吧（此处没有为高德地图打广告的意思，他们没给我钱，大家用百度地图也可以啊），如果我们搜西安钟楼到大雁塔之间的路线的话，地图软件会给出n种路线供我们选择，如果我们实在闲的没事儿干并且足够有钱的话，还可以用南辕北辙的方式绕地球一圈到达目的地。也就是说，不论采用哪一种方式，我们最终的目标就是到达大雁塔这个地方。回到 MySQL 中来，我们平时所写的那些查询语句本质上只是一种声明式的语法，只是告诉 MySQL 我们要获取的数据符合哪些规则，至于 MySQL 背地里是怎么把查询结果搞出来的那是 MySQL 自己的事儿。对于单个表的查询来说，设计MySQL的大叔把查询的执行方式大致分为下边两种：
    <br><span style='padding-left:1.2em'>`使用全表扫描进行查询`: 这种执行方式很好理解，就是把表的每一行记录都扫一遍嘛，把符合搜索条件的记录加入到结果集就完了。不管是啥查询都可以使用这种方式执行，当然，这种也是最笨的执行方式。
    <br><span style='padding-left:1.2em'>`使用索引进行查询`: 因为直接使用全表扫描的方式执行查询要遍历好多记录，所以代价可能太大了。如果查询语句中的搜索条件可以使用到某个索引，那直接使用索引来执行查询可能会加快查询执行的时间。使用索引来执行查询的方式五花八门，又可以细分为许多种类：
    <br><span style='padding-left:3em'>`1.` 针对主键或唯一二级索引的等值查询
    <br><span style='padding-left:3em'>`2.` 针对普通二级索引的等值查询
    <br><span style='padding-left:3em'>`3.` 针对索引列的范围查询
    <br><span style='padding-left:3em'>`4.` 直接扫描整个索引
    <br><br>设计 MySQL 的大叔把 MySQL 执行查询语句的方式称之为 **访问方法** 或者 **访问类型** 。同一个查询语句可能可以使用多种不同的访问方法来执行，虽然最后的查询结果都是一样的，但是执行的时间可能差老鼻子远了，就像是从钟楼到大雁塔，你可以坐火箭去，也可以坐飞机去，当然也可以坐乌龟去。下边细细道来各种 访问方法 的具体内容。

* ## const

    > [?] 有的时候我们可以通过主键列来定位一条记录，比方说这个查询：
    <br>`SELECT * FROM single_table WHERE id = 1438;`
    <br>MySQL 会直接利用主键值在聚簇索引中定位对应的用户记录，就像这样：
    <br>![](/.images/doc/framework/mysql/book/10_single-table-access/sta-01.png ':size=70%')
    <br><br>原谅我把聚簇索引对应的复杂的 B+ 树结构搞了一个极度精简版，为了突出重点，我们忽略掉了 页 的结构，直接把所有的叶子节点的记录都放在一起展示，而且记录中只展示我们关心的索引列，对于 single_table 表的聚簇索引来说，展示的就是 id 列。我们想突出的重点就是： B+ 树叶子节点中的记录是按照索引列排序的，对于的聚簇索引来说，它对应的 B+ 树叶子节点中的记录就是按照 id 列排序的。 B+ 树本来就是一个矮矮的大胖子，所以这样根据主键值定位一条记录的速度贼快。类似的，我们根据唯一二级索引列来定位一条记录的速度也是贼快的，比如下边这个查询：
    <br>`SELECT * FROM single_table WHERE key2 = 3841;`
    <br>这个查询的执行过程的示意图就是这样：
    <br>![](/.images/doc/framework/mysql/book/10_single-table-access/sta-02.png ':size=70%')
    <br><br>可以看到这个查询的执行分两步，第一步先从 idx_key2 对应的 B+ 树索引中根据 key2 列与常数的等值比较条件定位到一条二级索引记录，然后再根据该记录的 id 值到聚簇索引中获取完整的用户记录。
    <br><br>设计 MySQL 的大叔认为通过主键或者唯一二级索引列与常数的等值比较来定位一条记录是像坐火箭一样快的，所以他们把这种通过主键或者唯一二级索引列来定位一条记录的访问方法定义为： const ，意思是常数级别的，代价是可以忽略不计的。不过这种 const 访问方法只能在主键列或者唯一二级索引列和一个常数进行等值比较时才有效，如果主键或者唯一二级索引是由多个列构成的话，索引中的每一个列都需要与常数进行等值比较，这个 const 访问方法才有效（这是因为只有该索引中全部列都采用等值比较才可以定位唯一的一条记录）。
    <br><br>对于唯一二级索引来说，查询该列为 NULL 值的情况比较特殊，比如这样：`SELECT * FROM single_table WHERE key2 IS NULL;`
    <br>因为唯一二级索引列并不限制 NULL 值的数量，所以上述语句可能访问到多条记录，也就是说 上边这个语句不可以使用 const 访问方法来执行（至于是什么访问方法我们下边马上说）。

* ## ref

    > [?] 有时候我们对某个普通的二级索引列与常数进行等值比较，比如这样：`SELECT * FROM single_table WHERE key1 = 'abc';`
    <br>对于这个查询，我们当然可以选择全表扫描来逐一对比搜索条件是否满足要求，我们也可以先使用二级索引找到对应记录的 id 值，然后再回表到聚簇索引中查找完整的用户记录。由于普通二级索引并不限制索引列值的唯一性，所以可能找到多条对应的记录，也就是说使用二级索引来执行查询的代价取决于等值匹配到的二级索引记录条数。如果匹配的记录较少，则回表的代价还是比较低的，所以 MySQL 可能选择使用索引而不是全表扫描的方式来执行查询。设计 MySQL 的大叔就把这种搜索条件为二级索引列与常数等值比较，采用二级索引来执行查询的访问方法称为： ref 。我们看一下采用 ref 访问方法执行查询的图示：
    <br>![](/.images/doc/framework/mysql/book/10_single-table-access/sta-03.png ':size=70%')
    <br><br>从图示中可以看出，对于普通的二级索引来说，通过索引列进行等值比较后可能匹配到多条连续的记录，而不是像主键或者唯一二级索引那样最多只能匹配1条记录，所以这种 ref 访问方法比 const 差了那么一丢丢，但是在二级索引等值比较时匹配的记录数较少时的效率还是很高的（如果匹配的二级索引记录太多那么回表的成本就太大了），跟坐高铁差不多。不过需要注意下边两种情况：
    <br><br><span style='padding-left:1.2em'>**二级索引列值为 NULL 的情况**: 不论是普通的二级索引，还是唯一二级索引，它们的索引列对包含 NULL 值的数量并不限制，所以我们采用 key IS NULL 这种形式的搜索条件最多只能使用 ref 的访问方法，而不是 const 的访问方法。
    <br><span style='padding-left:1.2em'>**对于某个包含多个索引列的二级索引来说，只要是最左边的连续索引列是与常数的等值比较就可能采用 ref的访问方法，比方说下边这几个查询：**
    <br><span style='padding-left:3em'>`SELECT * FROM single_table WHERE key_part1 = 'god like';`
    <br><span style='padding-left:3em'>`SELECT * FROM single_table WHERE key_part1 = 'god like' AND key_part2 = 'legendary';`
    <br><span style='padding-left:3em'>`SELECT * FROM single_table WHERE key_part1 = 'god like' AND key_part2 = 'legendary' AND key_part3 = 'penta kill';`
    <br><br>但是如果最左边的连续索引列并不全部是等值比较的话，它的访问方法就不能称为 ref 了，比方说这样：
    <br>`SELECT * FROM single_table WHERE key_part1 = 'god like' AND key_part2 > 'legendary';`

* ## ref_or_null
    
    > [?] 有时候我们不仅想找出某个二级索引列的值等于某个常数的记录，还想把该列的值为 NULL 的记录也找出来，就像下边这个查询：
    <br>`SELECT * FROM single_demo WHERE key1 = 'abc' OR key1 IS NULL;`
    <br>当使用二级索引而不是全表扫描的方式执行该查询时，这种类型的查询使用的访问方法就称为 ref_or_null ，这个 ref_or_null 访问方法的执行过程如下：
    <br>![](/.images/doc/framework/mysql/book/10_single-table-access/sta-04.png ':size=70%')
    <br><br>可以看到，上边的查询相当于先分别从 idx_key1 索引对应的 B+ 树中找出 key1 IS NULL 和 key1 = 'abc' 的两个连续的记录范围，然后根据这些二级索引记录中的 id 值再回表查找完整的用户记录。
    
* ## range

    > [?] 我们之前介绍的几种访问方法都是在对索引列与某一个常数进行等值比较的时候才可能使用到（ ref_or_null 比较奇特，还计算了值为 NULL 的情况），但是有时候我们面对的搜索条件更复杂，比如下边这个查询：
    <br>`SELECT * FROM single_table WHERE key2 IN (1438, 6328) OR (key2 >= 38 AND key2 <= 79);`
    <br>我们当然还可以使用全表扫描的方式来执行这个查询，不过也可以使用 二级索引 + 回表 的方式执行，如果采用 二级索引 + 回表 的方式来执行的话，那么此时的搜索条件就不只是要求索引列与常数的等值匹配了，而是索引列需要匹配某个或某些范围的值，在本查询中 key2 列的值只要匹配下列3个范围中的任何一个就算是匹配成功了：
    <br><br><span style='padding-left:1.2em'>key2 的值是 1438
    <br><span style='padding-left:1.2em'>key2 的值是 6328
    <br><span style='padding-left:1.2em'>key2 的值在 38 和 79 之间。
    <br><br>设计 MySQL 的大叔把这种利用索引进行范围匹配的访问方法称之为： **range**
    
    > [!ATTENTION|label:小贴士] 此处所说的使用索引进行范围匹配中的 `索引` 可以是聚簇索引，也可以是二级索引。

    > [?] 如果把这几个所谓的 key2 列的值需要满足的 范围 在数轴上体现出来的话，那应该是这个样子：
    <br>![](/.images/doc/framework/mysql/book/10_single-table-access/sta-05.png ':size=70%')
    <br><br>也就是从数学的角度看，每一个所谓的范围都是数轴上的一个 区间 ，3个范围也就对应着3个区间：
    <br><br><span style='padding-left:1.2em'>范围1： key2 = 1438
    <br><span style='padding-left:1.2em'>范围2： key2 = 6328
    <br><span style='padding-left:1.2em'>范围3： key2 ∈ [38, 79] ，注意这里是闭区间。
    <br><br>我们可以把那种索引列等值匹配的情况称之为 单点区间 ，上边所说的 范围1 和 范围2 都可以被称为单点区间，像 范围3 这种的我们可以称为连续范围区间。

* ## index

    > [?] 看下边这个查询：
    <br>`SELECT key_part1, key_part2, key_part3 FROM single_table WHERE key_part2 = 'abc';`
    <br>由于 key_part2 并不是联合索引 idx_key_part 最左索引列，所以我们无法使用 ref 或者 range 访问方法来执行这个语句。但是这个查询符合下边这两个条件：
    <br><br><span style='padding-left:1.2em'>它的查询列表只有3个列： key_part1 , key_part2 , key_part3 ，而索引 idx_key_part 又包含这三个列。
    <br><span style='padding-left:1.2em'>搜索条件中只有 key_part2 列。这个列也包含在索引 idx_key_part 中。
    <br><br>也就是说我们可以直接通过遍历 idx_key_part 索引的叶子节点的记录来比较 key_part2 = 'abc' 这个条件是否成立，把匹配成功的二级索引记录的 key_part1 , key_part2 , key_part3 列的值直接加到结果集中就行了。由
    于二级索引记录比聚簇索记录小的多（聚簇索引记录要存储所有用户定义的列以及所谓的隐藏列，而二级索引记录只需要存放索引列和主键），而且这个过程也不用进行回表操作，所以直接遍历二级索引比直接遍历聚簇索引的成本要小很多，设计 MySQL 的大叔就把这种采用遍历二级索引记录的执行方式称之为： index 。

* ## all

    > [?] 最直接的查询执行方式就是我们已经提了无数遍的全表扫描，对于 InnoDB 表来说也就是直接扫描聚簇索引，设计 MySQL 的大叔把这种使用全表扫描执行查询的方式称之为： all 。

* ## 注意事项
    + ### 重温 二级索引 + 回表
    + ### 明确range访问方法使用的范围区间
        - #### 所有搜索条件都可以使用某个索引的情况
        - #### 有的搜索条件无法使用索引的情况
        - #### 复杂搜索条件下找出范围匹配的区间
    + ### 索引合并
        - #### Intersection合并
        - #### Union合并
        - #### Sort-Union合并
        - #### 索引合并注意事项
        - #### 联合索引替代 Intersection 索引合并