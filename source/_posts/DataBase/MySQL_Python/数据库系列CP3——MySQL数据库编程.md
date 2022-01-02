---
title: 数据库系列CP3——MySQL数据库编程
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - DataBase
  - MySQL
tags:
  - MySQL
  - pymysql
abbrlink: 428506b1
date: 2020-08-09 17:13:58
top:
---

---

* 数据库系列CP3——MySQL数据库编程
* CP3主要分享了：
  * 外键约束
  * 视图和事务
  * 数据库三范式
  * E-R模型
  * Pyhton连接MySQL：connection
  * Python操作MySQL：cursor
  * SQL防注入

<!-- more -->

# 数据库系列CP3——MySQL数据库编程

## 三、MySQL数据库编程

* 时间：2020.08.07-2020.08.10

* 主要内容
  * 外键约束
  * 视图和事务
  * 数据库三范式
  * E-R模型
  * Pyhton连接MySQL：connection
  * Python操作MySQL：cursor
  * SQL防注入

### 1. 数据库编程基础

* 时间：2020.08.07

#### 1.1 数据库高阶操作

* SQL练习

  > ```sql
  > create database jingdong charset=utf8;
  > use jingdong;
  > 
  > create table goods(
  >     id int unsigned primary key auto_increment not null,
  >     name varchar(150) not null,
  >     cate_name varchar(40) not null,
  >     brand_name varchar(40) not null,
  >     price decimal(10,3) not null default 0,
  >     is_show bit not null default 1,
  >     is_saleoff bit not null default 0
  > );
  >     
  > insert into goods value(0,'r510vc 15.6英寸笔记本','笔记本','华硕','3399',default,default);
  > insert into goods value(0,'y400n 14.0英寸笔记本电脑','笔记本','联想','4999',default,default);
  > insert into goods value(0,'g150th 15.6英寸游戏本','游戏本','雷神','8499',default,default);
  > insert into goods value(0,'x550cc 15.6英寸笔记本','笔记本','华硕','2799',default,default);
  > insert into goods value(0,'x240 超极本','超极本','联想','4880',default,default);
  > insert into goods value(0,'u330p 13.3英寸超极本','超极本','联想','4299',default,default);
  > insert into goods value(0,'svp13226scb 触控超极本','超极本','索尼','7999',default,default);
  > insert into goods value(0,'ipad mini 7.9英寸平板电脑','平板电脑','苹果','1998',default,default);
  > insert into goods value(0,'ipad air 9.7英寸平板电脑','平板电脑','苹果','3388',default,default);
  > insert into goods value(0,'ipad mini 配备 retina 显示器','平板电脑','苹果','2788',default,default);
  > insert into goods value(0,'ideacenter c340 20英寸一体电脑','台式机','联想','3499',default,default);
  > insert into goods value(0,'vostro 3800-r1206 台式电脑','台式机','戴尔','2899',default,default);
  > insert into goods value(0,'imac me086ch/a 21.5英寸一体电脑','台式机','苹果','9188',default,default);
  > insert into goods value(0,'at7-7414lp 台式电脑 linux','台式机','宏基','3699',default,default);
  > insert into goods value(0,'z220sff f4f06pa工作站','服务器/工作站','惠普','4288',default,default);
  > insert into goods value(0,'poweredge ii服务器','服务器/工作站','戴尔','5388',default,default);
  > insert into goods value(0,'mac pro 专业级台式电脑','服务器/工作站','苹果','28888',default,default);
  > insert into goods value(0,'hmz-t3w 头戴显示设备','笔记本配件','索尼','6999',default,default);
  > insert into goods value(0,'商务双肩背包','笔记本配件','索尼','99',default,default);
  > insert into goods value(0,'x3250 m4机架式服务器','服务器/工作站','ibm','6888',default,default);
  > insert into goods value(0,'商务双肩背包','笔记本配件','索尼','99',default,default);
  > 
  > -- 查询显示
  > select * from goods;
  > 
  > -- 显示电脑的种类
  > select cate_name, count(*) from goods group by cate_name;
  > select distinct cate_name from goods;
  > 
  > -- 分组查询
  > select cate_name, round(avg(price),2) from goods group by cate_name;
  > 
  > -- 查询价格大于平均价格的商品，按价格降序显示
  > select name, price from goods where price > (select round(avg(price),2) as price_avg from goods) order by price desc;
  > ```

