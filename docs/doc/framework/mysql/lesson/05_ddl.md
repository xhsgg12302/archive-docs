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

            1. ##### 添加列级约束
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
            2. ##### 添加表级约束
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

         
            <hr/>

            ###### 主键和唯一对比
            |     | 保证唯一性 | 是否允许为空 | 一个表中可以有多个 | 是否允许组合（联合）|
            | :--: |:--: |:--: |:--: |:--: |
            | 主键 | ✅ | ❌ | 至多有1个主键 | ✅ <br>【primary key(id,name)】|
            | 唯一 | ✅ | ✅ <br>(可以有多个NULL) | 可以有多个唯一字段 | ✅ <br>【unique(seat, seat2】|

        - #### 修改表时添加约束