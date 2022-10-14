## ZK 简介
> zab协议
>
> description

## ZK 实现原理

## ZK 常用命令
| command | function |
-|-
| help | 显示所有命令操作 |
| ls path [watch] | 使用ls 命令来查看当前znode中所包含的内容 |
| ls2 path [watch] | 查看当前节点数据，并能看到更新次数等数据 |
| create | 普通创建 -s 含有序列，-e 临时（重启或者超时消失）|
| get path [watch] | 获得节点值 |
| set | 设置节点具体值 |
| stat | 查看节点状态 |
| delete | 删除节点 | 
| rmr | 递归删除节点 | 
## ZK stat 结构
|show | explain |
-|-
|cZxid = 0x20000000e        			| create 创建znode更改的事务ID |
|ctime = Thu Aug 04 09\: 33\:08 UTC 2022 	| create znode 创建时间 |
|mZxid = 0x20000000e					| modify 修改znode更改的事务ID |
|mtime = Thu Aug 04 09&#58;33&#58;08 UTC 2022	| modify 修改时间 |
|pZxid = 0x20000000e					| p....  添加或删除子节点的znode更改事务ID |
|cversion = 0							| 表示对此znode子节点进行更改的次数|
|dataVersion = 0						| 表示对此znode数据的更改次数 |
|aclVersion = 0							| 表示对此znode的ACL进行更改的次数 | 
|ephemeralOwner = 0x0					| ephemeral类型？znode 所有者session Id : 0 |
|dataLength = 11						| znode 数据字段长度 | 
|numChildren = 0						| znode 的子节点的数量 |

## ZK 应用
1. 共享锁
	- explain
	> description
2. 独占锁
3. 公平锁
4. 非公平锁
5. 分布式锁
6. 命名服务
7. 配置中心

## ZK 问题
1. 为什么需要半数机制？
2. 为什么需要奇数台集群?
