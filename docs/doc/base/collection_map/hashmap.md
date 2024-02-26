* ## Intro(HashMap)

    ?> hello hashmap

    + ### 扩容方法

        ?> 实际是求取一个整形数值的最接近 ${\color{red}2^{n}}$ 的值。
        <br><br>可以通过以下方法获取：
        <br> $2^{0} < x <= 2^{1}-1$  $\hspace{5em} [00000000\quad00000000\quad00000000\quad0000000{\color{red}1}] - [00000000\quad00000000\quad00000000\quad0000000{\color{red}1}]$
        <br> $2^{1} < x <= 2^{2}-1$  $\hspace{5em} [00000000\quad00000000\quad00000000\quad000000{\color{red}10}] - [00000000\quad00000000\quad00000000\quad000000{\color{red}11}]$
        <br> $2^{2} < x <= 2^{3}-1$  $\hspace{5em} [00000000\quad00000000\quad00000000\quad00000{\color{red}100}] - [00000000\quad00000000\quad00000000\quad00000{\color{red}111}]$
        <br> $2^{3} < x <= 2^{4}-1$  $\hspace{5em} [00000000\quad00000000\quad00000000\quad0000{\color{red}1000}] - [00000000\quad00000000\quad00000000\quad0000{\color{red}1111}]$
        <br> $2^{4} < x <= 2^{5}-1$  $\hspace{5em} [00000000\quad00000000\quad00000000\quad000{\color{red}10000}] - [00000000\quad00000000\quad00000000\quad000{\color{red}11111}]$
        <br> $\hspace{2.5em}\vdots$
        <br> $2^{28} < x <= 2^{29}-1$  $\hspace{4.3em} [000{\color{red}10000\quad00000000\quad00000000\quad00000000}] - [000{\color{red}11111\quad11111111\quad11111111\quad11111111}]$
        <br> $2^{29} < x <= 2^{30}-1$  $\hspace{4.3em} [00{\color{red}100000\quad00000000\quad00000000\quad00000000}] - [00{\color{red}111111\quad11111111\quad11111111\quad11111111}]$
        <br> $2^{30} < x <= 2^{31}-1$  $\hspace{4.3em} [0{\color{red}1000000\quad00000000\quad00000000\quad00000000}] - [0{\color{red}1111111\quad11111111\quad11111111\quad11111111}]$
        
        !> 1). 由上述规律可知：若求取一个值的最接近的2次方，实际上是获取该数字的二进制最高位1，然后将最高位位置右边的值都设置为1，最后`+1`,即所需要的值。这个可以通过jdk1.7中扩容方法中调用的方法名: `Integer.highestOneBit()`猜验。
        <br>例如`10`的二进制为 $[0000 {\color{red}1}010]$ ,则最高位1在第四位，第四位右边的都为1后二进制为 $[0000 {\color{red}1111}]$ , 最后`+1`即 $[000{\color{red}10000}]$ = `16`。
        <br><br>2). 至于为什么刚开始就`-1`。则是因为如果一个值本身就是所需要的值。例如对于`16`($2^4$)它需要的值为`16`,但是按照刚才的方法下来后取的值为`32`($2^5$) 了就。
        <br>而且`-1`操作对其他值没有影响。例如计算8 <--> 16 中的值。如果为8，则`-1`后为7,最后取值为8.如果为9，则`-1`为8，最后取值为16。
        <br><br>3). 为什么只到`n |= n >>> 16;`,而没有`n |= n >>> 32;`？
        <br>我们可以举例说明。因为最大值=`MAXIMUM_CAPACITY = 1 << 30`, 即 $[0{\color{red}1}000000\quad00000000\quad00000000\quad00000000]$
        <br>如果传入的值为$[0{\color{red}1}000000\quad00000000\quad00000000\quad00000001]$。
        <br>减一后 $[0{\color{red}1}000000\quad00000000\quad00000000\quad00000000]$ 。
        <br>`n |= n >>> 1;` $\hspace{1em}[0{\color{red}11}00000\quad00000000\quad00000000\quad00000000]$ 。
        <br>`n |= n >>> 2;` $\hspace{1em}[0{\color{red}1111}000\quad00000000\quad00000000\quad00000000]$ 。
        <br>`n |= n >>> 4;` $\hspace{1em}[0{\color{red}1111111\quad1}0000000\quad00000000\quad00000000]$ 。
        <br>`n |= n >>> 8;` $\hspace{1em}[0{\color{red}1111111\quad11111111\quad1}0000000\quad00000000]$ 。
        <br>此时值在31-16位都已经为1了。所以对于一个整形int值，只需要在移动16位即可，移动32位没有任何意义。
        <br>`n |= n >>> 16;` $\hspace{0.5em}[0{\color{red}1111111\quad11111111\quad11111111\quad11111111}]$ 。
        <br>则根据程序逻辑`n >= MAXIMUM_CAPACITY`,取值为`MAXIMUM_CAPACITY` = $2^{30}$ 。

        ```java
        static final int MAXIMUM_CAPACITY = 1 << 30;

        /**
        * jdk 1.7 hashmap 扩容算法
        * @param number
        * @return
        */
        private static int roundUpToPowerOf2(int number) {
            // assert number >= 0 : "number must be non-negative";
            return number >= MAXIMUM_CAPACITY
                    ? MAXIMUM_CAPACITY
                    : (number > 1) ? Integer.highestOneBit((number - 1) << 1) : 1;
        }

        /**
        * jdk 1.8 hashmap 扩容算法
        * @param cap
        * @return
        */
        static final int tableSizeFor(int cap) {
            int n = cap - 1;
            n |= n >>> 1;
            n |= n >>> 2;
            n |= n >>> 4;
            n |= n >>> 8;
            n |= n >>> 16;
            return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
        }
        ```

* ## Problem

    1. [解决哈希冲突的方法](https://cloud.tencent.com/developer/article/1672781)
    2. 扰动函数
    3. 空间大小，及扩容容量
    4. jdk1.7头插死链形成

* ## Reference

    + https://www.bilibili.com/video/BV1b84y1G7o5
    + https://www.cnblogs.com/youzhibing/p/13915116.html