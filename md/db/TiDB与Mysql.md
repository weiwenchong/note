
## TiDB
PingCAP公司开发的开源分布式数据库,兼容Mysql,具备强一致性和高可用性.最主要的优势是支持无限水平扩展,支持ACID事务.结合了关系型数据库和非关系型数据库的优点.
## Mysql
关系型数据库,mysql AB公司开发,目前属于Oracle,是最流行的关系型数据库.

## TiDB适用场景
1. 原Mysql业务受到单机容量和性能瓶颈,可以适用TiDB切换.
2. 大数据量下Mysql查询慢.
3. 数据增长快,不想做分库分表.
4. 大数据量下,有并发实时写入,实时查询,实时统计分析的需求.
5. 有分布式事务,多数据中心强一致性,高可用性需求.

## TiDB不适用场景
1. 单机Mysql性能能满足.
2. 数据少于5000w.
3. 没有高可用,强一致和多数据中心需求.

## TiDB特点
1. 兼容Mysql.
2. 水平弹性扩展.
3. 分布式事务.
4. 高可用.强一致.
5. 云原生.
6. 成本高.

## TiDB的Sql注意事项
事务使用乐观锁.  
```
alter table table_name add index idx_ab (a, b);
alter table table_name add index idx_c (c);
select id from table_name where (a=1 and b=1) or c=2;
```
使用mysql的情况下这条是可以走到索引的,但是在TiDB中是走不到的,需要这样写.
```
select id from table_name where (a=1 and b=1) UNION select id from table_name where (c=1);
```

## 贴一个伴鱼的TiDB选型心路历程
[伴鱼TiDB](https://zhuanlan.zhihu.com/p/164706527)
