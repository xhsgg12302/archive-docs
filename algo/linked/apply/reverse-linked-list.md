---
tags: ["algo","linked"]
---

* ## Intro(REVERSE-LINKED-LIST | 反转链表)

    + ### 题目描述

        > [?] 给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。如下：

        <!-- panels:start -->
        <!-- div:left-panel-50 -->
        ```mermaid
        graph LR;
            A[1] --> B[2];
            B --> C[3];
            C --> D[4];
            D --> E[5];
            E --> F[NULL];
        ```
        
        <!-- div:right-panel-50 -->
        ```mermaid
        graph RL;
            E[5] --> D[4];
            D --> C[3];
            C --> B[2];
            B --> A[1];
            A[1] --> F[NULL];
        ```
        <!-- panels:end -->
        
        > [!ATTENTION] 在力扣题目 [206. 反转链表](https://leetcode.cn/problems/reverse-linked-list/description/?envType=problem-list-v2&envId=linked-list) 、[剑指 Offer 24. 反转链表](https://leetcode.cn/problems/fan-zhuan-lian-biao-lcof/description/) 中，根据官方题解可知有 **迭代** 和 **递归** 的方式。
        <br><br>对于递归的方式暂时先不记录。只对迭代通过图形的方式加深印象。
        <br>![](/.images/algo/linked/reverse-linked-list/rll-traversal-01.png ':size=90%')

* ## Reference

    + https://leetcode.cn/problems/reverse-linked-list/description/?envType=problem-list-v2&envId=linked-list
    + https://leetcode.cn/problems/fan-zhuan-lian-biao-lcof/description/