# Test2


## test2 
# 拉勾数据分析笔记

# 阶段一：Excel

## 案例一：水果店

![image-20220113183004605](/Users/curry/Library/Application Support/typora-user-images/image-20220113183004605.png)

## 案例二：在线教育

![未找到图片：/Users/curry/Library/Application Support/typora-user-images/image-20220113183037037.png](/Users/curry/Library/Application Support/typora-user-images/image-20220113183037037.png "未找到图片：/Users/curry/Library/Application Support/typora-user-images/image-20220113183037037.png")



# 阶段二：MySQL

## SQL查询数据

### 0. MySQL数据类型

![image-20220113183125899](/Users/curry/Library/Application Support/typora-user-images/image-20220113183125899.png)

### 1. 单表查询

#### DDL数据定义语言

结构性操作

``` sql
-- 创建数据库
# 创建数据库
create database db1;
# 创建数据库并指定字符集
create database db1 character set utf8；

-- 查看/选择数据库
# 查看当前数据库
select database()；
# 查看有哪些数据库
show databases;
# 查看一个数据库的定义信息
show create database db1;
# 切换数据库
use db2;

-- 修改、删除数据库
# 修改数据库
alter database db1 character set utf8;
show create database db1;
# 删除数据库
drop database db1;

-- 创建表
# 创建表
use db1；
create table table1(
  id int，
  name varchar(20),
  cdate date
);

# 快速创建一个表结构相同的表
create table db2 like db1;

-- 查看表
# 查看当前数据库中的所有表
show tables;
# 显示当前数据表结构
desc table1;
# 查看创建表的语句
show create table table1;

-- 修改/删除表
# 修改表名 rename
rename table table1 to table2;
# 向表中添加字段，add
alter table table1 add age int;
# 修改表中字段的数据类型或长度，modify
alter table table1 modify age smallint;
# 修改表中字段名称，change
alter table table1 change age ages smallint;
# 删除字段，Drop
alter table table1 drop age;
# 删除表
drop table table1
```

#### DML数据操纵语言

针对数据的增删改

```sql
# 往这个表里插入数据
create table student(
  sid int,
  sname varchar(20),
  age int,
  sex char(1),
  address varchar(40)
);

-- 增
# 如果插入控制，可以忽略不写
# 方式一：插入全部字段，并列出所有字段名，一一对应
insert into student(sid,sname,age,sex,address)values(1,'老刘',20,'男','枣营北里');
# 方式二：插入全部字段，不写字段名
insert into student values(1,'老刘',20,'男','枣营北里');
# 方式三：插入指定字段的值
insert into student(cname)values('老李');

-- 改
# 整列修改
update student set sex = '女';
# 修改符合条件的数据
#比如修改sid=3的学生，性别修改为男
update student set sex = '男'where sid = 3;
#比如修改sid=3的学生，年龄修改为20，性别修改为男
update student set age = 20,sex = '男'where sid = 3;

-- 删
# 删除所有数据
# 方式一：一条条删数据
delete from student;
# 方式二：删除整张表，然后重建一张一样的表（不带数据）
truncate table student;
# 删除符合条件的数据
delete from student where sid = 1;
```

#### DQL数据查询语言

针对数据的查询（书写顺序：select-from-where-group by-having--order by-limit）

