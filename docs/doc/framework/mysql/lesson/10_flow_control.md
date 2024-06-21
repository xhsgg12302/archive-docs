* ## Intro(FLOW CONTROL)

    > [!] 简单介绍：
    <br>顺序结构：程序从上至下依次执行
    <br>分支结构：程序从两条或多条路径中选择一条去执行
    <br>循环结构：程序在满足一定条件的基础上，重复执行一段代码

    + ### 分支结构

        - #### IF函数

            > [?] 功能：实现简单的双分支
            <br><br>语法：`if(表达式1，返回值2，返回值3)`
            <br>如果表达式1成立，则if使用返回值2，否则使用返回值3.
            <br><br>应用：任何地方

        - #### CASE函数

            <!-- panels:start -->
            > [?]特点：
            <br>`1).` 可以作为表达式，嵌套在其他语句中使用，可以放在任何地方，begin end 中或begin end外面。可以作为单独的语句去使用，只能放在 begin end 中。
            <br>`2).` 如果when中的值满足或条件成立，则执行对应的then后面的语句，并且结束case，如果都不满足，则执行else中的语句或值
            <br>`3).` else可以省略，如果else省略了，并且所有when条件都不满足，则返回NULL。
            <!-- div:left-panel-50 -->
            > [!NOTE] 情况一：类似于java中的switch语句，一般用作实现等值判断
            ```sql
            case 变量|表达式|字段
            when 要判断的值 then 返回的值1或语句1;
            when 要判断的值 then 返回的值1或语句1;
            ...
            else 返回的值n或语句n;
            end case;
            ```
            <!-- div:right-panel-50 -->
            > [!NOTE] 情况二：类似于java中的多重if语句，一般用作实现区间判断
            ```sql
            case
            when 要判断的条件1 then 返回的值1或语句1;
            when 要判断的条件2 then 返回的值1或语句1;
            ...
            else 返回的值n或语句n;
            end case;
            ```
            <!-- panels:end -->

            > [?] case1：根据我们传入的成绩，来显示等级，比如传入的成绩：90-100，显示A，80-90，显示B，60-80，显示C，否则，显示D
            <br>调用：`call test_case(95);`
            ```sql
            create procedure test_case(in score int)
            begin
                case 
                when score >= 90 and score <= 100 then select 'A';
                when score >= 80 then select 'B';
                when score >= 60 then select 'C';
                else select 'D';
                end case;
            end $
            ```
        
        - #### IF结构

            > [?] 功能：实现多重分支
            <br><br>语法：`if 条件1 then 语句1; elseif 条件2 then 语句2; ... ;else 语句n; end if;`
            <br>如果表达式1成立，则if使用返回值2，否则使用返回值3.
            <br><br>应用：应用在begin end 中
            <br><br>case1：根据传入的成绩，来显示等级，比如传入的成绩：90-100，返回80-90，返回B，60-80，返回C，否则，返回D
            <br>调用 `select test_if(85);`

            ```sql
            create function test_if(score int) returns char
            begin
                if score >= 90 and score <= 100 then return 'A';
                elseif score >= 80 then return 'B';
                elseif score >= 60 then return 'C';
                else return 'D';
                end if;
            end $  
            ```
    + ### 循环结构

        > [?] 分类：`while, loop, repeat`
        <br><br>循环控制：
        <br>`iterate`: 类似于 continue 继续，结束本次循环，继续下一次
        <br>`leave`: 类似于 break，跳出，结束当前所在的循环

        <!-- tabs:start -->
        #### **while**
        > [!NOTE] omited
        ```sql
        [标签:] 
        while 循环条件 do
            循环体;
        end while;
        [标签];
        ```
        - ##### demo

            > [?] 没有添加循环控制的情况 
            <br>case1：批量插入，根据次数插入到admin表中多条记录
            <br>调用：`call pro_while1(100);`
            ```sql
            create procedure pro_while1(in insertCount int)
            begin
                declare i int default 1;
                while i <= insertCount do
                    insert into admin(username, password) values (concat('Rose' , i), '666');
                    set i = i + 1;
                end while;
            end $
            ```

            > [?] 添加leave 循环控制语句 
            <br>case2：批量插入，根据次数插入到admin表中多条记录，如果次数 >20 则停止
            <br>调用：`call pro_while2(100);`
            ```sql
            create procedure pro_while2(in insertCount int)
            begin
                declare i int default 1;
                a:while i <= insertCount do
                    insert into admin(username, password) values (concat('xiaohua' , i), '666');
                    if i >= 20 then leave a;
                    end if;
                    set i = i + 1;
                end while a;
            end $
            ```

            > [?] 添加iterate 循环控制语句 
            <br>case3：批量插入，根据次数插入到admin表中多条记录，只记录偶数次
            <br>调用：`call pro_while3(100);`
            ```sql
            create procedure pro_while3(in insertCount int)
            begin
                declare i int default 0;
                flag:while i <= insertCount do
                    set i = i + 1;
                    if mod(i, 2) != 0 then iterate flag;
                    end if;
                    insert into admin(username, password) values (concat('xiaohua' , i), '666');
                end while flag;
            end $
            ```

        #### **loop**
        > [!NOTE] 可以用来模拟简单的死循环
        ```sql
        [标签:] 
        loop
            循环体;
        end loop;
        [标签];
        ```

        #### **repeat**
        > [!NOTE] omited
        ```sql
        [标签:] 
        repeat
            循环体;
        until 结束条件
        end repeat;
        [标签];
        ```
        <!-- tabs:end -->
