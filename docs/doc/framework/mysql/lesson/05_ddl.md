* ## DDL语言的学习

    + ### 1.库和表的管理
    + ### 2.常见数据类型介绍
    + ### 3.常见约束

        > [?] 含义：一种限制，用于限制表中的数据，为了保证表中数据的准确和可靠性
        <br><br> 分类：六大约束：
        <br>`①:` NOT NULL： 非空，用于保证该字段的值不能为空。
        <br>`②:` DEFAULT：默认，用于保证该字段有默认值。
        <br>`③:` PRIMARY KEY： 主键，用于保证该字段的值具有唯一性，并且非空。
        <br>`④:` UNIQUE：唯一，用于保证该字段的值具有唯一性，可以为空。
        <br>`⑤:` CHECK：检查约束【mysql中不支持】
        <br>`⑥:` FOREIGN KEY：外键，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值。在从表中添加外键约束，用于引用主表中的某列的值。
        <br><br>添加约束的时机：
        <br>`a).` 创建表时：
        <br>`b).` 修改表时：
        <br><br>约束的添加分类：
        <br>`1).` 列级约束：六大约束语法上都支持，但是外键约束没有效果
        <br>`2).` 表级约束：除了非空，默认，其他都支持
        <br><span style='color: blue'>通用写法： 其他列级，外键表级</span>
        ###### 测试约束用到的数据库
        ```sql
        drop database students;
        create database students character set utf8;
        use students;

        -- 创建外键引用的主表
        drop table if exists major;
        create table major(
            id int primary key,
            majorName varchar(20)
        );
        insert into major values (1, 'java'),(2, 'h5');
        select * from major;
        ```

        - #### 创建表时添加约束

            <!-- panels:start -->
            <!-- div:left-panel-50 -->
            ##### 添加列级约束
            ```sql
            # 1.1 添加列级约束
            drop table if exists stu_info;
            create table stu_info(
                id int primary key, # 主键
                stuName varchar(20) not null, # 非空
                gender char(1) check ( gender = '男' or gender = '女'), # 检查
                seat int unique, # 唯一
                age int default 18, # 默认
                majorId int references major(id) # 外键(列级没效果)
            );
            desc stu_info;
            show index from stu_info;
            insert into stu_info values (1,' john', '男', null, 19, 1);
            insert into stu_info values (2,' lily', '男', null, 19, 2);
            select * from stu_info;
            ```

            <!-- div:right-panel-50 -->
            ##### 添加表级约束
            ```sql
            # 1.2添加表级约束
            drop table if exists stu_info;
            create table stu_info(
                id int,
                stuName varchar(20) not null,
                gender char(1),
                seat int,
                age int default 18,
                majorId int,

                constraint pk primary key(id), # 主键
                constraint uq unique (seat), # 唯一
                constraint ck check ( gender = '男' or gender = '女' ), # 检查
                constraint fk_stu_info_major foreign key (majorId) references major(id) # 外键
            );
            ```
            <!-- panels:end -->
            <hr/>

            ###### 主键和唯一对比
            |     | 保证唯一性 | 是否允许为空 | 一个表中可以有多个 | 是否允许组合（联合）|
            | :--: |:--: |:--: |:--: |:--: |
            | 主键 | ✅ | ❌ | 至多有1个主键 | ✅ <br>【primary key(id,name)】|
            | 唯一 | ✅ | ✅ <br>(可以有多个NULL) | 可以有多个唯一字段 | ✅ <br>【unique(seat, seat2】|

            ###### 外键的特点
            > [!NOTE] `1).` 要求在从表上设置外键关系
            <br>`2).` 要求从表的外键列的类型和主表而关联列的类型 一致或兼容。名称无要求
            <br>`3).` 主表的关联列必须是一个key（一般是主键或者唯一）
            <br>`4).` 插入时：先主后从。删除时，先从后主（不然又外键约束引用，删除会报错。比如此处先删major,后删stu_info会报错）

        - #### 修改表时添加约束

            > [?]特点:
            <br>`1).` 添加列级约束：`alter table 表名 modify column 字段名 字段类型 新约束;`
            <br>`2).` 添加表级约束：`alter table 表名 add [constraint 约束名] 约束类型(字段名) [外键的引用]`
            <br><br>一、添加非空约束： `alter table stu_info modify column stuname varchar(20) not null;`
            <br>二、添加默认约束：`alter table stu_info modify column age int default 18;`
            <br>三、添加主键：
            <br>&nbsp;&nbsp;&nbsp; 列级：`alter table stu_info modify column id int primary key;`
            <br>&nbsp;&nbsp;&nbsp; 表级：`alter table stu_info add primary key(id);`
            <br>四、添加唯一：
            <br>&nbsp;&nbsp;&nbsp; 列级：`alter table stu_info modify column seat int unique;`
            <br>&nbsp;&nbsp;&nbsp; 表级：`alter table stu_info add unique(seat);`
            <br>五、添加外键：`alter table stu_info add constraint fk_stuinfo_major foreign key(majorid) references major(id);`

        - #### 修改表时删除约束

            > [?]
            一、删除非空约束： `alter table stu_info modify column stuname varchar(20) null;`
            <br>二、删除默认约束：`alter table stu_info modify column age int;`
            <br>三、删除主键：`alter table stu_info drop primary key;`
            <br>四、删除唯一：`alter table stu_info drop index seat;`，可以通过`show index from stu_info`来查看名字。
            <br>五、删除外键：`alter table stu_info drop foreign key fk_stuinfo_major;`

    + ### 4.标识列

        > [?] 又称为自增长列
        <br>含义：可以不用手动的插入值，系统提供默认的序列值
        <br><br>特点：
        <br>`1).` 标识列必须和key搭配（主键，唯一）
        <br>`2).` 一个表至多一个
        <br>`3).` 标识列的类型只能是数值型
        <br>`4).` 标识列可以通过`set auto_increment_increment = 3;`设置步长，通过手动插入值设置offset起始值。
        <br><br>查看相关变量使用`show variables like '%auto_increment%';`
        <br><br>一、创建表时设置标识列`create table t1(id int primary key auto_increment);`
        <br>二、修改表时设置标识列`alter table t1 modify column id int primary key auto_increment;`
        <br>三、修改表时删除标识列`alter table t1 modify column id int;`