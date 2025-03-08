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

* ## Reference