```sql
-- 简单查询
#查询所有数据
select * from emp；
#查询emp中所有记录，仅显示id和name字段
select edi,ename from emp;
#将字段名称改成中文
select
	eid as '编号',
	ename as '姓名'
from emp;
#只查询不重复信息
select distinct dept_name from emp;
#计算后再显示
select ename, salary + 1000 from emp;

-- 条件查询
#查询员工黄蓉的所有信息
select * from emp where ename = '黄蓉';
#查询薪水不是5000的员工信息
SELECT * FROM emp WHERE salary != 5000; 
SELECT * FROM emp WHERE salary <> 5000;
# 查询薪水价格在5000到10000之间所有员工信息
SELECT * FROM emp WHERE salary BETWEEN 5000 AND 10000;
# 查询薪水价格是3600或7200或者20000的所有员工信息
-- 方式1: or
SELECT * FROM emp WHERE salary = 3600 OR salary = 7200 OR salary = 20000; 
-- 方式2: in() 匹配括号中指定的参数
SELECT * FROM emp WHERE salary IN(3600,7200,20000);
#模糊查询
#查询含有精字的所有员工信息
select * from emp where ename like '%精%';
#查询以孙开头的所有员工信息
select * from emp where ename like '孙%';
#查询第二个字为兔的所有员工信息
select * from emp where ename like '_兔%';
#查询没有部门的员工信息
select * from emp where dept_name is null;

-- 核心查询
#排序（order by）
## 单列排序:正序，倒序
select * from emp order by salary;
select * from emp order by salary desc;
## 组合排序，先以薪水倒序排序，当薪水相同时，以eid倒序排序
select * from emp order by salary desc,eid desc;

#聚合（count、sum、max、min、avg）
##查询员工的总数，注意不能用有空数据的字段来计算
select count(*) from emp;
select count(1) from emp;
##查询员工总薪水、最高薪水、最小薪水、平均薪水
select
	sum(salary)
	max(salary)
	min(salary)
	avg(salary)
from emp;

#分组（group by），需要和聚合函数一起用，group by前面只能写要分组的条件和聚合函数。
##通过性别字段进行分组,求各组的平均薪资
SELECT sex, AVG(salary) FROM emp GROUP BY sex;
##分组后对数据进行过滤：where和having的区别：where进行分组前的过滤，且不能使用聚合函数；having进行分组后的过滤，且可以使用聚合函数。
##查询每个部门的平均薪资，部门名称不能是Null
select 
	dept_name, avg(salary) 
from emp 
where dept_name is not null 
group by dept_name;
##查询平均薪资大于6000的部门
select
	dept_name, ave(salary)
from emp
group by dept_name
having avg(salary) > 6000;

#limit关键字，limit offset，length（offset起始行数，默认为0，length返回的行数）
##查询emp表中第4行到第6行
select * from emp limit 3,2;
##分页操作：每页显示3条数据(起始索引公示：（当前页数-1）*每页条数
select * from emp limit 3;
select * from emp limit 3,3;
select * from emp limit 6,3;
```

#### DCL数据操作语言

事务处理

### 2. 约束

对表中数据进行一些限制，比如主键、唯一、非空、外键约束

#### 主键约束

```sql
-- 主键约束：primary key
特点：不可重复、非空、唯一
哪些字段作为主键？通常针对业务选择，每张表都会设计一个主键。主键没意义没关系，只要保证不重复就可以。如果没有合适作为主键的字段，可以添加一列序号作为主键。
#创建一个带主键的表
##方式一
create table emp2(
  eid int primary key,
  ename varchar(20)
);
##方式二
create table emp2(
  eid int,
  ename varchar(20),
  primary key(eid)
);
##方式三
create table emp2(
  eid int,
  ename varchar(20)
);
alter table emp2 add primary key(eid);
#删除主键约束
alter table emp2 drop primary key;
#设置主键自增auto_increment
##创建主键子增的表
create table emp2(
  eid int primary key auto_increment,
  ename varchar(20)
);
#修改自增的起始值
##创建主键从100开始自增的表
create table emp2(
  eid int primary key auto_increment,
  ename varchar(20)
)auto_increment = 100;
#delete和truncate对自增的影响
##delete删除表中所有数据，不影响自增。比如当前数据自增到102，delete删除表中所有数据后，继续插入数据，数据从继续从103开始。
##truncate删除表，自增重新从1开始。自增起始值的设置也没用了。
-- 非空约束
#将ename设置为非空
create table emp2(
  eid int,
  ename varchar(20) not null
);
-- 唯一约束
create table emp2(
  eid int,
  ename varchar(20) not null unique
);
-- 默认值约束
##创建性别默认为女的表
create table emp2(
  eid int,
  ename varchar(20),
  sex char(1) default'女'
);
##不使用默认值
insert into emp2 values(1,'a','男');

```

### 3. 多表查询

#### 外键约束

外键：数据表中A有一列，指向了数据表B的主键，那么A中这一列则为外键，A为从表，B为主表。

