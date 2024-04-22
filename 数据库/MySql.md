## 事务

### 事务及其特征

数据库中的事务是指对数据库执行一批操作，在同一个事务当中，这些操作最终要么全部执行成功，要么全部失败，不会存在部分成功的情况。

- 事务是一个原子操作。是一个最小执行单元。可以甶一个或多个SQL语句组成
- 在同一个事务当中，所有的SQL语句都成功执行时，整 个事务成功，有一个SQL语句执行失败，整个事务都执行失败。

比如A用户给B用户转账100操作，过程如下：

1. 从A账户扣100
2. 给B账户加100

如果在事务的支持下，上面最终只有2种结果：

1. 操作成功：A账户减少100；B账户增加100
2. 操作失败：A、B两个账户都没有发生变化

目前mysql常用的存储引擎有InnoDB（MySQL5.5以后默认的存储引擎）和MyISAM（MySQL5.5之前默认的存储引擎），其中InnoDB支持事务处理机制，而MyISAM不支持。

#### 特征

一个操作序列要成为事务，必须满足事务的原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation）和持久性（Durability）。这四个特性简称为ACID特性。

* 原子性(Atomicity)：事务的整个过程如原子操作一样，最终要么全部成功，或者全部失败，从最终结果来看这个过程是不可分割的。
* 一致性(Consistency)：一个事务必须使数据库从一个一致性状态变换到另一个一致性状态。所谓一致性，指的是数据处于一种有意义的状态，这种状态是**语义上**的而不是**语法上**的。最常见的例子是转帐。例如从帐户A转一笔钱到帐户B上，如果帐户A上的钱减少了，而帐户B上的钱却没有增加，那么我们认为此时数据处于不一致的状态。
* 隔离性(Isolation)：一个事务的执行不能被其他事务干扰。即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰。
* 持久性(Durability)：一个事务一旦提交，他对数据库中数据的改变就应该是永久性的。当事务提交之后，数据会持久化到硬盘，修改是永久性的。

mysql中事务默认是隐式事务，执行insert、update、delete操作的时候，数据库自动开启事务、提交或回滚事务。

是否开启隐式事务是由变量autocommit控制的。

所以事务分为**隐式事务**和**显式事务**。

#### 隐式事务

事务自动开启、提交或回滚，比如insert、update、delete语句，事务的开启、提交或回滚由mysql内部自动控制的。

查看变量autocommit是否开启了自动提交

```sql
mysql> show variables like 'autocommit';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| autocommit   | ON   |
+---------------+-------+
1 row in set, 1 warning (0.00 sec)
```

autocommit为ON表示开启了自动提交。

#### 显式事务

事务需要手动开启、提交或回滚，由开发者自己控制。

2种方式手动控制事务：

方法一：

```sql
//设置不自动提交事务
set autocommit=0;
//执行事务操作
commit|rollback;
```

方法二：

```sql
start transaction;//开启事务
//执行事务操作
commit|rollback;
```

#### savepoint关键字

我们可以将一大批操作分为几个部分，然后指定回滚某个部分。可以使用savepoin来实现，效果如下：

```sql
mysql> start transaction;
Query OK, 0 rows affected (0.00 sec)

mysql> insert into test1 values (1);
Query OK, 1 row affected (0.00 sec)

mysql> savepoint part1;//设置一个保存点
Query OK, 0 rows affected (0.00 sec)

mysql> insert into test1 values (2);
Query OK, 1 row affected (0.00 sec)

mysql> rollback to part1;//将savepint = part1的语句到当前语句之间所有的操作回滚
Query OK, 0 rows affected (0.00 sec)

mysql> commit;//提交事务
Query OK, 0 rows affected (0.00 sec)

mysql> select * from test1;
+------+
| a   |
+------+
|   1 |
+------+
1 row in set (0.00 sec)
```

从上面可以看出，执行了2次插入操作，最后只插入了1条数据。

