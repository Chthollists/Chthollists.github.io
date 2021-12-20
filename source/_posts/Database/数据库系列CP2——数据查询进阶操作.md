---
title: 数据库系列CP2——数据查询进阶操作
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Middleware
  - MySQL
tags:
  - MySQL
  - 子查询
  - 连接查询
abbrlink: 2db84f7e
date: 2020-08-06 18:11:58
top:
---

---

* 数据库系列CP2——数据查询进阶操作
* CP2主要分享了：
* where关键字语法
* order、limit、group语法
* 聚合函数：sum/avg/count/max/min
* 分页查询
* 连接查询(内连接、外连接)、自连接查询
* 子查询

<!-- more -->

# 数据库系列CP2——数据查询进阶操作

## 二、数据库数据查询进阶操作

* 时间：2020.08.04 ~ 2020.08.06

* 主要内容
  * 查询进阶
  * where关键字语法
  * order、limit、group语法
  * 聚合函数：sum/avg/count/max/min
  * 分页查询、连接查询、自连接查询、子查询

### 1. 查询高级操作

* 时间：2020.08.04

* 查询所有字段

  > ```sql
  > select * from 表名;
  > ```

* 查询部分字段

  > ```sql
  > select 字段1, 字段2...   from 表名;
  > ```

* 查询字段起别名：as 关键字

  > ```sql
  > select 字段 as ‘别名‘   from 表名;
  > select 字段  ‘别名‘   from 表名;
  > ```

* 查询并给表起别名：as 关键字

  > ```sql
  > select 表别名.表字段名,.... from 表名 as 别名;
  > select  s.id, s.name    from  students as s;
  > ```

* 去重查询：distinct 关键字

  > ```sql
  > select distinct 字段名 from 表名;
  > ```

---

### 2. 条件查询：where关键字

* 时间：2020.08.04

* where语句的作用：

  > 使⽤where⼦句对表中的数据筛选，结果为true的⾏会出现在结果集中。

* where后面支持多种运算符，进行条件的处理，不同的运算符可以联合使用，主要运算符为：

  * ⽐较运算符 
  * 逻辑运算符 
  * 模糊查询 
  * 范围查询 
  * 空判断

#### 2.1 比较运算符

* 等于:	=

* ⼤于:	> 

* ⼤于等于:	>= 

* ⼩于:	< 

* ⼩于等于:	<= 

* 不等于:	!=	或	<>

  > ```sql
  > select * from students where age > 18;
  > select s.name  '姓名' from students as s where age != 20;
  > select distinct s.gender from students as s where age <> 20;  
  > select * from students where is_delete = 0;
  > ```

#### 2.2 逻辑运算符

* 与：and，表示有多个条件时，多个条件必须同时成⽴（值为True） 

* 或：or，表示有多个条件时，满⾜任意⼀个条件时成⽴ 

* 非：not，表示取反操作，需要放在条件开头

  > ```sql
  > select * from students where age > 18 and age <= 24;
  > select * from students where age > 55 or gender = '妖';
  > select * from students where not gender = '女' and is_delete = 0;
  > ```

#### 2.3 模糊查询

* like 关键字

* %  表示任意多个任意字符 

* _    表示⼀个任意字符

  > ```sql
  > -- 查询姓刘的学生的信息
  > select * from students where name like '刘%';
  > -- 查询姓刘且名字只有两个字的学生的信息
  > select * from students where name like ‘刘_’;
  > -- 查询名字包含菲的学生的信息
  > select * from students where name like '%菲%';
  > -- 查询名字为三个字的学生的信息
  > select * from students where name like '___';
  > ```

#### 2.4 范围查询

* 范围查询分为连续范围查询和⾮连续范围查询

* 连续范围查询 ：between  and   关键字

  > 注意:	between	A	and	B在匹配数据的时候匹配的范围空间是	[A,B]
  >
  > ```sql
  > -- 查询年龄在18~30范围的信息
  > select * from students where age between 18 and 30;
  > ```

* 非连续范围查询： in 关键字

  > ```sql
  > -- 查询年龄为18,24,30的信息
  > select id, name, age  from students where age in(18, 24, 30);
  > ```

#### 2.5 空判断

* is null  表示判断值为空

