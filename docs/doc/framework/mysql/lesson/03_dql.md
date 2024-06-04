* ## DQL语言的学习

    + ### 基础查询
    
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
        <br># case: 查询员工名和姓连接成一个字段，并显示为姓名
        <br>`select concat(last_name,first_name) as 姓名 from employees limit 10;`
        
    + ### 条件查询
    + ### 排序查询
    + ### 常见函数
    + ### 分组函数
    + ### 分组查询
    + ### 连接查询
    + ### 子查询
    + ### 分页查询
    + ### 联合查询

