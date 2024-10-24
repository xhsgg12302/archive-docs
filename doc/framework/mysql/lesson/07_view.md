* ## Intro(VIEW)

    > [?] 含义：虚拟表，和普通表一样使用
    <br><span style='color:blue'>mysql5.1版本出现的新特性是通过表动态生成的数据，行和列的数据来自定义视图的查询中使用的表，并且实在使用视图时动态生成的，只保存了sql逻辑，不保存查询结果</span>
    <br><br>好处：
    <br>`1).`: 重用sql语句
    <br>`2).`: 简化复杂的sql操作，不必知道它的查询细节
    <br>`3).`: 保护数据，提高安全性
    <br><br>应用场景：
    <br>`1).`: 多个地方用到了同样的查询结果
    <br>`2).`: 该查询结果使用的sql语句较复杂

    - ### 示例

        ```sql
        create view my_v1
        as 
        select stuname, majorname from stu_info s inner join major m on s.majorId = m.id;
        ```

    > [?] case1: 查询姓张的学生名和专业名
    <br>`select stuname,majorname from stu_info s inner join major m on s.majorId = m.id where s.stuname like '张%';`
    <br>`create view v1 as select stuname, majorname from stu_info s inner join major m on s.majorId = m.id;`
    <br>`select * from v1 where stuname like '张%';`

    - ### 创建视图
    
        > [?] case1: 查询邮箱中包含a字符的员工名，部门名和工种信息
        <br>`create view myv1 as select last_name,department_name, job_title from employees e join departments d on e.department_id = d.department_id join jobs j on j.job_id = e.job_id;`
        <br>`select * from myv1 where last_name like '%a%';`
        <br><br>case2: 查询各部门的平均工资级别
        <br>`create view myv2 as select avg(salary) ag, department_id from employees group by department_id;`
        <br>`select myv2.ag, g.grade_level from myv2 join job_grades g on myv2.ag between g.lowest_sal and g.highest_sal;`
        <br><br>case3: 查询平均工资最低的部门信息
        <br>`select * from myv2 order by ag limit 1;`
        <br><br>case4: 查询平均工资最低的部门名和工资（<span style='color:blue'>通过视图继续创建视图</span>）
        <br>`create view myv3 as select * from myv2 order by ag limit 1;`
        <br>`select d.*, m.ag from myv3 m join departments d on m.department_id = d.department_id; `

    - ### 修改视图

        > [?] 方式一：`create or replace view 视图名 as 查询语句;`
        <br>`create or replace view myv3 as select avg(salary), job_id from employees group by job_id;`
        <br><br>方式二：`alter view 视图名 as 查询语句;`
        <br>`alter view myv3 as select * from employees;`

    - ### 删除视图

        > [?] 语法：`drop view 视图名,视图名,...;`
        <br>`drop view myv1,myv2,myv3;`

    - ### 查看视图

        > [?] `desc myv3;` 或者 `show create view myv3;`

    - ### 更新视图

        > [?] 例如：
        <br>~`create or replace view myv1 as select last_name, email, salary * 12 * (1+ifnull(commission_pct,0)) from employees;`~，这个有限制条件。
        <br>`create or replace view myv1 as select last_name, email from employees;`
        <br>case1：插入
        <br>`insert into myv1 values('张飞', 'zf@qq.com');`
        <br>case2：修改
        <br>`update myv1 set last_name = '张无忌' where last_name = '张飞';`
        <br>case3：删除
        <br>`delete from myv1 where last_name = '张无忌';`

        + #### 限制条件

            * ##### 包含以下关键字的sql语句：
                > [!NOTE] 分组函数，distinct，group by，having， union或者union all
                <br>`create or replace view myv1 as select max(salary) m, department_id from employees group by department_id;`
                <br>`select * from myv1;`
                <br>~`update myv1 set m = 9000 where department_id = 10;`~
            * ##### 常量视图
                > [!NOTE] `create or replace view myv2 as select 'john' name;`
                <br>`select * from myv2;`
                <br>~`update myv2 set name = 'lucy';`~
            * ##### select中包含子查询
                > [!NOTE] `create or replace view myv3 as select department_id, (select max(salary) from employees) 最高工资 from employees;`
                <br>`select * from myv3;`
                <br>~`update myv3 set 最高工资=100000;`~
            * ##### join
                > [!NOTE] `create or replace view myv4 as select last_name, department_name from employees e join departments d on e.department_id = d.department_id;`
                <br>`select * from myv4;`
                <br>`update myv4 set last_name = '张飞' where last_name = 'Whalen';`
                <br>~`insert into myv4 values('陈真', 'xxxx');`~
            * ##### from一个不能更新的视图
                > [!NOTE] `create or replace view myv5 as select * from myv3;`
                <br>`select * from myv5;`
                <br>~`update myv5 set 最高工资=10000 where department_id = 60;`~
            * ##### where子句中的子查询引用了from子句中的表
                > [!NOTE]`create or replace view myv6 as select last_name, email, salary from employees where employee_id in (select manager_id from employees where manager_id is not null);`
                <br>`select * from myv6;`
                <br>~`update myv6 set salary = 10000 where last_name = 'K_ing';`~

    - ### 视图和表的对比

        |       | 创建语法的关键字 | 是否实际占用物理空间 | 使用|
        | :--: | :--: | :--: | :--: |
        | 视图  | create view | 只是保存了sql逻辑 | 增删改查，一般不能增删改 |
        | 表    | create table | 保存了数据 | 增删改查 | 