```sql
#外键约束注意事项：
1.从表外键子段类型必须与主表主键字段类型一致，否则创建失败
2.添加数据时，应该先添加主表中的数据
3.删除数据时，应该先删除从表中的数据
#添加外键约束
##方式一：新建表时添加。在emp表中将dept_id添加外键，对应到department表中的id字段。
create table emp(
  eid int primary key,
  ename varchar(20),
  dept_id int,
  constraint dept_fk foreign key(dept_id) references department(id)
);
##方式二：已有表后添加外键（constraint dept_fk外键名称可以省略，系统会自动生成一个）
alter table emp add[constraint][dept_fk] foreign key(dept_id) references department(id);

#删除外键约束
alter table emp drop foreign key dept_fk;

#级联删除：实现当主表数据删除时，与之相关的从表数据也被删除
在添加外键时加上：on delete cascade
```

```sql
--笛卡尔积查询
#最原始：笛卡尔积方式，会查出很多无效数据。表A与表B中的每一条数据进行组合。
select * from 表1，表2;
```

```sql 
--内连接查询：通过指定条件匹配两张表中的数据
---隐式内连接：
select * from 表1，表2 where...;
---显示内连接：
select * from 表1 join 表2 on...;

###例子：查询每个分类下有哪些产品
#隐式内连接：笛卡尔积+where
select p.pname, c.cname from products p, category c where category_id = cid
#显式内连接：join...on...
select p.pname, c.cname from category c join products p on c.cid = p.category_id; 
####例子：查询鞋服分类下，价格大于500的商品名称和价格
#显式内连接：
select p.pname,p.price from products p join category c on p.category_id = c.cid where p.price > 500 and c.cname = '鞋服';
```

![image-20220105163037950](/Users/curry/Library/Application Support/typora-user-images/image-20220105163037950.png)


```sql 
--外连接查询
---左外连接：以左表为基准，匹配右表数据，匹配不到的话，左表数据正常显示，右表显示为null
select * from category c left join products p on c.cid = p.category_id;
```

![image-20220105163623507](/Users/curry/Library/Application Support/typora-user-images/image-20220105163623507.png)

```sql
---右外连接：以右表为基准，匹配左表数据，匹配不到的话，右表数据正常显示，左表显示为null
select * from products p right join category c on p.category_id = c.cid;
```

![image-20220105164540778](/Users/curry/Library/Application Support/typora-user-images/image-20220105164540778.png)

```sql
#例子：
## 左外连接，查询每个分类下的商品种数
select 
	c.cname as '类别名称',
	count(p.pid) as '商品种数'
from category c left join products p on c.cid = p.category_id
group by c.cname;
```

![image-20220105165300828](/Users/curry/Library/Application Support/typora-user-images/image-20220105165300828.png)

```sql
总结：
内连接：只获取两张表中交集的部分
左外连接：获取左表的所有数据，以及与右表有交集的部分
右外连接：获取右表的所有数据，以及与左表有交集的部分
```

#### join on与where条件执行顺序

（参考阶段二模块一第3小题程序异常）

1. where指的是，从on条件筛选出来的数据中，再进行筛选的过程。如果on后面都没有想要的数据，那么where也不会有
2. on后面的条件，可以是多个。如果是多个，则用左外连接时，将会需要同时满足这两个条件的时候，才会有数据，没有数据的就显示为null。

### 4. 合并查询

```sql
#union合并：
两个select语句必须有相同数量的列，且列必须拥有相似的数据类型，列的名称可以不同。
会删除重复的列
null值不会被忽略
SELECT column1 [, column2 ]
FROM table1 [, table2 ]

UNION

SELECT column1 [, column2 ]
FROM table1 [, table2 ]

#union all合并：
简单的将两个结果合并，不去重
SELECT column1 [, column2 ]
FROM table1 [, table2 ]

UNION all

SELECT column1 [, column2 ]
FROM table1 [, table2 ]

```

### 5. 子查询

