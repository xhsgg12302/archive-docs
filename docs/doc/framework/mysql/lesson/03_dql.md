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
        <br><span style='color: blue'>以下都是单行函数</span>
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

        > [?] 语法： `select 函数名(实参列表) [from 表名];`
        <br><br>功能：用作统计使用，又称为聚合函数或统计函数或组函数
        <br><br>分类：`sum 求和`,`avg 平均值`,`max 最大值`,`min 最小值`,`count 个数`
        <br><br>特点：
        <br>`1).` **sum,avg**一般用于处理数值类型
        <br>`2).` 以上分组函数都忽略 NULL 值
        <br>`3).` 可以和 **distinct** 搭配实现去重的运算
        <br>`4).` **count**函数的单独介绍: 一般使用count(\*)用作统计行数
        <br>`5).` 和分组函数一同查询的字段要求是**group by**后的字段
        <br><br>一、简单的使用
        <br>`select sum(salary) from employees;` 
        <br>`select avg(salary) from employees;` 
        <br>`select min(salary) from employees;` 
        <br>`select max(salary) from employees;` 
        <br>`select count(salary) from employees;` 
        <br><br>`select sum(salary), avg(salary), min(salary), max(salary), count(salary) from employees;` 
        <br>`select sum(salary), round(avg(salary), 2), min(salary), max(salary), count(salary) from employees;` 
        <br><br>二、参数支持哪些类型
        <br>`select sum(last_name), avg(last_name) from employees;` // 返回0，不报错，无意义
        <br>`select max(last_name), min(last_name), count(last_name) from employees;`
        <br><br>三、忽略NULL
        <br>`select sum(commission_pct), avg(commission_pct), sum(commission_pct) / 35, sum(commission_pct) / 109 from employees;`
        <br>`select count(commission_pct) from employees;`
        <br><br>四、和 distinct 搭配
        <br>`select sum(distinct salary), sum(salary) from employees;`
        <br>`select count(distinct salary), count(salary) from employees;`
        <br><br>五、count函数的详细介绍
        <br> MYISAM 中 count(\*) 的效率高，
        <br> INNODB count(\*),count(1) 差不多，但比count(field)要高一些。
        <br>`select count(salary) from employees;`
        <br>`select count(*) from employees;` // <span style='color: blue'>~可以认为如果当前字段为NULL，会测试下一个~</span>
        <br>`select count(1) from employees;`
        <br><br>六、和分组函数一同查询的字段有限制
        <br>`select employee_id, avg(salary) from employees;` // 此处的 employee_id 没有任何意义。

    + ### 6.分组查询

        > [?] 语法： `select columnM, func(columnN) from 表名 [where 筛选条件] [group by colomnM] [order by 排序列表 [asc|desc]];`
        <br><br>注意：查询列表比较特殊，要求是分组函数和group by 后出现的字段。
        <br><br>特点：
        <br>`1).` 分组查询中的筛选条件分为两类，分组前和分组后。也就是下文中的**having**解释
        <br>`2).` group by 子句支持单个字段分组，多个字段分组(多个字段之间用逗号分隔开没有顺序要求)，表达式或函数(用的较少)
        <br>`3).` 也可以添加排序(排序放在整个分组查询的最后)
        <br><br>技巧：
        <br>`①`: 分组函数做条件肯定是放在having子句中
        <br>`②`: 能用分组前筛选的，就优先考试使用分组前筛选(也就是where后面)，关乎性能问题
        <br><br>*引入、查询每个部分的平均工资*
        <br>`select department_id, avg(salary) from employees group by department_id;`
        <br>![](/.images/doc/framework/mysql/lesson/03-dql/dql-02.png ':size=50%')
        <br><br>一、查询每个工种的最高工资
        <br>`select job_id, max(salary) from employees group by job_id;`
        <br><br>二、查询每个部门上的部门个数
        <br>`select location_id, count(1) from departments group by location_id;`
        <br><br>*添加筛选条件*
        <br>一、查询邮箱中包含a字符的，每个部分的平均工资
        <br>`select department_id, avg(salary) from employees where email like '%a%' group by department_id;`
        <br>二、查询有奖金的每个领导手下员工的最高工资
        <br>`select manager_id, max(salary) from employees where commission_pct is not null group by manager_id;`
        <br><br>*添加复杂的筛选条件*
        <br>`having`: <span style='color: blue'>与where相比，where作用在分组动作前，而having作用在分组动作后</span>。
        <br>一、查询哪个部分的员工个数 > 2
        <br>`select department_id, count(1) AS _count from employees group by department_id having _count > 2;`
        <br><br>二、查询每个工种有奖金的员工的最高工资>12000的工种编号和最高工资
        <br>`select job_id, max(salary) AS _max from employees where commission_pct is not null group by job_id having _max > 12000;`
        <br><br>三、查询领导编号>102的每个领导手下的最低工资>5000的领导编号是哪个，以及其最低工资
        <br>`select manager_id, min(salary) _min from employees where manager_id > 102 group by manager_id having _min > 5000;`
        <br><br>*按表达式或函数分组*
        <br>一、按员工姓名的长度分组，查询每一组的员工个数，筛选员工个数>5 的有哪些？
        <br>`select length(last_name) as _len, count(1) as _count from employees group by _len having _count > 5;`
        <br><br>*按多个字段分组*
        <br>一、查询每个部门每个工种的员工的平均工资
        <br>`select department_id, job_id, avg(salary) from employees group by department_id, job_id;`
        <br><br>*添加排序*
        <br>一、查询每个部门每个工种的员工的平均工资，并且按平均工资的高低显示
        <br>`select department_id, job_id, avg(salary) AS _avg from employees group by department_id, job_id order by _avg;`

    + ### 7.连接查询

        > [?] 含义：又称多表查询，当查询的字段来自于多个表时，就会用到`连接查询`
        <br><br>笛卡尔乘积现象：表1 有m行，表2，有n行，结果=m*n行。发生原因：没有有效的连接条件，添加条件可以避免。
        <br><br>分类：
        <br>`1).` 按年代分：sql92标准：仅仅支持内连接，sql99标准（推荐）：支持内连接+外连接（左外+右外）+ 交叉连接
        <br>`2).` 按功能：内连接(等值连接，非等值连接，自连接)，外连接(左外连接，右外连接，全外连接)，交叉连接

        > [!NOTE]*一、sql92标准*
        <br>语法： `select 查询列表 from 表1 别名, 表2 别名 [where 筛选条件] [group by 分组] [having 筛选条件] [order by 排序列表 [asc|desc]];`
        <br>特点：
        <br>`①`: 多表等值连接的结果为多表的交集部分
        <br>`②`: n表连接，至少需要n-1个连接条件
        <br>`③`: 多表的顺序没有要求 
        <br>`④`: 一般需要为表起别名
        <br>`⑤`: 可以搭配前面介绍的所有子句使用，比如排序，分组，筛选。
        <br><br>*sql92.1 等值连接*:
        <br>sql92.1.1 demo
        <br>case1: 查询女神名和对应的男神名
        <br>`select name, boyName from boys,beauty where boys.id = beauty.boyfriend_id;`
        <br>case2: 查询员工名和对应的部门名
        <br>`select last_name, department_name from employees, departments where employees.department_id = departments.department_id;`
        <br>sql92.1.2 为表起别名
        <br>①提高语句的简洁度，②区分多个重名的字段,注意：如果为表起别名，则查询字段就不能用原来的表名去限定了。
        <br>case1: 查询员工名，工种号，工种名
        <br>`select last_name, j.job_id,j.job_title from employees e, jobs j where e.job_id = j.job_id;`
        <br>sql92.1.3 两个表的顺序可以调换
        <br>case1: 查询员工名，工种号，工种名
        <br>`select last_name, j.job_id,j.job_title from jobs j, employees e where e.job_id = j.job_id;`
        <br>sql92.1.4 可以添加筛选
        <br>case1: 查询有奖金的员工名，部门名
        <br>`select last_name, department_name, commission_pct from employees e, departments d where e.department_id = d.department_id and e.commission_pct is not null;`
        <br>case2 : 查询城市名中第二个字符为o的部门名和城市名
        <br>`select department_name, city from departments d, locations l where d.location_id = l.location_id and city like '_o%';`
        <br>sql92.1.5 可以添加分组
        <br>case1: 查询每个城市的部门个数
        <br>`select location_id, count(1) as _count from departments group by location_id;`
        <br>case2: ~查询有奖金的每个部门的部门名和部门的领导编号和该部门的最低工资~
        <br>`select department_name, e.manager_id, min(salary) as _min from employees e, departments d where e.commission_pct is not null group by d.department_id;`
        <br><br>*sql92.2 非等值连接*:
        <br>sql92.2.1 demo
        <br>case1: 查询员工的工资和工资级别
        <br>`select salary, grade_level from employees e, job_grades g where salary between g.lowest_sal and g.highest_sal;`
        <br>`select salary, grade_level from employees e, job_grades g where salary between g.lowest_sal and g.highest_sal and g.grade_level = 'A';`
        <br><br>*sql92.3 自连接*:
        <br>sql92.3.1 demo
        <br>case1: 查询 员工名和上级的名称
        <br>`select e.employee_id, e.last_name, m.employee_id, m.last_name from employees e, employees m where e.manager_id = m.employee_id;`

        > [!NOTE]*一、sql99标准*
        <br>语法： `select 查询列表 from 表1 别名 [连接类型] join 表2 别名 on 连接条件 [where 筛选条件] [group by 分组] [having 筛选条件] [order by 排序列表 [asc|desc]];`
        <br>分类：内连接(inner),外连接：左外(left[outer])，右外(right[outer])，全外(full[outer])，交叉连接(cross)
        <br>特点：
        <br>`①`: 多表等值连接的结果为多表的交集部分
        <br>`②`: n表连接，至少需要n-1个连接条件
        <br>`③`: 多表的顺序没有要求 
        <br>`④`: 一般需要为表起别名
        <br>`⑤`: 可以搭配前面介绍的所有子句使用，比如排序，分组，筛选。
        <br><br>*sql99.1 内连接*:
        <br>分类：等值，非等值，自连接
        <br>特点：
        <br>`①`: 添加排序，分组
        <br>`②`: inner 可以省略
        <br>`③`: 筛选条件放在where后面,连接条件放在on后面，提高分离性，便于阅读
        <br>`④`: inner join连接和sql92语法中的等值连接效果是一样的，都是查询多表的交集
        <br>sql99.1.1 等值连接
        <br>case1: 查询员工名，部门名
        <br>`select last_name, department_name from employees e inner join departments d on e.department_id = d.department_id;`
        <br>case2: 查询名字中包含e的员工名和工种名（添加筛选）
        <br>`select last_name, job_title from employees e inner join jobs j on e.job_id = j.job_id where e.last_name like '%e%';`
        <br>case3: 查询部门个数>3的城市名和部门个数（添加分组+筛选）
        <br>`select city, count(1) as _count from departments d inner join locations l on d.location_id = l.location_id group by city having _count > 3;`
        <br>case4: 查询哪个部门的员工个数>3的部门名和员工个数，并按照个数降序（添加排序）
        <br>`select department_name, count(1) as _count from employees e inner join departments d on e.department_id = d.department_id group by e.department_id having _count > 3 order by _count desc;`
        <br>case5: 查询员工名，部门名，工种名，并按部门名降序
        <br>`select last_name, department_name, job_title from employees e inner join departments d on e.department_id = d.department_id inner join jobs j on e.job_id = j.job_id order by d.department_name desc;`
        <br>sql99.1.2 非等值连接
        <br>case1: 查询员工的工资级别
        <br>`select last_name, grade_level from employees e inner join job_grades g on e.salary between g.lowest_sal and g.highest_sal;`
        <br>case1: 查询工资级别个数>2的个数，并按照工资降级排序
        <br>`select grade_level, count(1) as _count from employees e inner join job_grades g on e.salary between g.lowest_sal and g.highest_sal group by grade_level having _count > 20 order by _count desc;`
        <br>sql99.1.3 自连接
        <br>case1: 查询员工的名字，上级的名字
        <br>`select e.last_name, m.last_name from employees e inner join employees m on e.manager_id = m.employee_id;`
        <br>case1: 查询员工姓名中包含字符k的员工的名字，上级的名字
        <br>`select e.last_name, m.last_name from employees e inner join employees m on e.manager_id = m.employee_id where e.last_name like '%k%';`
        <br><br>*sql99.2 外连接*:
        <br>特点：
        <br>`①`: 外连接的查询结果为主表中的所有记录(从表中有，则显示，没有显示NULL)
        <br>`②`: 左外连接(left join 左边的是主表），右外连接，right join右边的是主表。
        <br>`③`: 左外和右外交换两表的顺序，可以实现同样的效果
        <br>`④`: 全外连接就是两表的并集(mysql不支持)
        <br>`⑤`: 交叉连接(就是sql92中的逗号连接)
        <br>case1: 查询没有男朋友的女神
        <br>`select b.id, b.name from beauty b left join boys y on b.boyfriend_id = y.id where y.id is null;`
        <br>case2: 查询哪个部门没有员工
        <br>`select d.department_name from departments d left join employees e on d.department_id = e.department_id where e.employee_id is null;`
        <br>case3: 交叉连接
        <br>`select b.*, bo.* from beauty b cross join boys bo;`

    + ### 8.子查询

        > [?] 含义：出现在其他语句中的select语句，称为`子查询或内查询`，内部嵌套其他select语句的查询，称为`外查询或主查询`。
        <br>`select first_name from employees where department_id in (select department_id from departments where location_id = 1700);`
        <br><br>分类：
        <br>`1).` 按子查询出现的位置：select后面(仅仅支持标量子查询)，from后面（支持表子查询），where或having后面（标量子查询，列子查询，行子查询），exists后面（表子查询） 
        <br>`2).` 按结果集的行列数不同：标量子查询（结果集只有一行一列），列子查询（结果集只有一列多行），行子查询（结果集有一行多列），表子查询（结果集一般为多行多列）

        > [!NOTE]*一、where或者having后面*
        <br>语法： `select 查询列表 from 表 [where (子查询)] [group by 分组] [having (子查询)] [order by 排序列表 [asc|desc]];`
        <br>特点：
        <br>`①`: 子查询放在小括号内
        <br>`②`: 子查询一般放在条件的右侧
        <br>`③`: 标量子查询一般搭配着单行操作符使用: `>, <, >=, <=, =, <>`，列子查询一般搭配着多行操作符时候用: `in, any/some, all`
        <br>`④`: 子查询的执行优先于主查询的执行，主查询的条件用到了子查询的结果。
        <br><br>非法使用标量子查询（1,不是一行一列，2,干脆没有值）
        <br><br>*1. 标量子查询*:
        <br>case1: 谁的工资比Abel高
        <br>`select * from employees where salary > (select salary from employees where last_name = 'Abel');`
        <br>case2: 返回iob_id 与 141 号员工相同，salary 比 143号员工多的员工的姓名，job_id 和工资
        <br>`select last_name, job_id, salary from employees where job_id = (select job_id from employees where employee_id = 141) and salary > (select salary from employees where employee_id = 143);`
        <br>case3: 返回公司工资最少的员工的last_name, job_id 和 salary
        <br>`select last_name, job_id, salary from employees where salary = (select min(salary) from employees);`
        <br>case4: 查询最低工资大于 50号部门的最低工资 的部门的部门Id 和 其最低工资
        <br>`select department_id, min(salary) as _min from employees group by department_id having _min > (select min(salary) from employees where department_id = 50);`
        <br><br>*2. 列子查询（多行子查询）*:
        <br>case1: 返回location_id是1400或1700 的部门中的所有员工姓名
        <br>`select last_name from employees where department_id in (select distinct department_id from departments where location_id in(1400, 1700));`
        <br>case2: ~返回其他部门中比job_id 为'IT_PROG'部门任意工资低的 员工的工号，姓名，job_id以及salary。~
        <br>case2: 返回其他工种中比job_id 为'IT_PROG'工种任意工资低的 员工的工号，姓名，job_id以及salary。
        <br>`select employee_id, last_name, job_id, salary from employees where job_id <> 'IT_PROG' and salary < (select max(salary) from employees where job_id = 'IT_PROG');`
        <br>case2: 返回其他工种中比job_id 为'IT_PROG'工种所有工资低的 员工的工号，姓名，job_id以及salary。
        <br>`select employee_id, last_name, job_id, salary from employees where job_id <> 'IT_PROG' and salary < (select min(salary) from employees where job_id = 'IT_PROG');`
        <br><br>*3. 行子查询（结果集一行多列或多行多列）*:
        <br>case1: 查询员工编号最小并且工资最高的员工信息
        <br>`select * from employees where (employee_id, salary) = (select min(employee_id), max(salary) from employees);`

        > [!NOTE]*二、select后面*
        <br>case1: 查询每个部门的个数
        <br>`select d.*, (select count(1) from employees e where  e.department_id = d.department_id) from departments d;`
        <br>case2: ~查询员工号 = 102 的部门名~

        > [!NOTE]*三、from后面*
        <br>要求：将子查询结果充当一张表，要求必须起别名
        <br>case1: 查询每个部门的平均工资的工资等级
        <br>`select temp.*, jg.grade_level from (select department_id, avg(salary) as _avg from employees group by department_id) temp inner join job_grades jg on temp._avg between lowest_sal and highest_sal;`

        > [!NOTE]*四、exists后面(相关子查询)*
        <br>语法： `select exists(完整的查询语句)` ,结果 1或0
        <br>case1: 查询有员工的部门名
        <br>`select department_name from departments d where exists (select * from employees e where e.department_id = d.department_id);`
        <br>`select department_name from departments d where department_id in (select department_id from employees);`
        <br>case2: 查询没有女朋友的男神信息
        <br>`select bo.* from boys bo where not exists (select * from beauty b where b.boyfriend_id = bo.id);`
        <br>`select bo.* from boys bo where bo.id not in (select boyfriend_id from beauty);`

    + ### 9.分页查询
    + ### 10.联合查询

