### 多表查询

####  联合查询

1. 通过联合查询，可以得到两张表中记录的集合或者公共记录的集合，或者其中某张表中的记录的集合

2. 联合查询以行为单位对表进行操作，主要是进行行数的增减

3. 作为联合查询的多表之间的列数、以及列数的类型必须相同（例如：表1查询哪些列，表2就查询哪些列）

4. 联合查询默认会去除重复的记录

5. 联合查询可以使用任何SELECT语句，但是ORDER BY子句只能在最后一次使用

##### union

返回表A查询和表B查询的并集（并进行去重处理）

```sql
select name,age from staff1 where origo='重庆' union select name,age from user;  #查询表1 origo为重庆的name，age字段的结果 加上表2查询name，age的结果，并进行去重处理（要求两表的name和age数据类型一致）
```

##### union all

相比于Union查询，此查询可以对合并的数据不进行去重

```sql
select name,age from staff1 where origo='重庆' union all select name,age from user; #没有进行去重处理
```

##### intersect

将两张表的某些字段的公共记录提取出来（交集），mysql无法使用

##### expect

将两张表的某些字段的公共记录减去，然后返回第一张表的剩余记录，mysql无法使用，可以使用`not in`实现

```sql
select name,age from staff1 where (name,age) not in (select name,age from user);  #查询表1 与 表2 的差集（将表1的查询结果减去表1与表2的交集）
```

#### 连接查询

**交叉连接(CROSS JOIN)**：是对两个或者多个表进行笛卡儿积操作，所谓笛卡儿积就是关系代数里的一个概念，表示两个表中的每一行数据任意组合的结果。比如：有两个表，左表有m条数据记录，x个字段，右表有n条数据记录，y个字段，则执行交叉连接后将返回m*n条数据记录，x+y个字段。

```sql
select * 
from emp
cross join dept
```

**自然连接(natural join)**：natural join等同于inner join或inner using，其作用是将两个表中具有相同名称的列进行匹配。处理大型数据集最好使用自定义条件，不然效率低。自然连接依赖于两个表之间存在相同的列，并且这些列的数据类型和数据内容也必须相同。

**内连接(inner join)**：

可以选取出同时存在于两张表中的数据，即两张表的交集数据。

```sql
select * 
from emp e
inner join dept d
using(deptno); --关联的字段，必须是同名的

select * 
from emp e
inner join dept d
on (e.deptno = d.deptno);
```

外连接

**左外连接(left join)**：

SELECT 字段列表 FROM 表1 LEFT [OUTER] JOIN 表2 ON 条件列表;

查询表1的所有数据，其中包含表1和表2交集部分的数据

**右外连接(right join)**：

SELECT 字段列表 FROM 表1 RIGHT [OUTER] JOIN 表2 ON 条件列表;

查询表2的所有数据包含表1和表2交集部分的数据

**全外连接(full outer join)**：

只要左表（table1）和右表（table2）其中一个表中存在匹配，则返回行，结合了 LEFT JOIN 和 RIGHT JOIN 的结果。

#### 子查询

不相关子查询：

一条SQL语句含有多个select，先执行子查询，再执行外查询，子查询可以独立运行，称为不相关子查询，根据子查询的结果行数，可以分为单行子查询和多行子查询。

```sql
select * from emp where sal > (select sal from emp where ename = 'CLARK');

select * from emp where (sal,dept) = any (select sal,dept from emp where ename = 'CLARK');
```

相关子查询：

子查询不可以独立运行，并且先运行外查询，再运行子查询。

```sql
select * from emp e where sal >= (select avg(sal) from emp e2 where e2.job = e.job)
```