```sql
-- where型：将子查询到结果，作为父查询的一个条件
##查询价格最高的商品信息
select * from products where price = (select max(price) from products);
##查询化妆品分类下的商品名称和商品价格
select 
	p.pname,
	p.price
from products p 
where p.category_id = (select cid from category where cname = '化妆品');
##不用子查询也可以实现
select 
	p.pname,
	p.price
from products p 
join category c on c.cid = p.category_id where c.cname = '化妆品';
-- from型：将子查询的结果作为一张表，提供给父查询使用，注意子查询结果必须重新命名才能访问表中字段
##查询出所有商品名称
select 
	b.pname
from (select pid, pname from products)b;
-- exists型：子查询的结果是单列多行，类似一个数组，不能用where=，所以用where in
##查询价格小于2000的商品，来自哪些分类
select * from category 
where cid in (select distinct category_id from products where price < 2000);
##查询家电类与鞋服类下面的全部商品信息
###先查出家电与鞋服的cid
select cid from category where cname in ('家电','鞋服');
###根据cid查询分类下的商品信息
select * from products
where category_id in (select cid from category where cname in ('家电','鞋服'));

-- 子查询总结：
查出来的数据如果是单列，那就在where后面作为条件：单列单行用=，单列多行用 in，
查出来的数据是多个字段，就当作一张表使用（要起别名）。
```

### 7. 案例：汽车新零售

```sql
#需求1:查询线索（二级渠道jdsc）后续转化成交车型详情
#隐式内连接
select
	c.clue_id,
	ca_n,
	chexing_id
from clue_day c, order_day o
where c.clue_id = o.clue_id
and ca_n = 'jdsc';
#显式内连接 
select
	c.clue_id,
	ca_n,
	chexing_id
from clue_day c join order_day o 
on c.clue_id = o.clue_id
where ca_n = 'jdsc';

#需求2：统计所有渠道（按照二级渠道）的转化率 
select 
	c.ca_n,
	count(distinct o.clue_id)/count(distinct c.clue_id) sale_rate
from
	clue_day c
	left join order_day o
	on c.clue_id = o.clue_id
group by c.ca_n
order by sale_rate desc
limit 6;

#需求3：查询各城市线索数并计算所有城市线索总数
select 
	city_id as '城市',
	count(clue_id) as '线索数'
from 
	clue_day
group by city_id

union all

select
	'总计' as 'city_id',
	count(clue_id)
from clue_day;

/*
需求4: 找出优质渠道，做重点投入
优质渠道定义：
该二级渠道内平均转化周期 < 整体平均转化周期 
该二级渠道的线索量 > 各二级渠道平均线索量
转化周期:成单日期（created_at） — 线索创建日期（clue_created_at）
*/

with clue_info as(select
	ca_n,
	avg(datediff(created_at, clue_created_at)) avg_single,
	count(clue_day.clue_id) clue_num
from clue_day
left join order_day
on clue_day.clue_id = order_day.clue_id
group by ca_n)
select 
	ca_n,
	avg_single,
	clue_num
from clue_info
where avg_single < (select 
avg(avg_single)
from clue_info)
and clue_num > (select
ceil(avg(clue_num))
from clue_info);

#老师答案：
WITH ca_value AS (
SELECT
ca_n,
avg( DATEDIFF( created_at, clue_created_at ) ) avg_time, count( c.clue_id ) clue_num
FROM
clue_day c
LEFT JOIN order_day o ON c.clue_id = o.clue_id
GROUP BY
        ca_n
      ) SELECT
      ca_n,
      avg_time,
      clue_num
FROM
ca_value
WHERE
	avg_time < (-- 整体线索平均转化周期 
	SELECT
    avg( DATEDIFF( created_at, clue_created_at ) )
FROM
clue_day c
LEFT JOIN order_day o ON c.clue_id = o.clue_id WHERE
o.created_at IS NOT NULL
AND c.clue_created_at IS NOT NULL )
AND
clue_num > (
-- 二级渠道平均线索量(需要基于上面select查询进一步avg处理) 
SELECT ceil( avg( clue_num ) ) FROM ca_value
);
```

## 函数及其他功能

### MySQL函数

