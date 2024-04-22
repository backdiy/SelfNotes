## 元数据查询

### 1.统计数据库大小

* 单个数据库的大小

```sql
select pg_size_pretty(pg_database_size('test_database')) AS database_size;
```

* 所有数据库的大小

```sql
select datname, pg_size_pretty (pg_database_size(datname)) AS database_size from pg_database;
```

### 2.统计数据表大小

* 单个表大小

```sql
select pg_size_pretty(pg_relation_size('test_table')) AS table_size;
```

* 查询单个表的总大小，包括该表的索引大小

```sql
select pg_size_pretty(pg_total_relation_size('test_table')) AS table_size;
```

* 所有表大小

```sql
select relname,pg_size_pretty(pg_relation_size(relid)) 
from pg_stat_user_tables 
order by pg_relation_size(relid) desc;
```

## 常用函数

### 字符串相关

#### 截取字符串

```sql
-- 字符串被分成3部分，取最后一部分，那最后一个参数就是3
select split_part('aa-bb-cc' ,'-', 3)

-- 获取最后一部分内容
select split_part('aa-bb-cc' ,'-', (length('aa-bb-cc') - length(replace('aa-bb-cc','-','')) + 1)); -- result:cc
-- 获取第一部分的内容
select SUBSTRING('aa-bb-cc' ,1,position('-' in 'aa-bb-cc') - 1); -- result:aa
-- 补充：SUBSTRING函数下标从1开始
select SUBSTRING('abcd',2); -- result:bcd 表示从下标从2开始截取到末尾
select SUBSTRING('abcd',1,2); -- result:ab 表示从下标从1开始,截取2个字符
```

#### 查找子字符串

1. 函数position(substring in string)

参数一：目标字符串；

参数二：原字符串；

如果包含目标字符串，会返回目标字符串笫一次出现的位置，可以根据返回值是否大于0来判断是否包含目标字符串。

```sql
select position('aa' in 'abcd');
 position 
----------
    0
select position('ab' in 'abcd');
 position 
----------
    1
select position('ab' in 'abcdab');
 position 
----------
    1
```

2. 函数strpos(string, substring)

参数一：原字符串；

参数二：目标字符串；

声明子串的位置，作用与position函数一致。

```sql
select strpos('abcd','aa');
 position 
----------
    0
 
select strpos('abcd','ab');
 position 
----------
    1
 
select strpos('abcdab','ab');
 position 
----------
    1
```

3. 使用正则表达式

如果包含目标字符串返回`t`，不包含返回`f`。

```sql
select 'abcd' ~ 'aa' as result;
result
------
  f 
    
select 'abcd' ~ 'ab' as result;
result
------
  t 
    
select 'abcdab' ~ 'ab' as result;
result
------
  t
```

4. 使用数组的@>操作符（不能准确判断是否包含）

```sql
select regexp_split_to_array('abcd','') @> array['b','e'] as result;
result
------
 f
 
select regexp_split_to_array('abcd','') @> array['a','b'] as result;
result
------
 t
```

注意下面这些例子：

```sql
select regexp_split_to_array('abcd','') @> array['a','a'] as result;
result
----------
 t
 
select regexp_split_to_array('abcd','') @> array['a','c'] as result;
result
----------
 t
 
select regexp_split_to_array('abcd','') @> array['a','c','a','c'] as result;
result
----------
 t
```

可以看出，数组的包含操作符判断的时候不管顺序、是否重复，只要包含了就返回true，在真正使用的时候注意。

#### 拼接字符串

1. 使用`||`符号

使用||运算符也可以合并两个或多个字符串，也可以合并非字符串。

```sql
SELECT 'Hello' || ' ' || 'World';
> Hello World

SELECT 'Hello' || 1 || 'World';
> Hello1World
```

2. 使用concat函数

使用concat()函数可以合并两个或多个字符串。

```sql
SELECT concat('Hello', ' ', 'World');
> Hello World
```

3. 使用concat_ws函数

使用concat_ws()函数可以合并多个字符串，并通过指定分隔符来分隔这些字符串。

```sql
SELECT CONCAT_WS(', ', 'apple', 'banana', 'orange');
> apple, banana, orange
```

4. 使用format()函数

使用format()函数可以格式化字符串，并将多个字符串合并为一个字符串。

```sql
SELECT format('%s %s world', 'hello', 'cruel');
> hello cruel world
```

#### 补充字符串

1. 使用lpad()函数

PostgreSQL中的`lpad()`函数有两个功能：

`lpad(string,length[,fill_text])`

1. 如果长度不够指定的长度，就在左边填充字符串
2. 如果长度超出了指定的长度，就把右边截掉。

```sql
SELECT lpad('esource', 10, 'w3r');
--  w3resource
SELECT lpad('esource', 13, 'w3r');
--  w3rw3resource
SELECT lpad('w3resource', 8, 'lpad');
--  w3resour
```

### 数学相关

#### 判断奇偶

1. 使用mod()

mod(a,b) 在sql中的意思是 a / b 的余数
mod(id, 2)=1 是指id是奇数。
mod(id, 2)=0 是指id是偶数。

```sql
select * from employees 
where mod(emp_no, 2) = 1 and last_name <> 'Mary' 
order by hire_date desc;
```

2. 使用%

```sql
如id是奇数：(id % 2) = 1;
id是偶数：(id % 2) = 0;
```

3. 使用位运算

如id是奇数：(id & 1) = 1;
id是偶数：(id & 1) = 0;

```sql
select * from employees 
where emp_no & 1 = 1 and last_name <> 'Mary' 
order by hire_date desc;
```

判断偶数也可以使用如 `id = (id >> 1 << 1)`，先除2，再乘2，和原来的值相等就是偶数，不等就是奇数；
