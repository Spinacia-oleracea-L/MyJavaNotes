# 外连接查询

多表查询中，出现A表中的数据B中链接后显现为null。需求中要求把**空的数据**以**NULL**显现，需要考虑Outer Join外连接。

```sql
	select FirstName, LastName, City, State from Person
		left join Address on Address.Personid = Person.Personid;
```

补充：

* A inner join B：取交集
* A left join B：取A全部，B没有对应的值，则为null
* A right join B：取B全部，A没有对应的值，则为null
* A full outer join B：取并集，彼此没有对应的值为null

对应条件，在on后填写。

# 临时表查询

用于**某个类别中**的最高价商品，或**第二高**价商品，采用临时表查询

```sql
select * from (
    # 临时表
    select column1, column2..., from tableName group by column1
) as tempName, tableName2
where tempName.column1 = tableName2.column1
and ...;
```

例题：

表: `Employee`

```
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| salary      | int  |
+-------------+------+
在 SQL 中，id 是该表的主键。
该表的每一行都包含有关员工工资的信息。
```

查询 `Employee` 表中第 `n` 高的工资。如果没有第 `n` 个最高工资，查询结果应该为 `null` 。

查询结果格式如下所示。

```
输入: 
Employee table:
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
n = 2
输出: 
+------------------------+
| getNthHighestSalary(2) |
+------------------------+
| 200                    |
+------------------------+
```

```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
    set N = N - 1;
  RETURN (
      # Write your MySQL query statement below.
      select 
        (select DISTINCT salary from Employee
            order by salary desc
            limit 1 offset N )
        AS getNthHighestSalary     
  );
END
```

# 名次排序查询

方式一：

```sql
select a.score as score,(
    # 条件一：总表中，大于等于当前成绩的总个数
    select count(distinct b.score) from scores as b where b.score >= a.score
) as `rank` from scores as a
order by a.score DESC;
```

方式二：

```sql
select score, dense_rank() over(order by score DESC) as `rank` from scores;
```

补充：over后面接column，以此列为排序标准

* rank() over

  >作用：查出指定条件后的进行排名，条件相同排名相同，排名间断不连续。
  >说明：例如学生排名，使用这个函数，成绩相同的两名是并列，下一位同学空出所占的名次。即：1 1 3 4 5 5 7

* dense_rank() over

  > 作用：查出指定条件后的进行排名，条件相同排名相同，排名间断且连续。
  > 说明：和rank() over 的作用相同，区别在于dense_rank() over 排名是密集连续的。例如学生排名，使用这个函数，成绩相同的两名是并列，下一位同学接着下一个名次。即：1 1 2 3 4 5 5 6

* row_number() over

  > 作用：查出指定条件后的进行排名，条件相同排名也不相同，排名不间断且连续。
  > 说明：这个函数不需要考虑是否并列，即使根据条件查询出来的数值相同也会进行连续排序。即：1 2 3 4 5 6

# 多次出现，重复出现

方式一：group by + 子查询

```sql
select class from (
    select class, count(distinct student) as nums from courses
    group by class
) as tempTable
where nums >= 5;
```

方式二： group by + having

```sql
select class from courses 
group by class
having count(distinct student) >= 5
```

# IS NULL & IS NOT NULL

注意：null不参与各种运算符

```sql
select name from customer
where referee_id <> 2 or
	refereee_id is null
```

# 去重查询

要点：

1. 需要将表进行自联结
2. 填入条件，相同的点（**重复项目**）
3. 找到去重的条件，需要去除哪些

```sql
delete a from person as a, person as b
# 去除相同邮箱，保留id小的
where a.email = b.email
and a.id > b.id
```

# 行列转换

输入：

| product_id | store1 | store2 | store3 |
| ---------- | ------ | ------ | ------ |
| 0          | 95     | 100    | 105    |
| 1          | 70     | null   | 80     |

输出：

| product_id | store_name | price |
| ---------- | ---------- | ----- |
| 0          | store1     | 95    |
| 0          | store2     | 100   |
| 0          | store3     | 105   |
| 1          | store1     | 70    |
| 1          | store3     | 80    |

## 列转行

```sql
select product_id, `store1` as store_name, store1 as price from products
where store1 is not null
union
select product_id, `store2` as store_name, store2 as price from products
where store2 is not null
union
select product_id, `store3` as store_name, store3 as price from products
where store3 is not null
```

## 行转列

```sql
select product_id,
	sum(if(store_name = `store1`, price, null) `store1`),
	sum(if(store_name = `store2`, price, null) `store2`),
	sum(if(store_name = `store3`, price, null) `store3`)
from products
group by product_id;
```

