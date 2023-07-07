# 数据库优化

## 优化步骤

1. **创建索引**：创建合适的索引提高查询速度；
2. **分库分表**：当一张表的数据比较多，或者一张表的某些字段的值比较多时；使用水平分表或者垂直分表来优化；
3. **读写分离（集群）**：当一台服务器无法满足需求时，采用读写分离的方式进行集群；
4. **缓存**：采用Redis来进行缓存。

# 一、项目的SQL调优的场景有哪些，数据库调优如何做？

## 场景：

## 1.最左匹配原则

```sql
-- 索引
KEY `idx_shopid_orderno` (`shop_id`,`order_no`)
-- SQL
SELECT * FROM _t WHERE order_no = '';
```

查询匹配从左往右匹配，要使用order_ no走索引，必须查询条件携带shop_id或者索引(shop_id，order_no)调换前后顺序。

## 2.隐式转换

```sql
-- 索引
KEY `idx_mobile` (`mobile`)
-- SQL
SELECT * FROM _user WHERE mobile = 12345678901;
```

隐式转换相当于在索引上做运算，会让索引失效。mobile是字符类型，使用了数字，应该使用字符串匹配。否则MySQL会用到隐式替换，导致索引失效。

## 3.大分页

```sql
-- 索引
KEY `idx_a_b_c` (`a`, `b`, `c`)
-- SQL
SELECT * FROM _t WHERE a = 1 and b = 2 ORDER BY c DESC LIMIT 10000, 10;
```

对于大分页场景，可以让产品优化需求。如果产品没有优化方向，有如下两种方式：

1. 把上次的最后一条数据，即上面的c传过来，然后做"c < xxx"处理，但是如此一般需要改接口协议；

2. 采用延迟关联的方式处理，减少SQL回表，但是要记得索引需要完全覆盖才有效果，SQL如下：

   ```sql
   SELECT t1.* FROM _t AS t1,
   	(SELECT id FROM _t WHERE a = 1 AND b = 2
       	ORDER BY c DESC LIMIT 10000, 10) AS t2
    	WHERE t1.id = t2.id;
   ```

## 4.IN + ORDER BY语句

```sql
-- 索引
KEY `idx_shopid_status_created` (`shop_id`, `order_status`, `created_at`)
-- SQL
SELECT * FROM _order WHERE shop_id AND order_status IN (1, 2, 3) ORDER BY created_at DESC LIMIT 10;
```

in查询在MySQL底层是通过*n\*m*的方式去搜索，类似union，但是效率比union高。in查询在进行cost代价计算时*（代价 = 元组数 \* IO平均值）*，是通过将in包含的数值，一条条地查询获取元组数的。因此这个计算过程比较的慢，所以MySQL设置了一个临界值*（eq_rang_index_dive_limit）*，5.6之后超过这个临界值后该列的cost就不参与计算了。因此会导致执行计划选择不准确。默认值为200，即n条件超过了200个数据后，会导致in的代价计算存在问题，可能会导致MySQL选择索引不准确。

处理方式，可以(order_status, created_at)互换前后顺序，并且调整SQL为延迟关联。

## 5.范围查询阻断，后续字段不能走索引

```sql
-- 索引
KEY `idx_shopid_created_status` (`shop_id`, `created_at`, `order_status`)
-- SQL
SELECT * FROM _order WHERE shop_id = 1 AND created_at > '2023-05-23 00:00:00' AND order_status = 10;
```

范围查询还有*(IN, BETWEEN)*

## 6.不等于(!=)、不包括(NOT )不能用到索引的快速搜索

```sql
SELECT * FROM _order WHERE shop_id = 1 AND order_status NOT IN (1, 2);
SELECT * FROM _order WHERE shop_id = 1 AND order_status != 1;
```

在索引上，避免使用NOT、!=、<>、!<、!>、NOT EXISTS、NOT IN、NOT LIKE等。

## 7.优化器选择不使用索引的情况

如果要求访问的数据量很小，则优化器还是会选择辅助索引；但是当访问的数据占整个表中数据的很大一部分时（一般时20%左右），优化器会选择通过聚集索引来查找数据。

```sql
SELECT * FROM _order WHERE order_status = 1;
```

查询出所以未支付的订单，一般这种订单是比较少的，即使创建了索引，也无法使用索引。

## 8.复杂查询

