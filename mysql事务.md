# mysql事务

标签（空格分隔）： mysql

---

## 事务隔离级别
读未提交（read uncommitted）：其他事务做的变更还未提交，事务就可以看该变更
读提交（read committed）：在其他事务提交之后，才能看到其他事务做的变更
可重复读（repeatable read）：该事务启动时读到的数据是什么就是什么
串行读（serializable）：需要等前一个事务执行完

隔离级别配置参数：

mysql> show variables like 'transaction_isolation';

+-----------------------+----------------+

| Variable_name | Value |

+-----------------------+----------------+

| transaction_isolation | READ-COMMITTED |

+-----------------------+----------------+

避免长事务：
	1. Begin或start transition和commit或者rollback配套使用
	2. 使用set autocommit=1来自动提交事务
	3. 在需要频繁使用事务的情况下，autocommit = 1的情况下使用commit work and chain来自动开启下一个事务
	4. select * from information_schema.innodb_trx where TIME_TO_SEC(timediff(now(),trx_started))>60
可以查询执行时间超过60s的事务。


## 不同事务隔离级别可能导致的问题

| 事务隔离级别 | 脏读 | 不可重复读 | 幻读 |
| :-----| ----: | :----: |:----:|
| 读未提交（read-uncommitted）| 是 | 是 | 是|
| 读已提交（read-committed） | 否 | 是 |是|
| 可重复读（repeatable-read） | 否 | 否 |是|
| 串行化（serializable） | 否 | 否 |否|

脏读：事务A读到了事务B修改的数据，事务B回滚之后，数据恢复，这时事务A读到的是脏数据。
不可重复读：事务A开始读到了一个数据C1，这时事务B将数据改为C2，事务A重新读该数据，事务A读到的数据被事务B修改为C2，两次读取的数据不一致，表示不可重复读取。
幻读：事务A第一次读取数据没有拿到C1，这是事务B插入一个C1的数据，事务A插入C1的数据时发现数据冲突，表示事务A读到了幻读数据。

## 多版本并发控制（MVCC）
mvcc解决了幻读的问题
原理：
保存数据在某个时间点的快照来实现。
innodb会为每个新插入的行保存当前系统版本号作为该数据的版本号：
|id	|name|	创建时间(事务ID)|	删除时间(事务ID)|
| :-----| :----: | :----: |:----:|
|1	|zhang|	1	|undefined|
|2	|gao|	1	|undefined|
|3	|li	|2	|undefined|

一个事务都会有一个全局的事务id，在查询一行数据时会对比该数据的版本号和当前事务的版本号，如果创建时间（事务ID）小于或等于该事务版本号，该数据才会被看到，如果删除时间（事务ID）大于或等于该事务版本号，或者没有删除时间，则该数据会被看到。