* is not null 判断值非空， 错误写法：not is null

  > 字段名 is not null 等价于 not 字段名 is null
  >
  > ```sql
  > -- 查询身高为空的信息
  > select * from students where height is null;
  > -- 判非空is not null
  > select * from students where height is not null;
  > select * from students where not height is null;
  > ```

#### 2.6 小结

* 运算符：比较、逻辑、模糊、范围、空判断

* 不同的运算符可以联合使用

* 优先级由高到低的顺序为：

  > ()小括号，not，比较运算符，逻辑运算符 and比or先运算，如果同时出现并希望先算or，需要结合()使用

---

### 3. 查询常用其他关键字

* 时间：2020.08.05

#### 3.1 排序

* order by 关键字

  > order by 需要放在where、运算符后

* 语法格式：`select * from 表名 order by 列1 asc|desc [,列2 asc|desc,...]`

  > 将行数据按照列1进行排序，如果某些行列1的值相同时，则按照列2排序，以此类推
  >
  > **asc**：从小到大排列，即升序 
  >
  > **desc**：从小到大排序，即降序 
  >
  > 默认按照列值从小到大排列（即asc关键字）

  > ```sql
  > -- 按年龄升序(默认)
  > select * from students order by age;
  > -- 按身高降序的女生
  > select * from students where gender = '女' order by height desc;
  > -- 按照年龄升序、身高降序排序
  > select * from students where not gender = '中性' and age between 18 and 50 order by age asc, height desc;
  > ```

#### 3.2 聚合函数

* 聚合函数aggregation function⼜称为组函数。 

  > 默认情况下	聚合函数会对当前所在表当做⼀个组进⾏统计。

* 聚合函数特点：

  * 每个组函数接收⼀个参数(字段名或者表达式)
  * 统计结果中默认忽略字段为NULL的记录，要想列值为NULL的⾏也参与组函数的计算，必须使用IFNULL函数对NULL值做转换。
  * 不允许出现嵌套，如sum(max(xx))	

* 常见的聚合函数：

  * count(*)     统计行数

  * max(字段)  找出该字段(列)的最大值

  * min(字段) 找出该字段(列)的最小值

  * sum(字段)  对该字段(列)求和

  * avg(字段)    对该字段(列)求平均值

    > round(数值,保留的小数位数)   四舍五入
    >
    > 注意：round不是聚合函数，其操作对象不是数据库的原始数据
    >
    > ```sql
    > select count(*) from students;
    > select max(age) from students where gender = '男';
    > select min(age) from students where gender = '女';
    > select sun(age) from students where gender = '男';
    > select avg(age) from students where gender = '女' and is_delete=0;
    > select round(avg(age),2) as '平均年龄' from students where gender = '女' and is_delete=0;
    > ```

#### 3.3 分组

* group by 关键字：放在where后，order前

* 使⽤特点：

  * group by 的含义：将查询结果按照1个或多个字段进行分组，字段值相同的为⼀组
  * group by 可⽤于单个字段分组，也可⽤于多个字段分组

* 语法格式：

  > ```sql
  > -- select 字段1名 from 表名 group by 字段1名
  > -- 按性别分组
  > select gender from students group by gender;
  > 
  > -- select 字段1名, 聚合函数 from 表名 group by 字段1名
  > -- 按性别分组，显示人数、最大年龄
  > select gender, count(*) from students group by gender;
  > select gender, max(age) from students where not gender = '保密' group by gender order by max(age);
  > 
  > -- 按字段分组后，将另一个字段在该组中的数据连成一个字符串
  > -- select 字段1名, group_concat(字段名) from 表名 group by 字段1名
  > -- 查询同种性别中的姓名
  > select gender, group_concat(name) from students group by gender;
  > 
  > -- 分组后再进行条件过滤，使用having关键字
  > select gender, avg(age) from students group by gender having avg(age)>30;
  > select gender, avg(age) as avg from students group by gender having avg>30;
  > 
  > -- with rollup  显示分组后的小结，即对分组后新的列添加一个新的记录，该记录是对整个列做分组时的操作后的结果，如avg、group_concat等
  > -- with rollup 加在 group by 字段名 的后面
  > select gender, avg(age) as avg from students group by gender with rollup having avg>30;
  > select gender, max(age) from students where not gender = '保密' group by gender order by max(age);
  > 
  > -- 综合
  > -- 按性别分组，显示该组人数大于 2 的平均年龄、姓名
  > select gender, avg(age) as avg, group_concat(name) from students group by gender having count(*)>2;
  > ```