* 表子查询——分组后查看全部信息

  > ```sql
  > select * from goods
  > inner join
  > (select cate_name as s_catename, max(price) as price_max from goods group by cate_name) as max_price_goods
  > on goods.cate_name = max_price_goods.s_catename and goods.price = max_price_goods.price_max;
  > ```

* 数据表优化：可变字段单独放入一个新表，并与主表关联，同时根据情况修改字段的名字和类型

  * 创建 goods_cates 表——商品分类表

    > ```sql
    > create table goods_cates(
    >     id int unsigned primary key auto_increment not null,
    >     name varchar(40) not null
    > );
    > ```

  * 插入数据:

    > 通过**子查询**提取表中数据插入新表，**insert into 与 select 联用**
    >
    > 注意：此时不用使用values，同时select语句需要() 

    > ```sql
    > insert into goods_cates(name) (select cate_name from goods group by cate_name);
    > ```

  * 更新 goods 表 cate_name 为 goods_cates.id

    > 通过两个表进行**连接查询**来关联更新，**update set 与 inner join on 联用**

    > ```sql
    > update goods inner join goods_cates on goods.cate_name = goods_cates.name set goods.cate_name = goods_cates.id;
    > ```

  * 修改 cate_name 为 cate_id，类型改为 int

    > ```sql
    > alter table goods change cate_name cate_id int unsigned not null;
    > ```

  * 创建 goods_brands 表——商品品牌表

    > ```sql
    > create table goods_brands(
    >     id int unsigned primary key auto_increment not null,
    >     name varchar(40) not null
    > );
    > 
    > insert into goods_brands(name) (select brand_name from goods group by brand_name );
    > 
    > update goods inner join goods_brands on goods.brand_name = goods_brands.name set goods.brand_name = goods_brands.id;
    > 
    > alter table goods change brand_name brand_id int unsigned not null;
    > ```

#### 1.2 外键约束

* 外键的概念

  > 一个表的主键在另外一个表中出现，在另外一个表中称为外键

* 外键的作用

  > 表间的数据插入、更新的时候的一种约束，用来防止无效信息的插入

* 外键约束：关键字——foreign key

  * foreign key约束指定某⼀个列或⼀组列作为外部键，其中包含外部键的表称为子表，包含外键所引用的键的表称为⽗表；
  * 对外键字段的值，在更新和插⼊时进行和引用的表中字段数据进行对比；
  * 只有 innodb 数据库引擎⽀持外键约束。

* 外键语法

  * 已有数据表，插入或创建外键约束的字段

    > 语法格式：`alter table 子表名 add foreign key (字段名) references 父表名 (字段名);`
    >
    > ```sql
    > alter table goods add foreign key (cate_id) references goods_cates (id);
    > alter table goods add foreign key (brand_id) references goods_brands (id);
    > ```

  * 新建数据表的同时，创建外键约束

    > 在创建表时添加外键约束语句
    >
    > 语法格式：`foreign key(brand_id) references goods_brand(id)`
    >
    > ```sql
    > create table goods_test(
    >     id int unsigned primary key auto_increment not null,
    >     name varchar(40) not null,
    >     price decimal(5,2),
    >     cate_id int unsigned,
    >     brand_id int unsigned,
    >     foreign key(cate_id) references goods_cates(id),
    >     foreign key(brand_id) references goods_brands(id)
    > );
    > ```

  * 删除外键约束：drop

    > 语法格式：`alter table 表名 drop foreign key 外键名;`
    >
    > ```sql
    > -- 获取有外键约束的字段的名称
    > show create table goods_test;
    > -- 删除外键约束
    > alter table goods_test drop foreign key goods_test_ibfk_1;
    > ```
    >
    > 

  * 注意：

    > 外键名需要通过show来查看，一般格式为 `表名_ibfk_序号`
    >
    > 进行外键约束的两个表间的字段需要逻辑上对应，同时数据类型相同
    >
    > 如果对已有数据表中的字段创建外键约束，需要预先将该表中不匹配外键约束的数据删去，以免报错
    >
    > 使用外键约束会极大的降低表更新的效率,	所以在追求读写效率优先的场景下⼀般很少使用外键。

#### 1.3 数据库引擎

* 概念：

  > 数据库存储引擎是数据库底层软件组织；
  >
  > 不同的存储引擎提供不同的存储机制、索引技巧、锁定水平等功能；
  >
  > 使用不同的存储引擎，还可以获得特定的功能。

