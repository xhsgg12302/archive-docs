hello mysql


## Reference
* [MySQL .frm File Format](https://web.archive.org/web/20220520211318/https://dev.mysql.com/doc/internals/en/frm-file-format.html)
* [frm-parser](https://github.com/fpspammers/frm-parser)
* [mySQL数据库中.frm和.myi和.myd和.ibd文件是什么文件?](https://blog.csdn.net/liu1123055728/article/details/122824425)
* [innodb_ruby](https://github.com/jeremycole/innodb_ruby/wiki)
* [gem install 提示 You don't have write permissions for the /Library/Ruby/Gems/2.3.0 directory](https://blog.csdn.net/LYYCasablanca000/article/details/86024688)
* [MySQL 页完全指南——浅入深出页的原理](https://xie.infoq.cn/article/e5a721616fc4cf100b73fa296)

## .frm parse
* https://www.cnblogs.com/dreamanddead/p/recover-mysql-table-structure-from-frm-file.html
* https://downloads.mysql.com/archives/utilities/
* https://github.com/abg/dbsake
* https://dbsake.readthedocs.io/en/latest/
* https://blog.51cto.com/u_16213319/8545282

## .idb parse
https://github.com/jeremycole/innodb_ruby/wiki

## comment
* https://stackoverflow.com/questions/9298368/in-mysql-what-does-this-mean-40100-default-character-set-latin1

## select(\g|\G)
* https://stackoverflow.com/questions/2277014/why-the-g-in-select-from-table-name-g


## character,collation
|后缀|英文释义|描述|
|:--:|:--:|:--:|
| _ai | accent insensitive |不区分重音| 
| _as | accent sensitive |区分重音| 
| _ci | case insensitive |不区分大小写| 
| _cs | case sensitive |区分大小写| 
| _bin | binary |以二进制方式比较|

## mysql查看通过`use`激活的数据库
SELECT DATABASE();