* group by 用于分组（按照某个特定的字段进行分类）

  > group by 字段名

* group by + 聚合函数，分组统计/计算

* group by + group_concat()  分组 + 内容链接为一个字符串

* group by + with rollup     分组 + 小计

  > with rollup 加在 group by 字段名 的后面
  >
  > 显示分组后的小结，即对分组后新的列添加一个新的记录
  >
  > 该记录是对整个列做分组时的操作后的结果，如avg、group_concat等

#### 3.4 限制记录

* limit 关键字：一定在SQL语句的最后

* 适用于数据量非常大的时候使用

* 语法格式：`limit 起始记录,记录数`

  > 起始记录是指从第⼏条记录开始取，第⼀条记录的下标是0；
  >
  > 默认起始记录为0；
  >
  > 记录数是指从起始记录开始向后依次取的记录的个数。
  >
  > ```sql
  > select * from students limit 0,5;
  > ```

* 关键字顺序总结：

  > ```sql
  > select 字段1,字段2,...
  > from  表名
  > [where 条件]
  > [group by 字段名]
  > [order by 字段名 排序规则]
  > [having 条件]
  > [limit 起始位置,数量]
  > ```

---

### 4. 查询拓展

* 时间：2020.08.06

#### 4.1 分页查询

* 将查询结果显示在多页上

* limit 关键字

* 语法格式：`limit (页码-1)*每页显示个数,每页显示个数;`

  > ```sql
  > select * from students limit (n-1)*m, m; 
  > ```

#### 4.2 连接查询

> 连接查询是指将多个表的结果查询汇总到一起；
>
> 当查询结果的列来源于多张表时，需要将多张表连接成⼀个大的数据集进⾏汇总显示；
>
> 连接后的字段数为多个表字段数之和。

* 连接查询分类：

  * 内连接查询

  * 外连接查询

    > 左连接查询
    >
    > 右连接查询

