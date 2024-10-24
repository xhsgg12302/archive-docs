* ## Intro(VARIABLES)

    > [?] 变量分类：
    <br>【系统变量 [全局变量 | 会话变量] 】
    <br>【用户自定义变量 [用户变量 | 局部变量]】：

    + ### 系统变量

        > [?] 说明：
        <br>变量由系统提供的，不是用户定义的，属于服务器层面
        <br><br>使用语法：
        <br>case1：查看所有的系统变量: ``
        <br>`show global|session variables;`
        <br><br>case2：查看满足条件的部分系统变量: ``
        <br>`show global|session variables like '%char%';`
        <br><br>case3：查看指定的某个系统变量的值: ``
        <br>`select @@global|session.变量名;`
        <br><br>case4：为某个变量赋值: 
        <br>`set global|session 系统变量名 = 值;`
        <br>`set @@global|session .系统变量名 = 值;`

        > [!CAUTION] 如果是全局级别，则需要加global，如果是会话级别，则需要加session，如果不写，则默认session

        - #### 全局变量

            > [!NOTE] 作用域：服务器每次启动将会为所有的全局变量赋初始值，针对于所有的会话（连接）有效，但不能跨重启。<span style='color: blue'>可通过配置文件进行重启生效</span>
            <br><br>使用语法：
            <br>case1：查看所有的系统变量:
            <br>`show global variables;`
            <br><br>case2：查看满足条件的部分系统变量:
            <br>`show global variables like '%char%';`
            <br><br>case3：查看指定的某个系统变量的值:
            <br>`select @@global.autocommit;`
            <br>~`select @@tx_isolation;`~
            <br><br>case4：为某个变量赋值:
            <br>`set global autocommit = 1;`，`set @@global.autocommit = 0;`

        - #### 局部变量

            > [!NOTE] 作用域：仅仅针对当前会话（连接）有效
            <br><br>使用语法：
            <br>case1：查看所有的会话变量:
            <br>`show session variables;`，`show variables;`
            <br><br>case2：查看满足条件的部分会话变量:
            <br>`show variables like '%char%';`
            <br><br>case3：查看指定的某个会话变量的值:
            <br>`select @@session.autocommit;`
            <br>`select @@tx_isolation;`
            <br><br>case4：为某个会话变量赋值:
            <br>`set session tx_isolation = 'read-committed';`，`set @@session.tx_isolation='read-uncommitted';`

    + ### 自定义变量

        > [?] 说明：变量是用户自定义的，不是由系统提供
        <br>使用步骤：声明，赋值，使用（查看，比较，运算等）
        
        - #### 用户变量

            > [!NOTE] 作用域：针对当前会话（连接）有效，同于会话变量的作用域
            <br><br>应用位置：可以在任何地方，也就是begin end 或者begin end外面
            <br><br>一、声明并初始化（赋值操作符可以是[ `=` | `:=` ]）
            <br>`set @用户变量名 = 值;`
            <br>`set @用户变量名 := 值;`
            <br>`select @用户变量名 := 值;`
            <br><br>二、赋值（更新用户变量的值）
            <br>2.1 方式一： 通过set或select，和**声明并初始化**中的语句一样。
            <br>2.2 方式二： 通过select into
            <br>&nbsp;&nbsp;&nbsp;`select count(1) into @count from employees;`
            <br><br>三、使用（查看用户变量的值），`select @用户变量名`
            <br>`select @count;`

            > [!CAUTION] 
            `set @name = 'john';`，`set @name = 1000;` <span style='color: blue'>变量弱类型</span>
            
        - #### 局部变量

            > [!NOTE] 作用域：仅仅在定义它的begin end中有效
            <br><br>应用位置：begin end中的第一句话
            <br><br>一、声明
            <br>`declare 变量名 类型`
            <br>`declare 变量名 类型 default 值;`
            <br><br>二、赋值
            <br>2.1 方式一： 通过set或select
            <br>&nbsp;&nbsp;&nbsp;`set 局部变量名 = 值;`
            <br>&nbsp;&nbsp;&nbsp;`set 局部变量名 := 值;`
            <br>&nbsp;&nbsp;&nbsp;`select @局部变量名 := 值;`
            <br>2.2 方式二： 通过select into
            <br>&nbsp;&nbsp;&nbsp;`select count(1) into @count from employees;`
            <br><br>三、使用，`select 局部变量`
            <br>`select @count;`
            <br><br>case1：声明两个变量并储初始值，求和，打印
            <br>`set @m = 1;`
            <br>`set @n = 2;`
            <br>`set @sum = @m + @n;`
            <br>`select @sum;`

        - #### 对比

            |   | 作用域 | 定义和使用的位置 | 语法 |
            | :--: | :--: | :--: | :--: |
            | 用户变量 | 当前会话 | 会话中的任何地方 | 必须加`@`符号，不用限定类型 |
            | 局部变量 | begin end | 只能在begin end中，且为第一句话 | 一般不用加`@`，需要限定类型 |