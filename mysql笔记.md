## mysql主要存储引擎

InnoDB：表空间大，支持主外键，支持事务，支持行锁，适合高并发操作

MyISAM：表空间小，不支持主外键，不支持事务，支持表锁，不适合高并发操作

## 性能下降SQL慢

1. 查询语句差
2. 索引失效（单值，复合）
3. 关联查询多

建索引

create index idx_user_name on user(name)

### SQL读取顺序

从from开始读

## 索引（Index）

是帮MySQL高效获取数据的**数据结构**

排好序的快速查找数据结构

详细解释：

数据本身之外，数据库还维护着一个满足特定查找算法的数据结构，这些数据结构以某种特定的方式指向数据，这样就可以在这些数据之上实现高级的查找算法，这种数据结构就是索引

### 索引类型

1. 单值索引：即一个索引只包含单个列，一个表可以有多个单列索引
2. 唯一索引：索引列的值必须唯一，但允许空值
3. 复合索引：一个索引包含多个列

### 语法：

#### 创建：

create [unique] index indexName on mytale(columname(length));

alter mytable add [unique] index [indexName] on (columname(length))

#### 删除

drop index [indexName] on mytable

#### 查看

show index from table_name\G

#### 



### 索引结构

1. BTree索引
2. Hash索引
3. full-text全文索引
4. R-Tree索引

## Explain

一般都是 explain+SQL语句

这样能分析你的查询语句或是表结构的性能瓶颈

### 查询结果字段解析

#### id:

表示select子句或操作表的顺序

三种情况：

1. 相同，执行顺序由上到下
2. 不同，大的先执行
3. 有相同有不同，大的先执行，相同的由上到下
4. null 结果合并，最后执行

#### select_type：

查询的类型，一般来用于区别普通查询，联合查询，子查询

#### table：

表类型

#### type：

表示访问类型，有以下几种

system（系统表）>const（常量）>eq_ref>ref>**range（范围）>index**>ALL

最好到最差

一般都是到range和index

#### possible_keys和key

possible_keys:可能用到的索引

key：真正用到的索引

#### key_len

查询中使用索引的长度，越短越好，最大可能长度，并非实际长度

#### ref：

显示索引的哪一列被引用了

#### rows：

根据索引和表信息计算出查找所需记录所需要读取的行数

#### extra：

其他列不适合书写，但重要的其他信息

##### 类型：

1. **Using filesort**：危险，表内索引无法完成排序，引用外部排序（尽快优化）
2. **Using temporary：**对查询结果排序时使用了临时表，一般出现与order by 和group by（特别严重）
3. **Using Index：**索引被引用，如果和Using where一起出现，那么索引真正被用，没有那么就是没有查找动作（好的）
4. Using where
5. 

## 查询截取分析

### SQL优化步骤：

1. 观察，至少跑一天，看看生产的慢SQL情况
2. 开启慢日志查询，设置阙值，比如超过5秒的SQL，抓取出来
3. explain+慢SQL分析---80%
4. show profile （）----99%
5. 运维经理 or DBA，进行SQL数据库服务器的参数调优---100%

### 总结：

1. 查询优化
2. 慢查询的开启并捕获
3. explain+慢SQL分析
4. show profile 查询SQL在Mysql服务器里面的执行细节和生命周期情况
5. SQL数据库服务器的参数调优

### 优化原则：

小表驱动大表，小的数据集驱动大的数据集

exist替换in的情况，当where的括号里是子查询时用exist

select ....from table where exist（subquery（子查询））

可以理解为：将主查询的数据，放到子查询中做条件查询，根据结果决定主查询数据是否得以保留

### Order by 优化

##### 在索引列上，为了让orderby不出现filesort：

1. order by 语句使用索引最左前列
2. 使用where子句与order by 子句条件列组合满足索引最左前列

##### 不在索引列上，filesort会启动两种算法（双路算法<单路算法(快)）

1. 不要用select *（双路算法开启）
2. 尝试提高sort_buffer_size
3. 尝试提高max_length_for_sort_date

### 慢查询日志

用来记录超过阙值的sql语句，该阙值long_query_time的默认值为10，之后结合explain进行分析优化



## MySQL锁机制

锁是计算机协调多个进程或线程并发访问某一个资源的机制

### 锁分类

#### 操作的粒度：

表锁和行锁

### 数据的操作类型

读锁（共享锁）：针对同一个数据，多个操作可以同时进行而相互不影响

写锁（排他锁）：当前操作没完成之前，阻断其他的读锁和写锁

### 三锁

1. #### 表锁（偏读）

   偏向MyISAM存储引擎，开销小，加锁快，无死锁，粒度大，冲突率最高，并发度高

   ##### 格式：

   lock table 表名 read（write），表2 read（write），其他；

   unlock tables ;

   #### 注意：

   加读锁，当前用户不能再读其他表

   写锁，独占

   ##### set autocommit=0 关闭事务自动提交

2. #### 行锁（偏写）

   偏向于InnoDB存储引擎，开销大，加锁慢，会出现死锁，锁定粒度最小，发生锁冲突的概率最低，并发度也最高，**支持事务**。

   事务并发带来的四个问题：

   ​	前提条件都是一个事务内

   1. 更新丢失：后覆盖先
   2. 脏读：a读了b修改但未提交数据
   3. 不可重复读：a读了两次数据不同
   4. 幻读：a读取了b提交的新增数据

   mysql默认可重复读隔离级别

   #### 无索引行锁升级表锁（隐蔽故障）

   varchar类型写错为int类型

   类型赋错值

   #### 间隙锁危害

   当我们用范围条件选定行时，mysql会把没有存在的行都锁了，真正要操作新建该行时，该用户也得等锁释放，这就是间隙锁

   #### 如何锁定一行

   begin；

   select..... for update;

   锁定

   commit;释放

3. #### 页锁