savepoint需要结合rollback to sp1一起使用，可以将保存点sp1到rollback to sp1之间的操作回滚掉。

#### 只读事务

表示在事务中执行的是一些只读操作，如查询，但是不会做insert、update、delete操作，数据库内部对只读事务可能会有一些性能上的优化。

```sql
start transaction read only;
```

只读事务中执行insert、delete等sql会报错。

### 事务并发问题

#### 更新丢失

丢失更新就是两个不同的事务（或者Java程序线程）在某一时刻对同一数据进行读取后，先后进行修改。导致第一次操作数据丢失。

第一类丢失更新 ：A，B 事务同时操作同一数据，A先对改数据进行了更改，B再次更改时失败然后回滚，把A更新的数据也回滚了。（事务撤销造成的撤销丢失）

第二类丢失更新：A，B 事务同时操作同一数据，A先对改数据进行了更改，B再次更改并且提交，把A提交的数据给覆盖了。（事务提交造成的覆盖丢失）

#### 脏读

一个事务在执行的过程中读取到了其他事务还没有提交的数据。 这个还是比较好理解的。

两个事务同时操作同一数据，A事务对该数据进行了修改还没提交的时候，B事务访问了该条事务，并且使用了该数据，此时A事务回滚，那么B事务读到的就是脏数据。

比如事务1，修改了某个数据 事务2，刚好访问了事务1修改后的数据

此时事务1，回滚了操作 事务2，读到还是回滚前的数据

#### 不可重复读

在同一事务中，多次读取同一数据返回的结果有所不同，换句话说，后续读取可以读到另一事务已提交的更新数据。

这种情况发生 在一个事务内多次读同一数据。A事务查询某条数据，该事务未结束时，B事务也访问同一数据并进行了修改。那么在A事务中的两 次读数据之间，由于第二个事务的修改，那么第一个事务两次读到的的数据可能是不一样的。

事务1，查询某个数据 事务2，修改了某个数据，提交

事务1，再次查询这个数据

这样事务1两次查询的数据不一样，称为不可重复读

#### 幻读

幻读与不可重复读类似。它发生在一个事务（T1）读取了几行数据，接着另一个并发事务（T2）插入了一些数据时。在随后的查询中，第一个事务（T1）就会发现多了一些原本不存在的记录，就好像发生了幻觉一样，所以称为幻读。

不可重复读的重点是修改属性，幻读的重点在于新增或者删除。

解决不可重复读的问题只需锁住满足条件的行，解决幻读需要锁表。

### 事务隔离级别

事务隔离级别主要是解决了上面多个事务之间数据可见性及数据正确性的问题。

隔离级别分为以下几种：

1. **读未提交：READ-UNCOMMITTED**
2. **读已提交：READ-COMMITTED**
3. **可重复读：REPEATABLE-READ**
4. **串行：SERIALIZABLE**

上面4中隔离级别越来越强，会导致数据库的并发性也越来越低。

mysql默认隔离级别为`repeatable read`，通过命令调整隔离级别：

```sql
--查看隔离级别
select @@transaction_isolation
-- 设置事务的隔离级别   （设置当前会话的隔离级别）
set session transaction isolation level read uncommitted;  
set session transaction isolation level read committed;  
set session transaction isolation level repeatable read;  
set session transaction isolation level serializable; 
```

**隔离级别会出现的问题**

| 隔离级别         | 脏读可能性 | 不可重复读可能性 | 幻读可能性 |
| ---------------- | ---------- | ---------------- | ---------- |
| READ-UNCOMMITTED | 有         | 有               | 有         |
| READ-COMMITTED   | 无         | 有               | 有         |
| REPEATABLE-READ  | 无         | 无               | 有         |
| SERIALIZABLE     | 无         | 无               | 无         |

**关于隔离级别的选择**

1. 隔离级别越高，并发性也低，比如最高级别SERIALIZABLE会让事物串行执行，并发操作变成串行了，会导致系统性能直接降低。
2. 读已提交（READ-COMMITTED）通常用的比较多。