```sql
SELECT SUM(amt) FROM _t WHERE a = 1 AND b IN (1, 2, 3) AND c > '2023-05-23';
SELECT * FROM _t WHERE a = 1 AND b IN (1, 2, 3) AND c > '2023-05-23' LIMIT 10;
```

如果是统计某些数据，可能改用数仓进行解决；如果是业务上就有那么复杂的查询，可能就不建议继续走SQL了。而是应该采用其他的方式进行解决，比如使用ES技术等。

## 9.升序ASC和降序DESC混用

```sql
SELECT * FROM _t WHERE a = 1 ORDER BY b DESC, c ASC;
```

混合时索引失效。

# 二、SQL调优的步骤：

## 1.通过慢查询日志定位执行效率较低的SQL语句-慢SQL

1. 修改my.cnf配置文件，添加慢查询配置

   ```properties
   [mysqld]
   # 日志输出到表
   log_output=table
   # 启动慢查询日志 1-启用、0-禁用
   slow_query_log=1
   # 慢查询界定，通常查询时间超过1秒钟即可归为慢查询
   long_query_time=1
   ```

2. 使用慢查询，查询慢查询日志

   ```sql
   SELECT * FROM mysql.slow_log;
   ```

   ```ini
   # 慢查询日志
   # Time: 150530 15:30:58 ·· --- 该查询发生在2015·5·30 15:30:58
   # User@Host: root[root] @ localhost [127.0.0.1] ·· --- 是谁，在哪台主机上发生的查询
   # Query_time: 1.134065 Lock_time: 0.000000 Rows_sent: 8 Rows_examined: 40000000 ·· --- 查询共用了多少时间；在查询时锁定表的时间；返回多少rows数据；表扫描了400w行数据才得到的结果；
   ```

## 2.explain分析SQL的执行计划

在找到慢SQL后，用**explain**分析SQL执行计划，需要重点关注type、rows、filtered、extra，重点关注索引问题，查看索引是否失效，索引是否合理。

### type由上到下，效率越来越高

+ ALL：全表扫描
+ index：索引全扫描
+ range：索引范围扫描，常用语<，<=，>=，between，in等操作
+ ref：使用非唯一索引扫描或唯一索引前缀扫描，返回单条记录，常出现于关联查询中
+ eq_ref：类似于ref，区别在于使用的是唯一索引，使用主键的关联查询
+ const/system：单条记录，系统会把匹配行中的其他列作为常数处理，如主键或唯一索引
+ null：MySQL不访问任何表或索引，直接返回结果。

### 虽然上至下，效率越来越高，但是根据cost模型

假设有两个索引*idx_1(a, b, c)*、*idx_2(a, c)*，SQL为：

```sql
SELECT * FROM _t WHERE a = 1 AND b IN (1, 2) ORDER BY C;
```

如果走*idx_1*，那么是type为range；如果走*idx_2*，那么type为ref。

当需要扫描的行数，使用*idx_2*大约是*idx_1*的5倍以上时，会用*idx_1*，否则会用*idx_2*。

### Extra

+ Using filesort：MySQL需要额外的一次传递，以找出如何按排序顺序检索行。通过根据联结类型浏览所有行并为所有匹配**WHERE**子句的行保存排序关键字和行指针来完成排序。然后关键字被排序，并按排序顺序检索行。
+ Using temporary：使用了临时表保存中间结果，性能特别差，需要重点优化
+ Using index：表示相应的 select 操作中使用了覆盖索引（Coveing Index），避免访问了表的数据行，效率不错！如果同时出现 using where，意味着无法直接通过索引查找来查询到符合条件的数据。
+ Using index condition：MySQL5.6之后新增的ICPusing index condtion就是使用了ICP（索引下推），在存储引擎层进行数据过滤，而不是在服务层过滤，利用索引现有的数据减少回表的数据。

## 3.show profile分析

了解`SQL`执行的线程的状态及消耗的时。
默认是关闭的，开启语句`set profiling = 1;`

```sql
SHOW PROFILES ;
SHOW PROFILE FOR QUERY  #{id};
```

## 4. 确定问题并采用相应措施

+ 优化索引
+ 优化SQL语句：修改SQL、IN查询分段、基于上一次数据过滤
+ 改用其他实现方式：ES、数仓等
+ 数据碎片化处理