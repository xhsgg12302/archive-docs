* ## Intro(HashMap)

    ?> hello hashmap

    + ### 扩容方法

        ?> 实际是求取一个整形数值的最接近 ${\color{red}2^{n}}$ 的值。

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