```sql
-- 系统信息函数
#返回当前数据库名
select database();
#返回数据库版本
select version();
#返回当前用户名
select user();

-- 数学函数
#绝对值函数abs
select abs(-10) as '绝对值' ;
#向下取整floor
select floor(5.9) as '向下取整';
#向上取整ceil
select ceil(5.01) as '向上取整';
#四舍五入round，第二个数值是保留几位小数的意思
select round(5.419,2) as '四舍五入';
#随机数rand，返回0-1之间的随机数 
select rand(); 
#返回圆周率
select pi(); 
#求余数
select mod(5,2); 

-- 字符串函数
#将字符串拼接，连接为一个字符串，CONCAT(s1,s2...)
SELECT CONCAT('la','gou') AS 字符串链接;
#返回从字符串s开始的n最左字符，LEFT(s,n)
SELECT LEFT('lagou',2) AS le_sub;
#la
select right('lagou',2) as ri_sub;
#ou
select mid('lagou',2,3) as mid_sub;
#ago
#移除掉字串中s的字头或字尾处空格：TRIM(s)
SELECT TRIM(' lagou ') AS 去掉首尾空格;
#用字符串s2替代字符串s重的字符串s1：REPLACE(s,s1,s2)
SELECT REPLACE('lagou_jiaoyu','_','.') AS 字符串替换; #lagou.jiaoyu
#截取字符串s中第n个位置开始，长度为len的字符串：SUBSTRING(s,n,len)
SELECT SUBSTRING('lagou_jiaoyu',7,6) AS SUBSTRING提取子串,
MID('lagou_jiaoyu',7,6) AS MID提取子串;
#将字符串s的顺序翻转过来：REVERSE(s)
SELECT REVERSE('lagou') AS 字符串翻转;

-- 日期和时间函数
SELECT CURDATE() AS 当前日期;
SELECT CURTIME() AS 当前时间;
SELECT NOW() AS 当前日期和时间;

-- 条件判断函数
#如果表达式成立，则执行v1，否则执行v2：IF(expo,v1,v2)
SELECT IF(10>5,10,5) as 最大值;
select pname,if(price>2000,'奢侈品','普通商品') '商品性质' from products;
#用于计算条件列表并返回多个可能结果表达式之一
case
when 条件1 then 结果1 
when 条件2 then 结果2 
when ... then 结果n (else 结果n+1)
end
```



### 窗口函数

窗口是一个范围，在此范围内的每条记录都要执行窗口函数。可以有静态窗口和滑动窗口。

窗口函数partition by和group by功能相同，不过窗口函数是每条记录都执行，而group by是整合成一个结果。

窗口函数基本用法：

```sql
函数名(使用窗口函数的列)  over(partition by <分组的列> order by<排序的列> rows between<数据范围>)

-- rows between使用方法：
rows between 2 preceding and current row #取当前行和前两行
rows between unbounded preceding and current row #取当前行和前面所有行
rows between current row and unbounded following #取当前行和之后所有行
rows between 3 preceding and 1 following # 取前面三行和下面一行，共五行
#当order by后面没有窗口从句，窗口默认是rows between unbounded preceding and current row.
#当partition by后面没有窗口从句，窗口默认为rows between unbounded preceding and unbounded following.
```

