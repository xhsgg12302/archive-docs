* ## Intro(PROCEDURE)

    > [!] 类似于java中的方法，MySQL 5.0 版本开始支持存储过程。
    <br>含义：一组预先编译好的sql语句的集合，理解成批处理语句。
    <br><br>好处：
    <br>`1).` 提高代码的重用性
    <br>`2).` 简化操作
    <br>`3).` 减少编译次数并且减少了和数据库服务器的连接次数，提高了效率。

    + ### 创建语法

        ```sql
        create procedure 存储过程名称(参数列表)
        begin
            存储过程题（一组合法的sql语句）
        end
        ```
        > [?] 注意：
        <br>`1).` 参数列表包含三部分（参数模式，参数名，参数类型），例如：`IN stuname varchar(20)`
        <br>参数模式：
        <br>in: 该参数可以作为输入，也就是该参数需要调用方传入值
        <br>out: 该参数可以作为输出，也就是该参数可以作为返回值
        <br>inout: 该参数既可以作为输入又可以作为输出，也就是该参数既需要传入值，又可以返回值
        <br><br>`2).` 如果存储过程体仅仅只有一句话，`begin end`可以省略,存储过程体中的每条sql语句的结尾要求必须加分号。存储过程的结尾可以使用 `delimiter`重新设置。语法：`delimiter 结束标记`
        <br>`delimiter $`

    + ### 调用语法

        `call 存储过程(实参列表);`

        - #### 1.空参列表

            > [?] case1：插入到admin表中五条记录
            <br>使用下面代码创建存储过程，
            <br>然后使用语句`call myp1();`进行调用
            ```sql
            delimiter $
            create procedure myp1()
            begin
                insert into admin(username, `password`) values('john1', '0000'), ('lily', '0000'), ('rose', '0000'), ('jack', '0000'), ('tom', '0000');
            end $
            ```

        - #### 2.(IN)模式列表

            > [?] case1：创建存储过程实现 根据女神名，查询对应的男神信息
            <br>使用下面代码创建存储过程，
            <br>然后使用语句`call myp2('小昭');`进行调用，控制台会展示返回值。
            ```sql
            create procedure myp2(in beautyName varchar(20))
            begin
                select bo.*
                from boys bo
                right join beauty b on bo.id = b .boyfriend_id
                where b.name = beautyName;
            end $
            ```

            > [?] case2：创建存储过程实现，用户是否登录成功
            <br>使用下面代码创建存储过程，
            <br>然后使用语句`call myp3('张飞','8888');`进行调用
            <!-- panels:start -->
            <!-- div:left-panel-50 -->
            ```sql
            create procedure myp3(in username varchar(20), in password varchar(20))
            begin
                declare result varchar(20) default '';
                select count(1) into result
                from admin
                where admin.username = username and admin.password = password;
                select result;
            end $
            ```
            <!-- div:right-panel-50 -->
            ```sql
            create procedure myp4(in username varchar(20), in password varchar(20))
            begin
                declare result int default 0;
                select count(1) into result
                from admin
                where admin.username = username and admin.password = password;
                select if(result > 0, '成功', '失败');
            end $
            ```
            <!-- panels:end -->

        - #### 3.(OUT)模式列表

            > [?] case1：根据女神名，返回对应的男神名
            <br>使用下面代码创建存储过程，
            <br>然后使用语句`set @bName; call myp5('小昭', @bName);`进行调用, set 定义可有可无。
            <br>可以通过`select @bName;`进行查看返回值。 
            ```sql
            create procedure myp5(in beautyName varchar(20), out boyName varchar(20))
            begin
                select bo.boyName into boyName
                from boys bo
                inner join beauty b on bo.id = b .boyfriend_id
                where b.name = beautyName;
            end $
            ```

            > [?] case2：根据女神名，返回对应的男神名和男神魅力值
            <br>使用下面代码创建存储过程，
            <br>然后使用语句`call myp6('小昭', @bName, @userCP);`进行调用。
            <br>可以通过`select @bName,@userCP;`进行查看返回值。 
            ```sql
            create procedure myp6(in beautyName varchar(20), out boyName varchar(20), out userCP int)
            begin
                select bo.boyName, bo.userCP into boyName, userCP
                from boys bo
                inner join beauty b on bo.id = b .boyfriend_id
                where b.name = beautyName;
            end $
            ```

        - #### 4.(INOUT)模式列表

            > [?] case1：传入a和b两个值，最终a和b都翻倍返回
            <br>使用下面代码创建存储过程，
            <br>然后使用语句`set @m = 10; set @n = 20;call myp7(@m, @n);`进行调用
            <br>可以通过`select @m,@n;`进行查看返回值。 
            ```sql
            create procedure myp7(inout a int, inout b int)
            begin
                set a = a * 2;
                set b = b * 2;
            end $
            ```

    + ### 删除语法

        > [?] 语法：`drop procedure 存储过程名;` 一次只能删除一个。
        <br>`drop procedure myp3;`
    + ### 查看语法

        > [?] 语法
        <br>~`desc myp2;`~
        <br>`show create procedure myp2;`

* ## Intro(FUNCTION)

    > [!] 类似于java中的方法，
    <br>含义：一组预先编译好的sql语句的集合，理解成批处理语句。
    <br><br>好处：
    <br>`1).` 提高代码的重用性
    <br>`2).` 简化操作
    <br>`3).` 减少编译次数并且减少了和数据库服务器的连接次数，提高了效率。 <span style='color:blue'>以上与存储过程一致</span>
    <br><br>和存储过程的区别
    <br> 存储过程：可以有0个返回，也可以有多个返回，适合做批量插入，批量更新
    <br> 函数：有且仅有1一个返回，适合做处理数据后返回一个结果

    + ### 创建语法

        ```sql
        create function 函数名(参数列表) returns 返回类型
        begin
            函数体
        end
        ```
        > [?] 注意：
        <br>参数列表包含两部分：函数名，参数类型
        <br><br>函数体：肯定会有return语句，如果没有会报错
        <br>如果return语句没有放在函数体的最后也不保存，但不建议
        <br><br>return值
        <br>函数体中仅有一句话，则可以省略 begin end
        <br>使用delimiter语句设置结束标记

    + ### 调用语法

        `select 函数名(参数列表);`

        - #### 1.空参有返回

            > [?] case1：返回公司的员工个数
            <br>使用下面代码创建函数，
            <br>然后使用语句`select myf1();`进行调用
            ```sql
            create function myf1() returns int
            begin
                declare c int default 0;
                select count(1) into c
                from employees;
                return c;
            end $
            ```

        - #### 2.有参有返回

            > [?] case1：根据员工名，返回他的工资
            <br>使用下面代码创建函数，
            <br>然后使用语句`select myf2('Kochhar');`进行调用
            ```sql
            create function myf2(empName varchar(20)) returns double
            begin
                set @sal = 0;
                select salary into @sal
                from employees
                where last_name = empName;
                return @sal;
            end $
            ```

            > [?] case2：根据部门名，返回该部门的平均工资
            <br>使用下面代码创建函数，
            <br>然后使用语句`select myf3('IT');`进行调用
            ```sql
            create function myf3(deptName varchar(20)) returns double
            begin
                declare sal double;
                select avg(salary) into sal
                from employees e
                join departments d on e.department_id = d.department_id
                where d.department_name = deptName;
                return sal;
            end $
            ```
    
    + ### 查看语法

        > [?] 语法
        <br>~`desc myf3;`~
        <br>`show create function myf3;`

    + ### 删除语法

        > [?] 语法：`drop function 函数名;` 一次只能删除一个。
        <br>`drop function myf3;`
