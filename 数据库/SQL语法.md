# 语法结构

![[Pasted image 20240615185936.png]]

## 具体结构

1. 子句：语句和查询的组成成分
2. 表达式：产生任何标量值，或由列和行的数据库表
3. 谓词：是一个计算结果为 TRUE、FALSE 或 UNKNOWN 的表达式
4. 查询：基于特定条件检索数据
5. 语句：持久地影响纲要和数据，也可以控制数据库事务、程序流程、连接、会话或诊断。

# 语法要点
	语句不区分大小写
	多条语句使用分号分隔
	处理语句时忽略空格

三种注释方式
```
##
--
/*  */
```

## CRUD

```sql
# 修改

# 插入一行
INSERT INTO user
VALUES (10, 'root', 'root', 'xxxx@163.com');
# 插入多行
INSERT INTO user
VALUES (10, 'root', 'root', 'xxxx@163.com'), (12, 'user1', 'user1', 'xxxx@163.com'), (18, 'user2', 'user2', 'xxxx@163.com');
# 插入行的一部分
INSERT INTO user(username, password, email)
VALUES ('admin', 'admin', 'xxxx@163.com');

# 更新数据
UPDATE user
SET username='robot', password='robot'
WHERE username = 'root';

# 删除表中指定数据
DELETE FROM user
WHERE username = 'robot';

# 清空表中数据
TRUNCATE TABLE user;

# 查询

# 查询单列
SELECT prod_name
FROM products;

# 查询多列
SELECT prod_id, prod_name, prod_price
FROM products;

# 查询所有列
SELECT *
FROM products;

# 查询不同值
SELECT DISTINCT
vend_id FROM products;

# 限制查询结果
-- 返回前 5 行
SELECT * FROM mytable LIMIT 5;
SELECT * FROM mytable LIMIT 0, 5;
-- 返回第 3 ~ 5 行
SELECT * FROM mytable LIMIT 2, 3; /* 第一个参数是输出记录的初始位置，第二个参数偏移量，偏移多少，输出的条目就是多少。*/
SELECT * FROM ids LIMIT 5 OFFSET 2/* 第一个参数是偏移量，第二个参数为起始行数。*/

# 排序 A升 D降
SELECT * FROM products
ORDER BY prod_price DESC, prod_name ASC;

# 分组
SELECT cust_name, COUNT(cust_address) AS addr_num
FROM Customers GROUP BY cust_name;
## 再排序
SELECT cust_name, COUNT(cust_address) AS addr_num
FROM Customers GROUP BY cust_name
ORDER BY cust_name DESC;
## 使用having过滤数据
## 过滤分组，一般都是和 group by 连用，不能单独使用。having 在 group by 之后。
SELECT cust_name, COUNT(*) AS num
FROM Customers
WHERE cust_email IS NOT NULL
GROUP BY cust_name
HAVING COUNT(*) >= 1;
```

## 子查询
	MYSQL 数据库从 4.1 版本才开始支持子查询，早期版本是不支持的。


```sql

# where 子查询

select column_name [, column_name ]
from   table1 [, table2 ]
where  column_name operator
    (select column_name [, column_name ]
    from table1 [, table2 ]
    [where])

# from 子查询
select column_name [, column_name ]
from (select column_name [, column_name ]
      from table1 [, table2 ]
      [where]) as temp_table_name
where  condition

```

## WHERE
	WHERE 子句用于过滤记录，即缩小访问数据的范围。
	WHERE 后跟一个返回 true 或 false 的条件。
	WHERE 可以与 SELECT，UPDATE 和 DELETE 一起使用。
	可以在 WHERE 子句中使用的操作符。

**BETWEEN	在某个范围内**
**LIKE	            搜索某种模式**
**IN	            指定针对某个列的多个可能值**

#### AND、OR、NOT 
	是用于对过滤条件的逻辑处理指令。AND 优先级高于 OR，为了明确处理顺序，可以使用 ()。

AND 操作符表示左右条件都要满足。
OR 操作符表示左右条件满足任意一个即可。
NOT 操作符用于否定一个条件。

### LIKE
	作用是确定字符串是否匹配模式。
	不要滥用通配符，通配符位于开头处匹配会非常慢。

**% 表示任何字符出现任意次数。**
**_ 表示任何字符出现一次。**
```sql
SELECT prod_id, prod_name, prod_price
FROM products
WHERE prod_name LIKE '%bean bag%';

SELECT prod_id, prod_name, prod_price
FROM products
WHERE prod_name LIKE '__ inch teddy bear';
```

## 连接
	JOIN 子句用于将两个或者多个表联合起来进行查询。
	本质就是将不同表的记录合并起来，形成一张新表。当然，这张新表只是临时的，它仅存在于本次查询期间。

```sql
select table1.column1, table2.column2...
from table1
join table2
on table1.common_column1 = table2.common_column2;
```
### ON 和 WHERE 区别

连接表时，SQL 会根据连接条件生成一张新的临时表。
ON 就是连接条件，它**决定临时表的生成**
WHERE 是**在临时表生成以后**，**再对临时表中的数据进行过滤**，生成最终的结果集，这个时候已经没有 JOIN-ON 了。

### 连接修饰关键词

1. INNER JOIN ：内连接（默认连接方式）只有当两个表都存在满足条件的记录时才会返回行。
2. LEFT JOIN / LEFT OUTER JOIN ：左(外)连接返回左表中的所有行，即使右表中没有满足条件的行也是如此。
3. RIGHT JOIN / RIGHT OUTER JOIN 右(外)连接返回右表中的所有行，即使左表中没有满足条件的行也是如此。
4. FULL JOIN / FULL OUTER JOIN ：全(外)连接只要其中有一个表存在满足条件的记录，就返回行。
5. SELF JOIN：将一个表连接到自身，就像该表是两个表一样。为了区分两个表，在 SQL 语句中需要至少重命名一个表。
6. CROSS JOIN：交叉连接，从两个或者多个连接表中返回记录集的笛卡尔积。
![[Pasted image 20240615195508.png]]

### 显式内连接和隐式内连接

```sql
# 隐式内连接
select c.cust_name, o.order_num
from Customers c, Orders o
where c.cust_id = o.cust_id
order by c.cust_name;

# 显式内连接
select c.cust_name, o.order_num
from Customers c inner join Orders o
using(cust_id)
order by c.cust_name;

```

## 组合
	将两个或更多查询的结果组合起来，并生成一个结果集


基本规则：

1. 所有查询的列数和列顺序必须相同。
2. 每个查询中涉及表的列的数据类型必须相同或兼容。
3. 通常返回的列名取自第一个查询。