```sql
-- 专有窗口函数
rank()、dense_rank()、row_number()、ntile(n)：直接写在select语句后面座位新一列
#排序例子：2020年1月，购买商品品类数的用户排名
select 
	user_name,
	count(distinct goods_category),
	row_number() over(order by couont(distinct goods_category))
from user_trade
where substring(pay_time,1,7) = '2020-01'
group by user_name;
#分组函数ntile(n)例子：查询出将2020年2月的支付用户，按照支付金额分成5组后的结果
select
	user_name,
	sum(pay_amount) pay_amount,
	ntile(5) over (order by sum(pay_amount) desc) level
from user_trade
where substr(pay_time,1,7) = '2020-02'
group by user_name

-- 聚合类窗口函数
sum()、count()、 avg()、max()、min()：需要将上一个查询作为子查询，再新增一列
#例子：查询出梅四个月的最大月总支付金额
select
	a.month,
	a.pay_amount,
	max(a.pay_amount) over (order by a.month rows between 3 preceding and current row)
from
	(select 
   		substr(pay_time,1,7) as month,
   		sum(pay_amount) as pay_amount
   from user_trade
   group by substr(pay_time,1,7))a;
   
-- 偏移分析函数
lag()、lead()
lag(哪个字段，偏移量，如果找不到就取本行)
示例：
lag(pay_time,1,pay_time) over(...)：取pay_time字段上面一行，如果找不到就取本行的pay_time
#例子：查询出每年支付时间间隔最长的用户 
select
b.year,
b.user_name,
b.jump_days
from 
  (select
  a.year,
  a.user_name,
  datediff(a.pay_time, a.lag_dt) jump_days,
  rank() over(partition by a.year order by datediff(a.pay_time, a.lag_dt) desc) rank1
  from 
    (select 
    year(pay_time) year,
    user_name,
    pay_time,
    lag(pay_time) over (partition by user_name, year(pay_time) order by pay_time) lag_dt
    from user_trade) a 
  where lag_dt is not null) b
where b.rank1 = 1 
```

### MySQl索引

索引是什么：在大量数据场景下效果明显，主要是为了提高查询速度，会影响where及order by语句

1. 主键索引：创建主键时会自动添加主键索引

2. 唯一索引

```sql
#建表时添加
create table 表名(
	字段名 数据类型,
  unique[索引名称](字段名)
)
#在已有的表上创建索引
create unique index 索引名 on 表名（字段名）;
#修改表结构添加索引
alter table 表名 add unique 索引名（字段名）
```

3. 普通索引

   唯一任务是加快对数据的访问速度，应该只为那些经常出现在查询条件或排序条件的字段添加索引。

```sql
#创建索引
create index 索引名 on 表名（字段名）;
#修改表结构
alter table f add index 索引名（列名）;
#复合索引：当where条件为a=1 and b =2时，可以为a、b两个字段创建复合索引，记得顺序要统一
create index a_b_index on table(a,b);
```

4. 删除索引

   索引会占用一定磁盘空间，应即时删除不需要的索引

   ```sql
   alter table 表名 drop index 索引名;
   ```

### MySQL视图

视图：封装起来的sql语句，从基表中获取数据。可以控制权限，简化多表查询

```sql
#创建视图
create view 视图名 as select语句
```

### 模块二大作业

- 留存：

留存:指基准日到APP的用户在之后的n日当天返回APP的人数; 留存率 = 基准日之后的n天当日返回的用户数 / 基准日的用户数 * 100%

或者指基准日产生某个行为的用户在之后的第一天，第二天，第三天......第n天的当天再次产生该行为的 人数。

- 留存计算：

一般用用户id，活跃日期做数据表自连接，然后用datediff统计出后期用户留存率

## 案例

### 案例一：电商平台

行为路径查询

```sql
#查找每个用户每个商品最后一次购买行为的行为路径
select
a.*
from 
(select
user_id,
item_id,
lag(behavior_type,4) over (partition by user_id,item_id order by date_time) lag_4,
lag(behavior_type,3) over (partition by user_id,item_id order by date_time) lag_3,
lag(behavior_type,2) over (partition by user_id,item_id order by date_time) lag_2,
lag(behavior_type) over (partition by user_id,item_id order by date_time) lag_1,
behavior_type,
rank() over (partition by user_id,item_id order by date_time desc) rank_number
from temp_trade) a 
where a.rank_number = 1 and
a.behavior_type = 2;
#输出每条行为路径用户数
select
concat(
ifnull(lag_4,'空'),'-',
ifnull(lag_3,'空'),'-',
ifnull(lag_2,'空'),'-',
ifnull(lag_1,'空'),'-',
behavior_type) as user_way,
count(distinct user_id) as user_amount
from product_user_way w
group by user_way;
```

之所以要加rank函数：是因为同一个用户对同一个商品可能做了多次操作，只关心最后购买行为的行为路径。所以在同一用户和同一商品组内进行时间倒序排序，选出排名第一的数据。

### 案例二：在线教育拓展“数据分析课程”

# 阶段三：BI商业智能工具

## Tableau数据可视化





