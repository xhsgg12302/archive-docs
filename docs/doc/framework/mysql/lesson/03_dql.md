* ## DQL语言的学习

    + ### 1.基础查询
    
        > [?]
        语法：`select 查询列表 from 表名;`
        <br><br>特点：
        <br>1，查询列表可以是：<span style='color: blue'>表中的字段，常量值，表达式，函数</span>
        <br>2，查询的结果是一个虚拟的表格
        <br><br>1.查询表中的单个字段
        <br>`select last_name from employees limit 10;`
        <br><br>2.查询表中的多个字段
        <br>`select last_name, salary, email from employees limit 10;`
        <br><br>3.查询表中的所有字段
        <br>`select * from employees limit 10;`
        <br><br>4.查询常量值
        <br>`select 100;`,`select 'john';`
        <br><br>5.查询表达式
        <br>`select 100%98;`
        <br><br>6.查询函数
        <br>`select version();`
        <br><br>7.起别名 
        <br>① 便于理解，② 如果查询字段有重名，使用别名可以区分开来，③ 别名中有特殊字符使用引号扩起来
        <br>`select 100%98 as 结果;`
        <br>`select last_name as 姓,first_name as 名 from employees limit 10;`
        <br><br>8.去重
        <br># case: 查询员工表中涉及到的所有的部门编号
        <br>`select distinct department_id from employees;`
        <br><br>9.+号的作用 ① 仅仅只有一个功能，运算符
        <br>`select 100 + 90;` 连个操作数都为数值型，则做加法运算
        <br>`select '123' + 90;` 213 其中一个为字符型，可以转换，做加法运算
        <br>`select 'john' + 90` 90 转换失败，则将字符型数值转换成 0 再做加法。
        <br>`select null + 10;` NULL 只要有一方为NULL, 则结果肯定为NULL。
        <br>所以下面字符串连接得使用`concat函数`
        <br>case: 查询员工名和姓连接成一个字段，并显示为姓名
        <br>`select concat(last_name,first_name) as 姓名 from employees limit 10;`
        
    + ### 2.条件查询

        > [?] 语法： `select 查询列表 from 表名 where 筛选条件;`
        <br><br>分类：
        <br>1，按条件表达式筛选：简单条件运算符 `>`, `<`, `=`, `!=`, `<>`, `>=`, `<=`, `<=>` (安全等于)可以与NULL进行判断。
        <br>2，按逻辑表达式筛选：逻辑运算符 `&&`, `||`, `!`, `and`, `or`, `not`
        <br>3，模糊查询：`like`, `between and`, `in`, `is null`
        <br><br>一、按条件表达式查询
        <br>case1: 查询工资 > 12000 的员工信息
        <br>`select * from employees where salary > 12000;`
        <br>case2: 查询部门编号 不等于90 号的员工名和部门编号
        <br>`select last_name, department_id from employees where department_id != 90;`
        <br><br>二、按逻辑表达式查询
        <br>case1: 查询工资在 10000-20000之间的员工名，工资及奖金
        <br>`select last_name, salary, commission_pct from employees where salary >= 10000 and salary <= 20000;`
        <br>case2: 查询部门编号不是在90~110之间，或者工资高于15000 的员工信息 
        <br>`select * from employees where department_id < 90 or department_id > 110 or salary > 15000;`
        <br>`select * from employees where not(department_id >= 90 and department_id <= 110) or salary > 15000;`
        <br><br>三、模糊查询
        <br>like-case1: 查询员工名中包含字符a的员工信息
        <br>`select * from employees where last_name like '%a%';`
        <br>like-case2: 查询员工名中第三个字符为n，第五个字符为l的员工名和工资
        <br>`select last_name, salary from employees where last_name like '__n_l%';`
        <br>like-case3: 查询员工名中第二个字符为_的员工名
        <br>`select last_name from employees where last_name like '_\_%';`
        <br>`select last_name from employees where last_name like '_$_%' escape '$';`
        <br>between-and-case1: 查询员工编号在100-120之间的员工信息
        <br>`select * from employees where employee_id between 100 and 120;`
        <br>in-case1: 查询员工的工种编号是 IT_PROG,AD_VP,AD_PRES中的一个员工名和工种编号
        <br>`select last_name, job_id from employees where job_id in ('IT_PROT', 'AD_VP', 'AD_PRES');`
        <br>is-null-case1: 查询(没有|有)奖金的员工名和奖金率
        <br>`select last_name, commission_pct from employees where commission_pct is null;`
        <br>`select last_name, commission_pct from employees where commission_pct is not null;`

    + ### 3.排序查询

        > [?] 语法： `select 查询列表 from 表名 [where 筛选条件] order by 排序列表 [asc|desc];`
        <br><br>特点： 
        <br>1，asc代表的是升序，desc代表的是降序，如果不写，默认是升序。
        <br>2，order by 子句中可以支持单个字段，多个字段，表达式，函数，别名。
        <br>3，order by 子句一般是放在查询语句的最后面，limit子句除外。
        <br><br>case1: 查询员工信息，要求工资(从高到低|从低到高)排序
        <br>`select * from employees order by salary desc;`
        <br>`select * from employees order by salary;`
        <br>case2: 查询部门编号 >=90 的员工信息，按入职时间的先后进行排序【添加筛选条件】
        <br>`select * from employees where department_id >= 90 order by hiredate asc;`
        <br>case3: 按年薪的高低显示员工的信息和年薪【按(表达式|别名)排序】
        <br>`select *, salary * 12 * ( 1 + ifnull(commission_pct, 0)) as 年薪 from employees order by 年薪 desc;`
        <br>case4: 按姓名的长度显示员工的姓名和工资【按函数排序】
        <br>`select length(last_name) 字节长度, last_name, salary from employees order by 字节长度 desc;`
        <br>case5: 查询员工信息，要求先按照工资排序，再按照员工编号排序【按多个字段排序】
        <br>`select * from employees order by salary asc, employee_id desc;`

    + ### 4.常见函数
    + ### 5.分组函数
    + ### 6.分组查询
    + ### 7.连接查询
    + ### 8.子查询
    + ### 9.分页查询
    + ### 10.联合查询