* 查看引擎

  > ```sql
  > -- 查看数据库服务器支持的表存储引擎
  > show engines;
  > -- 查看某个数据表的引擎是什么类型
  > show create table goods ;
  > ```
  >
  > 查看表的创建语句，可以看到 engine=innodb

* 注意：

  > 只有数据表的引擎类型是innodb类型时，才可以使用外键约束和事务

#### 1.4 视图

* 视图的概念

  > 视图是一个虚拟表，仅仅支持查询，把复杂SQL语句的功能封装起来的一个虚表，为了简化操作；

* 视图特点

  > 视图是对若干张基本表的引用，是⼀张虚表，只查询语句执行结果的字段类型和约束，
  >
  > 不存储具体的数据  (基本表数据发生了改变，视图也会跟着改变)
  >
  > 当用户使用视图的时候，视图会从基本表中取出数据进行查询 

* 适用场景与作用

  > 查询多个表连接的数据，如商品名称、商品分类和商品品牌时，通过视图封装对多个表的复杂操作，从而简化操作
  >
  > 通过视图可以对用户展示指定字段从而屏蔽其他字段数据，更加安全

  > 基本方法：
  >
  > ```sql
  > select g.name as Name, gc.name as Cate, gb.name as Brand from goods as g 
  >     inner join goods_cates as gc on g.cate_id = gc.id
  >     inner join goods_brands as gb on g.brand_id = gb.id;
  > ```

* 视图语法

  * 视图创建

    > 语法格式：`create view 视图名称 as select语句;`
    >
    > ```sql
    > create view v_goods_info as 
    >     select g.name as Name, gc.name as Cate, gb.name as Brand from goods as g 
    >         inner join goods_cates as gc on g.cate_id = gc.id
    >         inner join goods_brands as gb on g.brand_id = gb.id;
    > ```

    注意：

    > 视图同基本表的表结构一致
    >
    > 视图在保存基本表字段信息的时候字段不能重复 	
    >
    > 因此需要对select结果集中的字段进行重命名——别名

  * 视图使用

    > 与查询基础表的select语句一样
    >
    > ```sql
    > select * from v_goods_info;
    > -- 条件查询
    > select * from v_goods_info where Name = 'xxx'
    > ```

  * 视图查看

    > ```sql
    > show tables;
    > ```

  * 视图删除

    > ```sql
    > drop view v_goods_info;
    > -- 对比
    > drop table goods_test;
    > ```

#### 1.5 事务

* 事务概念：事务Transaction

  > 事务是指作为一个基本工作单元执行的一系列SQL语句的操作，要么全成功，要不全失败

* 作用：要么完全地执行，要么完全地都不执行

* 事务的四大特征 ACID：

  * A ：原子性 ，事务的操作是原子的，不能分隔

    > ⼀个事务必须被视为⼀个不可分割的最小工作单元，整个事务中的所有操作要么全部提交成功， 要么全部失败回滚，对于⼀个事务来说，不可能只执行其中的⼀部分操作

  * C ：一致性，双方的结果要一致

    > 数据库总是从⼀个⼀致性的状态转换到另⼀个⼀致性的状态

  * I  ：隔离性， 同一时间只有一个事务在操作

    > 通常来说，⼀个事务所做的修改在最终提交以前，对其他事务是不可见的

  * D ：持久性， 操作完成结果持久不变

    > ⼀旦事务提交，则其所做的修改会永久保存到数据库

* 事务语法

  * 开启事务

    > ```sql
    > begin;
    > -- 或者
    > start transaction;
    > ```

  * 提交事务(完成)

    > ```sql
    > commit;
    > ```

  * 回滚事务(撤销)

    > ```sql
    > rollback;
    > ```

  * 事务流程小结——如：银行存储

    > ```sql
    > start transaction;
    > 
    > -- 操作 update、insert、delete
    > select balance from checking where customer_id = 10233276;
    > update checking set balance = balance - 200.00 where customer_id = 10233276;
    > update savings set balance = balance + 200.00 where customer_id = 10233276; 
    > 
    > commit;
    > -- 或者
    > rollback;
    > ```

* 验证事务的ACID特性

  > 通过两个终端模拟两个用户来验证

  * 验证I 隔离性

    > 终端1修改表的数据，在commit前，通过终端2修改表的同一个数据
    >
    > 终端2进⼊等待状态(因为终端1并没有进行commit)此事就可以说明事务的隔离性特点，多个事务之 间相互不可见、不影响

