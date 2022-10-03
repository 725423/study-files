# MySQL随笔

### MyISAM 和 InnoDB 的区别是什么

- **1.是否支持行级锁**

  **MyISAM 只有表级锁**(table-level locking)，而 **InnoDB 支持行级锁(row-level locking)和表级锁**,默认为行级锁。

  也就说，MyISAM 一锁就是锁住了整张表，这在并发写的情况下是多么滴憨憨啊！这也是为什么 InnoDB 在并发写的时候，性能更牛皮了！

  **2.是否支持事务**

  MyISAM 不提供事务支持。

  InnoDB 提供事务支持，实现了 SQL 标准定义了四个隔离级别，具有提交(commit)和回滚(rollback)事务的能力。并且，InnoDB  默认使用的 REPEATABLE-READ（可重读）隔离级别是可以解决幻读问题发生的（基于 MVCC 和 Next-Key Lock）。

  关于 MySQL 事务的详细介绍，可以看看我写的这篇文章：[MySQL 事务隔离级别详解](https://javaguide.cn/database/mysql/transaction-isolation-level.html)

  **3.是否支持外键**

  MyISAM 不支持，而 InnoDB 支持。

  外键对于维护数据一致性非常有帮助，但是对性能有一定的损耗。因此，通常情况下，我们是不建议在实际生产项目中使用外键的，在业务代码中进行约束即可！

  **4.是否支持数据库异常崩溃后的安全恢复**

  MyISAM 不支持，而 InnoDB 支持。

  使用 InnoDB 的数据库在异常崩溃后，数据库重新启动的时候会保证数据库恢复到崩溃前的状态。这个恢复的过程依赖于 `redo log` 

  **5.是否支持 MVCC**

  MyISAM 不支持，而 InnoDB 支持。

  讲真，这个对比有点废话，毕竟 MyISAM 连行级锁都不支持。MVCC 可以看作是行级锁的一个升级，可以有效减少加锁操作，提高性能。

  **6.索引实现不一样。**

  虽然 MyISAM 引擎和 InnoDB 引擎都是使用 B+Tree 作为索引结构，但是两者的实现方式不太一样。

  InnoDB 引擎中，其数据文件本身就是索引文件。相比 MyISAM，索引文件和数据文件是分离的，其表数据文件本身就是按 B+Tree 组织的一个索引结构，树的叶节点 data 域保存了完整的数据记录



### 事务的特性

- 原子性、一致性、隔离性、持久性



### redo log

- `redo log`（重做日志）是`InnoDB`存储引擎独有的，它让`MySQL`拥有了崩溃恢复能力
- 比如`MySQL`实例挂了或宕机了，重启时，`InnoDB`存储引擎会使用`redo log`恢复数据，保证数据的持久性与完整性
- ![img](https://pic1.zhimg.com/80/v2-db16779bfbde98a7dfc9b3310c0ad35c_720w.jpg)



### 并发事务带来了哪些问题

- **脏读（Dirty read）:** 当一个事务正在访问数据并且对数据进行了修改，而这种修改还没有提交到数据库中，这时另外一个事务也访问了这个数据，然后使用了这个数据。因为这个数据是还没有提交的数据，那么另外一个事务读到的这个数据是“脏数据”，依据“脏数据”所做的操作可能是不正确的。
- **丢失修改（Lost to modify）:**  指在一个事务读取一个数据时，另外一个事务也访问了该数据，那么在第一个事务中修改了这个数据后，第二个事务也修改了这个数据。这样第一个事务内的修改结果就被丢失，因此称为丢失修改。 例如：事务 1 读取某表中的数据 A=20，事务 2 也读取 A=20，事务 1 修改 A=A-1，事务 2 也修改 A=A-1，最终结果  A=19，事务 1 的修改被丢失。
- **不可重复读（Unrepeatable read）:** 指在一个事务内多次读同一数据。在这个事务还没有结束时，另一个事务也访问该数据。那么，在第一个事务中的两次读数据之间，由于第二个事务的修改导致第一个事务两次读取的数据可能不太一样。这就发生了在一个事务内两次读到的数据是不一样的情况，因此称为不可重复读。
- **幻读（Phantom read）:** 幻读与不可重复读类似。它发生在一个事务（T1）读取了几行数据，接着另一个并发事务（T2）插入了一些数据时。在随后的查询中，第一个事务（T1）就会发现多了一些原本不存在的记录，就好像发生了幻觉一样，所以称为幻读。



### 查看事务的隔离级别

- select @@transaction_isolation  
- show variables like '%transaction_isolation%' 
- select @@global.transaction_isolation  查找全局事务隔离级别
- select @@session.transaction_isolation 查找当前session事务隔离级别



### 修改事务隔离级别

- SET GLOBAL TRANSACTION ISOLATION LEVEL REPEATABLE READ 修改全局事务隔离级别
- SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ 修改当前session事务隔离级别



### MySQL隔离级别是基于什么实现的

- MySQL 的隔离级别基于**锁和 MVCC 机制共同实现**的。

  SERIALIZABLE 隔离级别，是通过**锁**来实现的。除了 SERIALIZABLE 隔离级别，**其他的隔离级别都是基于 MVCC 实现**。

  不过， SERIALIZABLE 之外的**其他隔离级别可能也需要用到锁机制**，就比如 REPEATABLE-READ 在当前读情况下需要使用加锁读来保证不会出现幻读



### 表级锁和行级锁对比

- **表级锁：** MySQL 中锁定粒度最大的一种锁，是针对非索引字段加的锁，对当前操作的整张表加锁，实现简单，资源消耗也比较少，加锁快，不会出现死锁。其锁定粒度最大，触发锁冲突的概率最高，并发度最低，MyISAM 和 InnoDB 引擎都支持表级锁。
- **行级锁：** MySQL 中锁定粒度最小的一种锁，是针对索引字段加的锁，只针对当前操作的行记录进行加锁。 行级锁能大大减少数据库操作的冲突。其加锁粒度最小，并发度高，但加锁的开销也最大，加锁慢，会出现死锁。



### 行级锁使用的注意事项

- InnoDB 的**行锁是针对索引字段加的锁**，**表级锁是针对非索引字段加的锁**。当我们执行 `UPDATE`、`DELETE` 语句时，如果 `WHERE`条件中字段没有命中唯一索引或者索引失效的话，就会导致扫描全表对表中的所有行记录进行加锁



![image-20220917201043855](C:\Users\22906\AppData\Roaming\Typora\typora-user-images\image-20220917201043855.png)