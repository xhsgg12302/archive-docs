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

        > [?] 语法： `select 函数名(实参列表) [from 表名];`
        <br><br>函数帮助：`help substr;`
        <br>特点：1，叫什么(函数名) 2，干什么(函数功能)
        <br>好处：1，隐藏了实现的细节 2，提高代码的重用性
        <br>分类：`单行函数`，`分组函数(做统计用，又称为统计函数，聚合函数，组函数)`

        - #### 一、字符函数

            > [!NOTE]
            一、length()获取参数值的字节个数，和**字符集**有关系
            <br>`select length('John');`
            <br>`select length('张三丰hahaha');`
            <br><br>二、concat() 拼接字符串
            <br>`select concat(last_name, '_', first_name) 姓名 from employees;`
            <br><br>三、upper(), lower() 大小写转换
            <br>`select concat(upper(last_name), '_', lower(first_name)) 姓名 from employees;`
            <br><br>四、substr(), substring() 字符串截取
            <br>`select substr('李莫愁爱上了陆展元', 7) out_put;`
            <br>`select substr('李莫愁爱上了陆展元', 1, 3) out_put;`
            <br>case1: 姓名中首字符大写，其他字符小写然后用_拼接，显示出来
            <br>`select concat(upper(substr(last_name, 1, 1)), '_', lower(substr(last_name, 2))) out_put from employees;`
            <br><br>五、instr() 返回子串第一次出现的索引，如果找不到返回0
            <br>`select instr('杨不殷六侠悔爱上了殷六侠', '殷六侠') out_put;`
            <br>`select instr('杨不殷六侠悔爱上了殷六侠', '殷八侠') out_put;`
            <br><br>六、trim() 去前后空格
            <br>`select length(trim('  张翠山   ')) out_put;`
            <br>`select trim('a' from 'aaaaahello a cworldaaaaaaa') out_put;`
            <br><br>七、lpad()，rpad() 用指定的字符实现左,右填充指定长度
            <br>`select lpad('殷素素', 10, '*') out_put;`
            <br>`select rpad('殷素素', 12, '*') out_put;`
            <br><br>八、replace() 全部替换
            <br>`select replace('张无忌第一次碰见周芷若就爱上了周芷若','周芷若', '赵敏') out_put;`

        - #### 二、数学函数

            > [!NOTE]
            一、round() 四舍五入
            <br>`select round( 1.65);` // 2
            <br>`select round( 1.45);` // 1
            <br>`select round(-1.55);` // -2
            <br>`select round(1.567, 2);` // 1.57
            <br><br>二、ceil() 向上取整，返回 >= 该参数的最小整数
            <br>`select ceil( 1.52);` // 2
            <br>`select ceil(-1.02);` // -1
            <br><br>三、floor() 向下取整，返回 <= 该参数的最大整数
            <br>`select floor( 9.99);` // 9
            <br>`select floor(-9.99);` // -10
            <br><br>四、truncate 截断
            <br>`select truncate(1.65, 1);` // 1.6
            <br><br>五、mod 取余 mod(a,b) ==> a - a/b * b
            <br>`select mod(-10, -3);` // -1
            <br>`select mod(-10,  3);` // -1
            <br>`select mod( 10,  3);` // 1
            <br>`select mod( 10, -3);` // 1

        - #### 三、日期函数

            <!-- panels:start -->
            <!-- div:left-panel-50 -->
            > [!NOTE]
            一、now() 返回当前系统日期+时间
            <br>`select now();` // 2024-06-06 07__colon__03__colon__35
            <br><br>二、curdate() 返回当前系统日期，不包含时间
            <br>`select curdate();` // 2024-06-06
            <br><br>三、curtime() 返回当前系统时间，不包含日期
            <br>`select curtime();` // 07__colon__05__colon__20
            <br><br>四、获取自定的部分，年，月，日，小时，分钟，秒
            <br>`select year(now());`
            <br>`select year('1998-1-01');`
            <br>`select month(now());`
            <br>`select monthname(now());` // June
            <br><br>五、str_to_date() 将日期格式的字符串转换成指定格式的日期
            <br>`select str_to_date('9-13-1999', '%m-%d-%Y');` // 1999-09-13
            <br><br>六、date_format() 将日期转成字符
            <br>`select date_format('2018/6/6','%Y年%m月%d日');` // 2018年06月06日
            <!-- div:right-panel-50 -->
            ![](/.images/doc/framework/mysql/lesson/03-dql/dql-01.png ':size=100%')
            <!-- panels:end -->

        - #### 四、其他函数

            > [!NOTE]
            `select version();` ,`select database();`,`select user();`

        - #### 五、流程控制函数

            > [!NOTE]
            一、if()
            <br>`select if(10 < 5, 'hello', 'world');`
            <br><br>二、case()
            <br> 语法一： case 表达式 when 常量1 then 显示值1 when 常量2 then 显示值2 ... else 显示值else end
            <br>case1: 查询员工的工资，要求，部门号=30，工资为1.1倍：40，1.2：50，1.3
            <br>`select salary, department_id, case department_id when 30 then salary * 1.1 when 40 then salary * 1.2 when 50 then salary * 1.3 else salary end as after from employees;`
            <br> 语法二： case when 条件1 then 显示值 when 条件2 then 显示值 else 显示值 else end
            <br>case2: 查询员工的工资的情况，要求，工资大于20000，显示A， >15000:B，>10000:C
            <br>`select salary, case when salary > 20000 then 'A' when salary > 15000 then 'B' when salary > 10000 then 'C' else 'D' end as after from employees;`

    + ### 5.分组函数
    + ### 6.分组查询
    + ### 7.连接查询
    + ### 8.子查询
    + ### 9.分页查询
    + ### 10.联合查询