* 小结

  > 原⼦性强调事务中的多个操作是⼀个整体 
  >
  > ⼀致性强调数据库中不会保存不⼀致状态 
  >
  > 隔离性强调数据库中事务之间相互不可见
  >
  > 持久性强调数据库能永久保存数据，⼀旦提交就不可撤销

  > 在mysql命令行中会自动提交事务，所以当insert语句执⾏完成后没有commit，数据库也看到了提交的数据
  >
  > 如果不需要mysql命令行自动提交，键⼊`set autocommit=0`
  >
  > 不可撤销的操作(隐式提交):	除了对表数据 insert/update/delete 语句之外的绝大多数语句都是不能撤销的，如数据库、表结构的操作

---

### 2. 数据库设计

* 时间：2020.08.08

* 可以参考：[58到家数据库设计规范](https://mp.weixin.qq.com/s/Yjh_fPgrjuhhOZyVtRQ-SA?)

#### 2.1 数据库三范式

* 概念

  > 设计数据库的规范、原则，被称为范式(Normal Form) ，可以减少数据库的冗余数据；
  >
  > 目前有迹可寻的共有8种范式，⼀般需要遵守3范式即可。

* 三范式

  * 1NF：字段必须是原子的，不可分割

  * 2NF：满足1NF，必须有主键， 非主键字段必须完全依赖主键，而不能部分依赖 (解决办法：分表)

    > 当一个表的主键有多个字段时，即为复合主键时，需要表中其他字段完全依赖复合主键的每个字段

  * 3NF：满足2NF，所有非主键字段必须直接依赖主键，不能存在传递依赖的情况。

    > 即不能存在：非主键列A依赖于非主键列B，非主键列B依赖于主键的情况。

#### 2.2 数据库设计实例

* 设计一个简易电商类的数据库

  > 包含有商品信息、订单信息和顾客(用户)信息

* 商品信息

  * 商品详情表：goods
  * 商品分类表：goods_cates
  * 商品品牌表：goods_brands

* 顾客信息

  > 顾客信息表：customer
  >
  > ```sql
  > create table customer(
  >     id int unsigned primary key auto_increment not null,
  >     name varchar(30) not null,
  >     addr varchar(100),
  >     tel varchar(11) not null
  > );
  > ```

* 订单信息

  * 订信息单表：orders

    > ```sql
    > create table orders(
    >     id int unsigned primary key auto_increment not null,
    >     order_date_time datetime not null,
    >     customer_id int unsigned,
    >     foreign key(customer_id) references customer(id)
    > );
    > ```

  * 订单详情表：order_detail

    > ```sql
    > create table order_detail(
    >     id int unsigned primary key auto_increment not null,
    >     order_id int unsigned not null,
    >     goods_id int unsigned not null,
    >     quantity tinyint unsigned not null,
    >     foreign key(order_id) references orders(id),
    >     foreign key(goods_id) references goods(id)
    > );
    > ```

#### 2.3 E-R模型

* E-R模型简介

  > E-R模型即E-R图。
  > E-R图即实体-联系图(Entity Relationship Diagram)，是指提供了表示实体型、属性和联系的方法，用来描述现实世界的概念模型，主要用来描述数据库中表结构。

* E-R模型使用场景

  > 关系型数据库：关系模型的基础上，根据产品的设计策划，抽取出来模型与关系，制定出表结构，这是项⽬开始的第⼀步；
  > 在设计阶段⼀般使⽤E-R模型进行建模。有很多设计数据库的软件，常用的如power designer，db desinger等，这些软件可以直观的看到实体及实体间的关系；
  >
  > 关系模型：用⼆维表的形式表示实体和实体间联系的数据模型。

* E-R模型的组成元素

  > E-R图用：实体、联系和属性这3个概念来描述现实问题，分为这三种元素。

  * 实体型(Entity)：具有相同属性的实体具有相同的特征和性质，用实体名及其属性名集合来抽象和刻画同类实体；在E-R图中用**矩形**表示，矩形框内写明实体名。如：电商购物系统中用户、购物车、订单。

  * 属性(Attribute)：实体所具有的某⼀特性，⼀个实体可由若干个属性来刻画。在E-R图中用**椭圆**表示，并用无向边将其与相应的实体连接起来。如用户的ID、用户名、密码、昵称、身份证号等。

  * 联系(Relationship)：实体彼此之间相互连接的方式称为联系，也称为关系。

    > 联系可分为3种类型：⼀对⼀、⼀对多、多对多
    >
    > 关系也是⼀种数据，需要通过⼀个字段存储在表中。

  * 表间联系(关系)：
    * 一对一：一个表中的一条数据，能够和另外一个表的唯一一条数据对应（人和常住地址）

    * 一对多：一个表中的一条数据，能够和另外一个表的多条数据对应（一个班有多个学生）

    * 多对多：一个表中的一条数据，能够和另外一个表的多条数据对应，相反也成立。（学生和课程）

* E-R模型中不同类型联系的储存方法

  * 实体A对实体B为1对1，则在表A或表B中创建⼀个字段，存储另⼀个表的主键值；
  * 实体A对实体B为1对多，在表B中创建⼀个字段，存储表A的主键值；
  * 实体A对实体B为多对多，新建⼀张表C，这个表只有两个字段，⼀个⽤于存储A的主键值，⼀个用于存储B的主键值。

* 注意：

  > 多对多关系时，需要建立中间表(附加表)来储存数据。

---

### 3. Python连接操作数据库

* 时间：2020.08.09

* pymysql模块
* connection连接对象
* cursor 游标对象(操作数据库)

#### 3.1 Python访问MySQL流程

> Python DB API 访问数据库

* 导入pymysql模块
* 建立连接：创建connection连接对象
* 获取游标：创建cursor游标对象
* 执行数据库操作(Curd) ：execute
* 获取结果：fetchone、fetchall
* 关闭游标
* 关闭连接

#### 3.2 Python连接数据库

* 导入模块pymysql

* 建立连接

  * 语法格式：`pymysql.connect(参数列表)`

  * 返回连接对象，建立与数据库的连接

  * 参数列表：

    * host：连接的mysql主机，如果本机是'localhost' 
    * port：连接的mysql主机的端口，默认是3306 
    * database：数据库的名称
    * user：连接的用户名 
    * password：连接的密码
    * charset：通信采用的编码⽅式，推荐使用utf8

  * 连接对象

    > ```python
    > import pymysql
    > conn = pymysql.connect(host='localhost', port=3306, user='root', password='******', database='jingdong', charset='utf8')
    > # 获取游标
    > conn.cursor()
    > 
    > # 提交数据
    > conn.commit()
    > # 回滚数据
    > conn.rollback()
    > 
    > # 关闭连接
    > conn.close()
    > ```

* 获取游标对象

  * 语法格式：`连接对象.cursor()`
  * 返回Cursor对象，用于执行sql语句并获得结果

#### 3.3 Python操作数据库(Curd)

* 操作数据库

  * 语法格式：`游标对象.execute(sql语句 [,parameters])`

  * 主要用于执行select、insert、 update、delete语句(crud)，也可以执行create、alter、drop等语句

  * 返回数据

    > 返回的数据类型为int；
    >
    > 当SQL语句为查询语句时，返回数据表的总行数(总记录数)；
    >
    > 当SQL语句为增删改语句时，返回受影响的行的个数；
    >
    > 当SQL语句为create、alter、drop语句时，返回None；

* 获取结果

  * 语法格式：`游标对象.fetchone()`

    > 获取结果集的一条记录(一行)，返回的数据类型为元组

  * 语法格式：`游标对象.fetchall()`

    > 获取结果集的所有记录(所有行)，返回的数据类型为元组
    >
    > 一行构成⼀个元组，再将这些元组装入⼀个元组返回	

* 数据库连接并查询

  > ```python
  > # Python连接、操作MySQL数据库
  > 
  > # 1. 导入模块
  > # from pymysql import connect
  > import pymysql
  > 
  > # 2. 建立连接，创建连接对象
  > conn = pymysql.connect(host='localhost', port=3306, user='root', password='******', database='jingdong', charset='utf8')
  > 
  > # 3. 创建游标对象
  > cur = conn.cursor()
  > 
  > # 4. 操作数据库
  > sql = 'select * from goods;'
  > row_count = cur.execute(sql)
  > print('MySQL数据库受到操作的行数为:%d' % row_count)
  > 
  > # 5. 获取结果
  > result_one = cur.fetchone()
  > print(result_one)
  > 
  > result_list = cur.fetchall()
  > print(result_list)
  > for line in result_list:
  >     print(line)
  > 
  > # 6. 关闭游标和连接
  > cur.close()
  > conn.close()
  > ```

* 数据库增删改，以及创建修改删除数据表结构

  > ```python
  > # 1. 导入模块
  > # from pymysql import connect
  > import pymysql
  > 
  > # 2. 建立连接，创建连接对象
  > conn = pymysql.connect(host='localhost', port=3306, user='root', password='*****', database='jingdong', charset='utf8')
  > 
  > # 3. 创建游标对象
  > cur = conn.cursor()
  > 
  > # 4. 操作数据库
  > # 4.1 SQL查询
  > sql0 = "create table goods_cates_test(" \
  >        "id int unsigned primary key auto_increment not null," \
  >        "name varchar(30) not null);"
  > sql1 = "alter table goods_cates_test add test int not null;"
  > sql2 = 'select * from goods_cates_test;'
  > sql3 = "insert into goods_cates_test(name) (select cate_id from goods group by cate_id)"
  > sql4 = "update goods inner join goods_cates_test as gct " \
  >        "on goods.cate_id = gct.name set goods.cate_id = gct.id;"
  > sql5 = "delete from goods_cates_test where id > 7 "
  > 
  > # 4.2 执行SQL
  > row_count = cur.execute(sql5)
  > print('MySQL数据库受到操作的行数为:%d' % row_count)
  > 
  > # 5. 提交数据或回滚
  > # conn.commit()
  > conn.rollback()
  > 
  > # 6. 关闭游标和连接
  > cur.close()
  > conn.close()
  > ```

* 注意

  * cur.execute()语句的返回值在SQL语句不同时，结果不同

    > 当SQL语句为查询语句时，返回数据表的总行数(总记录数)；
    >
    > 当SQL语句为增删改语句时，返回受影响的行的个数；
    >
    > 当SQL语句为create、alter、drop语句时，返回None；

  * 只有SQL语句为查询语句时，才需要fetchone、fetchall语句来输出查询结果

  * 当SQL语句为增删改语句时，必须执行cunn.commit()语句来提交数据，才会改变数据库的数据

#### 3.4 SQL防注入

* SQL注⼊概念

  > 用户通过传入恶意数据，从而获取到数据库中大量数据，造成数据泄漏

* 产⽣原因：

  > 后台将用户提交的带有恶意的数据和SQL进行字符串方式的拼接，从而影响了SQL语句的语义，最终导致数据泄露的现象。
  >
  > ```python
  > # 存在SQL注入隐患的写法：字符串拼接
  > # 用户自定义查询内容
  > input_name = input('请输入要查询的数据的名称：')
  > 
  > # SQL查询
  > sql = "select * from goods_cates_test where name = '%s';" % input_name
  > 
  > row_count = cur.execute(sql)
  > print('MySQL数据库受到操作的行数为:%d' % row_count)
  > 
  > # 查询结果
  > result_list = cur.fetchall()
  > print(result_list)
  > for line in result_list:
  >     print(line)
  > ```
  >
  > 输入正常name时不会产生SQL注入，但如果输入' or 1 or' 则会SQL注入，数据泄露

* 解决方法：参数化列表

  > sql中需要变化的地方，可以占位符 %s %d...
  >
  > 将SQL语句中的所有数据参数存在⼀个列表中，传递给execute函数的第⼆个参数
  >
  > ```python
  > # 解决SQL注入问题
  > # 用户自定义查询内容
  > input_name = input('请输入要查询的数据的名称：')
  > 
  > # 1. 创建一个列表，用来传递用户输入的参数
  > params = [input_name]
  > 
  > # SQL查询
  > sql = "select * from goods_cates_test where name = %s;" 
  > 
  > # 把列表传递给 execute(sql, 列表)
  > row_count = cur.execute(sql，params)
  > print('MySQL数据库受到操作的行数为:%d' % row_count)
  > 
  > # 查询结果
  > result_list = cur.fetchall()
  > print(result_list)
  > for line in result_list:
  >     print(line)
  > ```

* 注意：

  * 如果要是有多个参数，需要进行参数化
  * params = [数值1, 数值2....]，此时sql语句中有多个%s即可
  * SQL 可以出现多个占位符，后续列表中元素的个数要与之对应





