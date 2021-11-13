## 索引
### 创建索引
1. 使用alter
```Mysql
alter table `table_name` add index `idx_nam` (`id`,`name`);
alter table `table_name` add unique `idx_name` (`id`, `name`);
alter table `table_name` add primary key `idx_name` (`id`);
```
2. 使用create index
```Mysql
create index `idx_name` on `table_name` (`id`, `name`);
create unique `idx_name` on `table_name` (`id`, `name`);
```
### 删除索引
```Mysql
drop index `index_name` on `table_name`;
alter table `table_name` drop index `index_name`;
```
### b树和b+树区别
b树是每一层的节点都会存储数据,b+树是只有最底层的叶子结点存储数据,这样普通节点可以放下更多子节点,并且子节点之间用指针连接起来,方便更快的范围查找.

## 同步
1. 异步同步.默认的复制是异步的,主库在执行完客户端的请求后会立即返回,不关心从库.这里就会导致如果此时主库crash,那从库是没有数据的,导致数据不完整.
2. 全同步复制.客户端请求上来后,主库首先执行完,所有的从库都执行完这个事务才返回给客户端,性能很差.
3. 半同步复制.客户端请求后,主库先写完,等待一个从库写入后收到relay log才返回给客户端,有一定程度延迟.

## binlog
### 格式
1. statement.  
每一条修改数据的sql都会被记录在binlog中,从服务器复制的时候会执行相同的sql.
* 优点:不用记录每一行的变化,减少了日志数量,节约io,提高性能.
* 缺点:主从同步一般不建议使用statement,有些语句会不支持.
2. ROW.  
不记录sql的上下文信息,只保存哪条记录被修改,而后在从服务器也做同样的修改.  
* 优点:只记录哪条被修改成什么,row的日志内容会很清楚的记下每行数据修改的细节.
* 缺点:数据量太大
3. MIXED.
结合了Row和statement的优点,同时binlog结构也更复杂.一般的语句使用statement来保存,如果statement无法保存(如一些函数),那会使用row格式.从服务器同步时也会采用相应的策略.

## 数据库的隔离级别
这个正确性存疑.
|隔离级别|导致问题|读操作|写操作|
|----|----|----|----|
|读未提交|脏读|不加锁|行级共享锁,事务结束后释放|
|读已提交|不可重复读|行级共享锁,执行完立即释放|行级互斥锁,事务结束后释放|
|可重复读|幻读|行级共享锁,事务结束后释放|行级互斥锁,事务结束后释放|
|串行化||表级共享锁,事务结束后释放|表级互斥锁,事务结束后释放|

## 锁
### 共享锁
也叫读锁,别的事务可以读,但不能写.
```Mysql
SELECT ... LOCK IN SHARE MODE;
```
### 排他锁
写锁,其他事务既不能读也不能写.
Mysql的InnoDB引擎会为insert,update,delete操作中自动加排他锁.
```Mysql
SELECT ... FOR UPDATE;
```

## MVCC
MVCC只在读已提交和可重复读两个隔离级别下工作.依赖隐藏字段,Read View,Undo log.
### 隐藏字段
InnoDB引擎在每行数据后添加三个隐藏字段:
1. DB_TRX_ID(6字节),记录最近一次对本记录做出修改的事务ID. 将delete认为update,会在新的删除位上表示为deleted.
2. DB_ROLL_PTR(7字节),回滚指针,指向记录行的undo log信息.
3. DB_ROW_ID(6字节),单调递增ID.当没有主键或唯一非空索引,会使用这个id来产生聚集索引.
### Read View
读视图,和快照一个概念,保存了对本事务不可见的其他活跃事务.  
在事务开始时创建.
1. low_limit_id: 目前出现的最大事务id+1,也就是下一个事务id.
2. up_limit_id: 活跃事务列表(trx_ids)中的最小事务id.
3. trx_ids: read view创建时其他未提交的活跃事务id列表.
4. creator_trx_id: 当前创建事务的id,是一个递增的编号.
### Undo log
事务的老版本数据.  
如果是insert,那undolog只会在事务回滚时用到,提交事务后就可以丢弃;如果是update,那除了事务回滚,还在快照读中需要用到,只有快照中不涉及该日志才会被删除.  
事务对一行数据每做一次修改,就会更改隐藏字段里的DB_TRx_ID和DB_ROLL_PTR,回滚指针会指向undolog中的版本,undolog中各个版本用链表串起来,链首是最新记录,链尾是最旧的.
### 可重复读
事务中select时会通过read view到undolog里去找自己开始的版本.
### 可重复读和读已提交区别
读已提交会在别的事务提交后再次查询时重新生成readview, 这样已提交的就是旧版本,就会读到已提交的内容.可重复读的readview是一直是第一次生成的readview,所以一直读到的是最开始的数据.


## 事务的四大特性如何实现
一致性依赖其他三种特性:原子性,隔离性,持久性.
* 原子性: innodb使用undo log,记录事务操作便于回滚.
* 隔离型: 使用锁机制.
* 持久性: 直接落盘的话IO消耗严重,使用redo log来解决持久性和IO消耗严重的问题.

## 主从同步
### 普通的主从复制
一共需要三个线程,主服务器上一个,从服务器上两个.   
从服务器创建一个io线程,连接主服务器让其发送binlog中的语句;主服务器创建一个线程将binlog中的内容发给从服务器;从服务器的io线程读取到log内容后将数据拷贝到从服务器本地文件中,即中继日志;从服务器创建一个sql线程执行中继日志中包含的更新.
### 异步和半同步
如果没说明,一般情况下都是异步,主库宕机后可能会丢失日志.  
半同步复制是指binlog写入完成后,等待一个从库接收到并写入从库relaylog后,才返回commit操作给客户端,就可以保证在主库宕机情况下至少有一个从库保有记录.

## drop|truncate|delete
* drop删除表数据和表结构.速度最快.
* truncate删除所有表数据,清空表.速度其次.
* delete删除某一行,会记录在日志中方便回滚.速度较慢.
