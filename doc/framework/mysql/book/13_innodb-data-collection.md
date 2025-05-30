
* ## 开场序言

    > [!CAUTION] 我们前边唠叨查询成本的时候经常用到一些统计数据，比如通过 `SHOW TABLE STATUS` 可以看到关于表的统计数据，通过 `SHOW INDEX` 可以看到关于索引的统计数据，那么这些统计数据是怎么来的呢？它们是以什么方式收集的呢？本章将聚焦于 InnoDB 存储引擎的统计数据收集策略，看完本章大家就会明白为啥前边老说 InnoDB 的统计信息是不精确的估计值了（言下之意就是我们不打算介绍 MyISAM 存储引擎统计数据的收集和存储方式，有想了解的同学自己个儿看看文档哈）。

* ## 两种不同的统计数据存储方式
* ## 基于磁盘的永久性统计数据

    + ### innodb_table_stats

        - #### n_rows统计项的收集
        - #### clustered_index_size和sum_of_other_index_sizes统计项的收集
    + ### innodb_index_stats
    + ### 定期更新统计数据
    + ### 手动更新 innodb_table_stats 和 innodb_index_stats 表

* ## 基于内存的非永久性统计数据
* ## innodb_stats_method的使用
* ## 总结