* 内连接：查询的结果为两个表匹配到的数据，默认是笛卡尔积——全连接

  * 关键字   inner join  

  * 语法格式：

    > `select 字段 from 表1 inner join 表2 where/on 表1.字段 = 表2.字段`
    >
    > ``select 字段 from 表1, 表2 where 表1.字段 = 表2.字段`
    >
    > 注意：on 必须和inner join联用，当inner join省略时，只能通过where来条件筛选

    > ```sql
    > -- inner join ... on
    > -- select ... from 表A inner join 表B;
    > -- 全连接，默认，笛卡尔积
    > select * from students inner join classes;
    > 
    > -- 查询 有能够对应班级的学生以及班级信息
    > select * from students inner join classes where students.cls_id = classes.id;
    > -- 省略 inner join 的写法
    > select * from students, classes where students.cls_id = classes.id;
    > 
    > -- 查询对应的姓名和班级
    > select s.name, c.name from students s inner join classes c on s.cls_id = c.id;
    > 
    > -- 查询对应的学生所有信息和班级信息
    > select s.*, c.name from students s inner join classes c on s.cls_id = c.id;
    > 
    > -- 查询对应的学生所有信息和班级信息，并以班级序号排序，班级相同按学生学号排序
    > select c.name, s.* from students s inner join classes c on s.cls_id = c.id order by c.id, s.id;
    > ```

* 外连接：outer 可以省略

  * 左外连接

    * 关键字：left (outer) join
    * 语法格式：`主表 left join 从表`

    * 查询的结果为两个表匹配到的数据和右表特有的数据，对于右表中不存在的数据使⽤null填充

  * 右外连接       

    * 关键字：right (outer) join
    * 语法格式：`从表 right join 主表`

    * 查询的结果为两个表匹配到的数据和右表特有的数据，对于左表中不存在的数据使⽤null填充

    > ```sql
    > -- left join ....on 
    > -- 查询每位学生对应的班级信息
    > select * from students left join classes on students.cls_id = classes.id ;
    > 
    > -- select * from students right join classes on students.cls_id = classes.id;
    > -- 查询没有对应班级信息的学生
    > -- select ... from xxx as s left join xxx as c on..... where .....
    > -- select ... from xxx as s left join xxx as c on..... having .....
    > select students.*  from students left join classes on students.cls_id = classes.id where classes.id is null;
    > 
    > -- right join   on
    > -- 将数据表名字互换位置，用left join完成
    > select * from students right join classes on students.cls_id = classes.id;
    > ```
    >
    > 注意：不建议使用 select * from students left join classes on students.cls_id=classes.id having classes.id is null;

* 小结：

  > on 必须与xxx join 联用，常用来表示连接条件
  >
  > where可以单独使用，常表示过滤条件
  >
  > having也可以单独使用，表示过滤条件，但是最好使用where
  >
  > 内连接inner join：适用于查看两个表共同有的数据，使用较多
  >
  > 外连接：适用于查询同时保留其中一个表所有数据的情况

#### 4.3 自连接查询

* 自连接查询：同一个表，自己和自己进行内连接

* 属于一种特殊的内连接

* 常用于省市区这种分级所属类型数据的存储与查询

* 使用时要通过别名区分各个表。

  > 数据：aeras.sql
  >
  > 创建数据表
  >
  > ```sql
  > create table areas(
  >  aid int primary key,
  >  atitle varchar(20),
  >  pid int );
  > ```
  >
  > 导入数据  aeras.sql
  >
  > ```sql
  > source areas.sql;
  > ```

* 表的结构说明：

  > 因为省没有所属的省份，所以可以填写为null 
  >
  > 城市所属的省份pid，填写省所对应的编号id 
  >
  > 这就是⾃关联，表中的某⼀列，关联了这个表中的另外⼀列，但是它们的业务逻辑含义是不⼀样的，
  >
  > 城市信息的pid引⽤的是省信息的id 在这个表中，结构不变，可以添加区县、乡镇街道、村社区等信息

* 自连接查询

  > ```sql
  > -- 查询省的个数
  > select count(*) from areas where pid is null;
  > 
  > -- 查询山西省的城市个数
  > select count(*) from areas as city inner join areas as province on city.pid = province.aid where province.atitle = '山西省';
  > -- 查询山西省的所有城市
  > select city.* from areas as city inner join areas as province on city.pid = province.aid where province.atitle = '山西省';
  > select * from areas as city inner join areas as province on city.pid = province.aid where province.atitle = '山西省';
  > 
  > -- 查询上海市的所有区
  > select * from areas as district inner join areas as city on district.pid = city.aid where city.atitle = '上海市';
  > ```

#### 4.4 子查询

* 概念：在⼀个select语句中,嵌⼊了另外⼀个select语句，那么被嵌⼊的select语句称之为子查询语句，外部那个select语句则称为主查询。

* 作用：减少数据库查询操作，从而减少数据库与用户通讯的次数，提高效率。

* 主查询和⼦查询的关系：

  > 子查询是嵌入到主查询中
  >
  > 子查询是辅助主查询的，要么充当条件，要么充当数据源 
  >
  > 子查询是可以独⽴存在的语句，是⼀条完整的select语句

* 子查询的分类

  * 标量子查询：子查询返回的结果是⼀个数据(一行一列) 
  * 列子子查询：返回的结果是⼀列(⼀列多行) 
  * 行子查询：返回的结果是一行(一行多列)
  * 表子查询：返回的结果是一个二维表(多行多列)

  > ```sql
  > -- 子查询
  > -- 标量子查询: 一行一列
  > -- 查询出高于平均身高的信息(height)
  > select * from students where height > (select avg(height) from students);
  > 
  > -- 列子查询: 返回的结果是一列(一列多行)
  > -- 查询学生的班级号能够对应的学生信息
  > select name from students where cls_id in (select id from classes);
  > 
  > -- 行子查询: 返回的结果是一行(一行多列)	
  > select * from students where (height, age) = (select max(height), max(age) from students);
  > ```

* 小结
  * 子查询是⼀个完整的SQL语句 
  * 子查询分为三种	标量、行、列⼦查询 
  * 标量子查询返回的结果一行一列	
  * 列子查询使用格式：主查询 where 条件 in (列子查询)
  * 行子查询使用格式：主查询 where (字段1,2,...) = (行子查询)