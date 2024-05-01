* ## Buffer

    ![](/.images/doc/base/io/nio/buffer-01.png ':size=80% :align=center')

    <!-- panels:start -->
    <!-- div:left-panel-20 -->
    ```java
    public final Buffer clear() {
        position = 0;
        limit = capacity;
        mark = -1;
        return this;
    }
    ```
    <!-- div:left-panel-20 -->
    ```java
    public final Buffer mark() {
        mark = position;
        return this;
    }
    ```
    <!-- div:left-panel-20 -->
    ```java
    public final Buffer reset() {
        int m = mark;
        if (m < 0) throw new InvalidMarkException();
        position = m;
        return this;
    }
    ```
    <!-- div:left-panel-20 -->
    ```java
    public final Buffer flip() {
        limit = position;
        position = 0;
        mark = -1;
        return this;
    }
    ```
    <!-- div:left-panel-20 -->
    ```java
    public final Buffer rewind() {
        position = 0;
        mark = -1;
        return this;
    }
    ```
    <!-- panels:end -->

## Reference
* [Efficient data transfer through zero copy](https://developer.ibm.com/articles/j-zerocopy/?mhsrc=ibmsearch_a&mhq=java%20zero%20copy)
* [什么是零拷贝？mmap与sendFile的区别是什么？ ](https://www.cnblogs.com/ericli-ericli/articles/12923420.html)
* [清华大牛权威讲解nio,epoll,多路复用，更好的理解redis-netty-Kafka等热门技术](https://www.bilibili.com/video/BV11K4y1C7rm/?spm_id_from=333.337.search-card.all.click&vd_source=550a4dc4b2a914c0681a14307bbe8cbe)
* [同步与异步、阻塞与非阻塞傻傻分不清楚？你得从linux中的5种IO模型看起](https://juejin.cn/post/7119034398078402568)