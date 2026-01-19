* ## Intro(System of numeration)

    + [补码](./complement.md)
    
    + ### 进制转换图

        ![](/.images/corner/numeration/num-base-convert-01.png ':size=80% :align=center')

    + ### 浮点数

        > [?] 按照 [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754) 浮点数标准，一般会以四个字节来保存，布局结构图如下：
        <br><br><span style='padding-left:2em'>![](/.images/corner/numeration/num-float-layout-01.png ':size=60%')
        <br><br>在谈论计算保存字节值之前先了解一下科学计数法：
        <br>在十进制中： $ 12302 = 1.2302 * 10^4 $， $ 0.00123 = 1.23 * 10^{-3}$
        <br>在二进制中： $ 11000 = 1.1 * 2^4 $， $ 0.0101 = 1.01 * 2^{-2}$
        <br><br>将 9.625 这个数改成 IEEE 754 标准的机器数步骤如下：
        <br>`1.` 确定符号位，正数，所以为 0。
        <br>`2.` 将十进制转换为二进制：
        <br><span style='padding-left:2em'>![](/.images/corner/numeration/num-float-convert-02.png ':size=40%')
        <br>`3.` 科学计数法规范化：$ 1001.101 = 1.001101 * 2^3 $。
        <br>`4.` 由步骤 3 可知，exponent = 3。而 IEEE 754 单精度格式使用偏置值 127 来表示指数，所以加上偏置值 $ 3 + 127 = 130 = 10000010 $
        <br>`5.` 组合起来就是: $0\space 10000010_8\space 00110100000000000000000_{23}$
        <br><br>Java 代码验证：
        <br><br><span style='padding-left:2em'>![](/.images/corner/numeration/num-float-verify-03.png ':size=90%')

        - #### 精度丢失过程

            ![](/.images/corner/numeration/num-float-missing-04.png ':size=90% :align=center')

            <!-- panels:start -->
            <!-- div:left-panel-50 -->
            1. 先将 16777216 转化为二进制表示： 1 00000000 00000000 00000000
            2. 将二进制写成科学计数法：$ 1.0 * 2^{24} $
            3. 计算 exponent : 24 + 127 = 10010111，外加位数 23 个 0。
            4. 综上：`0 10010111 0000000 00000000 00000000`。
            <!-- div:right-panel-50 -->
            1. 先将 16777217 转化为二进制表示： 1 00000000 00000000 00000001
            2. 将二进制写成科学计数法：$ 1.00000000 \hspace{0.5em} 00000000 \hspace{0.5em} 00000001 * 2^{24} $
            3. 计算 exponent : 24 + 127 = 10010111，<span style='color:blue'>只有23位，只能把最后的1截断，相当于加23个0。</span>
            4. 综上：`0 10010111 0000000 00000000 00000000`。
            <!-- panels:end -->

            ---

            <!--
            > [!WARNING] <span style='font-size:18px;color:red;display:block;text-align:center'>经典（ 0.1 + 0.2 != 0.3）</span>
            <br><br>$0.1_{10}  = 0.0001100110011001100110011001100110011..._{2}（无限循环）$ 可以对照上面 9.625 的计算方法，会发现会循环下去。
            <br>$ \hspace{2.3em} = 1.100110011001100110011001100110011..._{2} * 2^{-4}$， exponent = (-4 + 127) = 123 = 1111011
            <br>$ \hspace{2.3em} = 0 \hspace{0.5em} {\color{red}01111011} \hspace{0.5em} 1001100 11001100 11001100（精度丢失，只有 23 位）$ 
            <br><br>$0.2_{10}  = 0.0011001100110011001100110011001100110..._{2}（无限循环）$ 可以对照上面 9.625 的计算方法，会发现会循环下去。
            <br>$ \hspace{2.3em} = 1.100110011001100110011001100110011..._{2} * 2^{-3}$， exponent = (-3 + 127) = 124 = 1111100
            <br>$ \hspace{2.3em} = 0 \hspace{0.5em} {\color{red}01111100} \hspace{0.5em} 1001100 11001100 11001100（精度丢失，只有 23 位）$ 
            -->

* ## Reference