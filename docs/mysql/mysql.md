## 简介
1. #### 介绍
2. #### 功能
3. #### 负载均衡

## 编译

## 配置文件

## MVCC[ multi-version concurrency control]
1. #### 实现原理
	> mvcc实现隔离级别依赖于记录中的三个隐藏字段,undolog,read view来实现

	每行记录除了我们自定义的字段外，还有数据库隐式定义的如下字段。
	|field|explain|
	-|-
	|DB_TRX_ID	| 6Byte，最近事务修改ID，记录创建这条记录或者最后一次修改该记录的事务ID |
	|DB_ROLL_PTR| 7Byte,回滚指针，指向这条记录的上一个版本，用于配合undo log，指向上一个旧版本 |
	|DB_ROW_ID | 6Byte,隐藏的主键，如果数据表没有主键，那么innodb会自动生成一个6byte的row_id|
