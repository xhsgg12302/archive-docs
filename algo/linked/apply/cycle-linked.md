---
tags: ["algo","linked"]
---

* ## Intro(CYCLE-LINKED | 环形链表检测)

    + ### 题目描述

        > [?] 给定一个链表，如果它是有环链表，实现一个算法返回环路的开头节点。若环不存在，请返回 null。如下：

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```mermaid
        graph LR;
            A[3] --> B[2];
            B --> C[0];
            C --> D[-4];
            D --> B;
            style B fill:#6f9,stroke:#333,stroke-width:4px;
        ```
        
        <!-- div:right-panel-50 -->
        ```mermaid
        graph LR;
            A[3] --> B[2];
            B --> C[0];
            C --> D[-4];
            D --> E[NULL];
        ```
        <!-- panels:end -->
        
        > [!ATTENTION] 在力扣题目 [面试题 02.08. 环路检测](https://leetcode.cn/problems/linked-list-cycle-lcci/description/) 中，根据官方题解中第二种使用 **快慢指针** 的方式，可以求得环形链表的环点。
        <br>但是有点费解的是，官方直接在首次相遇后推算出公式 $a=c+(n−1)(b+c)$，然后莫名其妙就判定如果此时使用`pre`和`slow`指针最终会在环点相遇。
        <br><br>对于我来理解的话，跨度有点大了。经 [大佬视频](https://www.bilibili.com/video/BV1if4y1d7ob/) 的解释，慢慢理解了。
        <br><br><span style='color:red'>大概就是：只有在第一次快慢指针相遇后，才能的出公式 $a=c+(n−1)(b+c)$，其他时间这个公式不一定成立。而且对于公式中的 n 来说，必定 >= 1，不存在 0 即（快指针还没跑一圈呢，就和慢指针相遇了）的情况。在得出公式后，我们可以知道，在首次相遇后这个时间点，会存在 $a = c + x圈 $ 这种关系。其中 a 和 c 只是距离问题，和快慢没关系了。所以在 **首次相遇** 情况下，可以理解为两个人从 a 开头和 c 开头，均速往环点走，一定会在环点相遇，无非是第二个人从 c 点多可能走几圈而已。

* ## Reference

    + https://leetcode.cn/problems/linked-list-cycle-lcci/description/
    + https://www.bilibili.com/video/BV1if4y1d7ob/