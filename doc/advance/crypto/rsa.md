---
---

* ## Intro(RSA)

    + ### 前情提要

        - #### 欧拉函数

            > [?] [欧拉函数](https://zh.wikipedia.org/wiki/欧拉函数) $\varphi(n)$ 是小于等于 n 的正整数中与 n 互质的数的数目。
            <br>例如：$\varphi(8) = 4$，因为 1，3，5 和 7 均与 8 互质。
            <br><br>有如下性质：
            <br>`1.` 欧拉函数是积性函数，即是说若 m,n 互质，则 $\varphi(m*n) = \varphi(m) * \varphi(n)$。
            <br>`2.` 如果 n 为质数，则 $\varphi(n) = n - 1$。
        
        - #### 同余

            > [?] 对某两个整数 a b，若它们除以正整数 m 所得的余数相等，则称 a b 对于模 m 同余，也就是说，存在整数 k 使得 $a - b = km$，则称 a,b 对于 m 来说是同余的，一般记作 $a \equiv b \pmod{m}$。
            <br>比如 $26 - 14 = 12 = 1 * 12$
            <br>记作 $26 \equiv 14 \pmod{12}$

        - #### 模逆元

            > [?] $ab \equiv 1 \pmod{n}$，
            <br>求整数 3 对同余 11 的模逆元素 x ：$3x \equiv 1 \pmod{11}$ ==> $3(4) \equiv 1 \pmod{11}$，所以此处为 x = 4。

    + ### RSA 公私匙产生过程

        > [!CAUTION] `1.`:随意选择两个大质数 p,q。计算 $N = pq$。
        <br>`2.` 根据欧拉函数求得中间值 $r = \varphi(N) = \varphi(p) * \varphi(q) = (p-1)(q-1)$
        <br>`3.` 选择一个小于 r 的整数 e，使 e 与 r 互质。并求 e 关于 r 的模逆元，命名为 d，$ed \equiv 1 \pmod{r}$
        <br>`4.` 销毁 $pq$。
        <br><br>得出的 $(N,e)$ 是公匙，$(N,d)$ 是私匙
        <br><br>使用工具获取 RSA 中的 $(N,e,d)$ 可以参考[OPENSSL提取公私匙信息](/devops/os/util/openssl.md#openssl提取公私匙信息)

    + ### RSA 加密和解密

        > [!TIP] 得到 N,e,d 后，将要加密的消息转化成 小于 N 的非负数 n。比如 N 为33，则可以使用 26个字母（a:1,b:2,c:3...）。
        <br>通过 $c = n^e \mod N$ 加密
        <br>通过 $n = c^d \mod N$ 解密

    + ### 计算过程简单示例

        > [?] 若质数 p = 11, q = 3， 则求得 N = 33, r = (p - 1)(q - 1) = 20。
        <br>在 20 中找一个与 20 互质的数，选择为 7，则 e = 7。
        <br>代入 $ed \equiv 1 \pmod{r}$，得到 $7(d) \equiv 1 \pmod{20}$，当 d = 3 时，满足。
        <br>综上 $(N,e,d) = (33,7,3)$。
        <br><br>假设要加密的内容为：`i love rsa`, 使用自定义编码规则，比如： [1,26] 表示 26 个字母，0 表示空格。
        <br>所以当前内容编码为：`[9 0 12 15 22 5 0 18 19 1]`。
        <br><br>加密如下对每一个字符进行 $c = n^7 \mod 33$ 运算，
        <br>第一个`i`,对应字母位置为 9，则 c = `echo '9 ^ 7 % 33' | bc` = 15. 第二个`空格`对应0，则 c = `echo '0 ^ 7 % 33' | bc` = 0，其他同理。
        <br>综上得出加密内容为：`[15 0 12 27 22 14 0 6 13 1]`。
        <br><br>解密每一个字符通过 $n = c^3 \mod 33$ 运算，
        <br>第一个数字为`15`，则 n = `echo '15 ^ 3 % 33' | bc` = 9. 第二个`空格`对应0，则 n = `echo '0 ^ 3 % 33' | bc` = 0，其他同理。
        <br>综上得出解密内容为：`[9 0 12 15 22 5 0 18 19 1]`。

* ## Reference
    + https://zh.wikipedia.org/wiki/RSA%E5%8A%A0%E5%AF%86%E6%BC%94%E7%AE%97%E6%B3%95
    + https://www.bilibili.com/video/BV1XP4y1A7Ui
    + https://github.com/12302-bak/idea-test-project/blob/v2.0.0-BAK/_0_base-learning/src/main/java/_base/encryption/rsa/RSA.java