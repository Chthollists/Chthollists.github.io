---
title: 数据库系列CP1——SQL语言基础语法
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Middleware
  - MySQL
tags:
  - MySQL
abbrlink: 4fd02982
date: 2020-08-04 00:13:14
top:
---

---

* 数据库系列CP1——SQL语言基础语法
* CP1主要分享了：
  * 数据库的基本概念、数据库管理系统RDBMS
  * MySQL数据库的环境搭建
  * SQL基础语法：数据库操作、数据表操作、数据(字段、记录)的增删改查(Crud)

<!-- more -->

# 数据库系列CP1——SQL语言基础语法

## 一、数据库(DB)基本知识

主要内容

* 数据库基础概念
* SQL语言
* MySQL环境搭建
* MySQL操作与应用

### 1. 数据库基础概念

* 时间：2020.07.26

* 数据库概念：一些特殊格式的文件的集合

* 数据库的作用:用来存储各种数据

  * 数据库的使用优势：

    > 持久化存储 
    >
    > 读写速度极⾼
    >
    > 保证数据的有效性 
    >
    > 对程序⽀持性⾮常好，容易扩展

* 分类

  * 关系型数据库RDBMS (Relational Database Management System)

    > 基于关系模型(二维表模型)建立，用二维表进行数据存储的数据库

    >MySQL ：适用于中小型网站项目，体积⼩、速度快、成本低、开源
    >
    >Oracle ：更安全、可恢复，适用于大型项目，银⾏、电信、商城等项⽬
    >
    >SQLite：轻量级数据库，适用于移动端的项目开发（如通讯录），可以无需联网  
    >
    >Microsoft SQL Server ：只能在微软的Windows系统使用

  * 非关系型数据库NoSQL(Not Only SQL)

    > 不是基于二维表，基于key-value 方式存储，强调Key-Value	存储和⽂档数据库的优点
    >
    > MongoDB   
    >
    > Redis

------

### 2. 数据库管理系统(RDBMS)

* 时间：2020.07.27

#### 2.1 数据库管理系统

* 数据库管理系统的介绍

  > 数据库管理系统 DBMS (Database Management System)是为管理数据库而设计的软件系统。

* DBMS组成 ：三个部分 

  * 数据库⽂件集合.	主要是⼀系列的数据⽂件,	作⽤是存储数据. 
  * 数据库服务端.	主要负责对数据⽂件以及⽂件中的数据进⾏管理
  * 数据库客户端.	主要负责和服务端通信,	向服务端传输数据或者从服务端获取数据。

* DBMS与数据库DB的关系

  > 数据库客户端通过SQL语句告诉服务端，客户端想要做什么
  >
  > 服务端和数据⽂件⼀般都在同⼀台机器上，直接可以读写数据⽂件.

#### 2.2 SQL语句

* SQL语句介绍(Structured Query Language)

  > SQL是结构化查询语言，是⼀种⽤来操作RDBMS的数据库语⾔。
  >
  > 当前⼏乎所有关系型数据库RDBMS都⽀持使⽤SQL语⾔进⾏操作

* SQL作⽤

  > 实现数据库客户端和服务端之间的通信.

* SQL格式

  > 其表现形式为：带有⼀定格式的字符串。

* SQL分类

  * **DQL**：数据查询语⾔，⽤于对数据进⾏查询，如select 
  * **DML**：数据操作语⾔，对数据进⾏增加、修改、删除，如insert、udpate、delete 
  * TPL：事务处理语⾔，对事务进⾏处理，包括begin、transaction、commit、rollback 
  * DCL：数据控制语⾔，进⾏授权与权限回收，如grant、revoke 
  * **DDL**：数据定义语⾔，进⾏数据库、表的管理等，如create、drop 
  * CCL：指针控制语言，通过控制指针完成表的操作，如declare、cursor

  > 对于web程序员来讲，重点是数据的crud（增删改查），
  >
  > 必须熟练编写DQL、DML，能够编写 DDL完成数据库、表的操作，
  >
  > 其它语⾔如TPL、DCL、CCL了解即可。

#### 2.3 关系型数据库中核心元素

* 数据行(记录) 

* 数据列(字段) 

* 数据表(数据行的集合)：： 二维表（行、列）

* 数据库(数据表的集合)：数据库系统

  > RDBMS由若干个数据表组成，数据表由行和列组成，
  >
  > 每一行是一个完整的数据实体，称为记录---row
  >
  > 每一列数据类型相同，称为字段---field

* 保存数据的基本步骤

  * 创建数据库
  * 在数据库中创建数据表
  * 向数据表中插⼊多条记录数据

------

### 3. MySQL数据库环境搭建

* 时间：2020.07.28

#### 3.1 MySQL环境搭建

* Linux系统(Ubuntu)

  * MySQL 数据库管理系统的安装

    > ```ini
    > sudo apt-get install mysql-server
    > ```

  * MySQL 数据库的配置

    > 配置文件：`/etc/mysql/mysql.conf.d/mysqld.cnf`

  * MySQL 数据库操作

    * 启动   `sudo service mysql start`
    * 查看：`ps -ajx | grep mysql`
    * 重启:  `sudo service mysql restart`
    * 停止：`sudo service mysql stop`

* Windows系统

  > 参考教程
  >
  > https://www.cnblogs.com/yunlongaimeng/p/12558638.html
  >
  > https://blog.csdn.net/qq_44697035/article/details/97395349

  * 1.1 下载MySQL安装包

    > [MySQL官网](https://dev.mysql.com/downloads/mysql/)
    >
    > [国内镜像](http://mirrors.sohu.com/mysql/MySQL-8.0/)

  * 1.2 解压安装包

    > 路径：`F:\CodeTools\MySQL\mysql-8.0.21-winx64`

  * 1.3 配置环境变量

    > 变量名：MYSQL_HOME
    >
    > 变量值：`F:\CodeTools\MySQL`

  * 1.4 更新/设置配置信息

    > 文件存放路径：`F:\CodeTools\MySQL\mysql-8.0.21-winx64\my.ini`

    >```ini
    >[mysql]
    ># 设置mysql客户端默认字符集
    >default-character-set=utf8
    >[mysqld]
    ># 设置3306端口
    >port = 3306
    ># 设置mysql的安装目录
    >basedir= F:\CodeTools\MySQL\mysql-8.0.21-winx64
    ># 设置mysql数据库的数据的存放目录
    >datadir= F:\CodeTools\MySQL\mysql-8.0.21-winx64\data
    ># 允许最大连接数
    >max_connections=20
    ># 服务端使用的字符集默认为8比特编码的latin1字符集
    >character-set-server=utf8
    ># 创建新表时将使用的默认存储引擎
    >default-storage-engine=INNODB
    >```

  * 2.1 以管理员身份运行cmd 

    > 进入 `F:\CodeTools\MySQL\mysql-8.0.21-winx64\bin` 路径

  * 2.2 生成数据库数据存放目录data

    > ```bash
    > $ mysqld --initialize-insecure --user=mysql
    > ```

  * 2.3 安装MySQL作为window服务,自动启动

    > ```bash
    > $ mysqld -install
    > ```

  * 执行安装命令mysqld --initialize --console ——等价于2.1~2.3

    > ```bash
    > $ mysqld --initialize --console 
    > 
    > --initialize //创建数据文件目录和mysql系统数据库 产生随机root密码
    > --console //写错误日志到console window平台
    > ```

  * 2.4 启动MySQL服务

    > ```bash
    > $ net start MySQL
    > ```

  * 2.5 登录mysql

    > 因为之前没设置密码，所以密码为空，不用输入密码，直接回车即可

    >```bash
    >$ mysql -u root -p
    >```

  * 2.6 查询用户密码

    > ```sql
    > mysql> select host,user,authentication_string from mysql.user;
    > ```

  * 2.7 **设置（或修改）root用户密码**

    > ```sql
    > mysql> use mysql
    > # password为新密码
    > mysql> alter user 'root'@'localhost' identified with mysql_native_password by 'password';
    > 
    > 保存修改
    > mysql> flush privileges;  
    > ```

  * 2.8 退出

    > ```sql
    > quit
    > 或者
    > exit
    > ```

  * 2.9 数据库备份与恢复

    * 备份：导出SQL脚本 `/bin/`目录下

      > ```bash
      >    $ mysqldump -u root -p pyhton_test > python_test.sql
      > ```

    * 恢复：先新建数据库，再导入备份文件

      > 先在MySQL中创建一个数据库，如python_test，再将备份文件导入到该数据库中。
      >
      > ```bash
      > $ mysql -u root -p python_test < python_test.sql
      > ```

  * 其他：停止服务、卸载MySQL、查看版本、查看默认的密码认证插件、查看当前所有用户绑定的认证插件、创建新用户

    > ```sql
    > - net stop mysql
    > mysqld -remove
    > 
    > 查看版本
    > mysql> select version();
    > 查看默认的密码认证插件：
    > mysql> show variables like 'default_authentication_plugin';
    > 查看当前所有用户绑定的认证插件：
    > mysql> select host,user,plugin from mysql.user;
    > 创建新用户
    > mysql> CREATE USER root@'%' IDENTIFIED BY '123456';
    > ```

#### 3.2 数据库的完整性

* 完整性的作用： 保证数据的正确性。

  > 系统在更新、插⼊或删除等操作时都要检查数据的完 整性，核实其约束条件，即关系模型的完整性规则。

* 完整性约束：四类

  > 约束作⽤是保证数据的完整性和⼀致性，分为表级约束和列级约束。

  * 实体完整性

    > 实体完整性指表中**行**的完整性。主要⽤于保证操作的数据(记录)⾮空、唯⼀且不重复。
    >
    > 即实体完整 性要求每个关系表有且仅有⼀个主键，每个主键值必须唯⼀，⽽且不允许为“空”或重复。

    > 主键约束 ：不能为空，也不能重复     primary key
    >
    > 唯一约束 ：能为空，但是不能重复     unique key 

  * 域完整性约束

    > 域完整性是指数据库表中的**列**必须满⾜某种特定的数据类型或约束。
    >
    > 约束包括取值范 围、精度等。表中的check、foreign key约束和default、not null定义都属于域完整性的范畴。

    > 非空约束:   该字段信息不能为空  not null
    >
    > 默认约束：这个字段可以设置默认值  default 

  * 参照完整性

    > 参照完整性属于表间规则。
    >
    > 对于永久关系的相关表，在更新、插⼊或者删除记录时，如果只改其 ⼀，就会影响数据的完整性。
    >
    > 如删除⽗表的某记录后，⼦表的相应记录未删除，致使这些记录称为孤立记录。对于更新、插⼊、删除表间数据的完整性，统称为参照完整性。

    > 外键约束:   建立表和表外键的关系 foreign key

  * 用户定义完整性

    > ⽤户定义完整性是对数据表中字段属性的约束，⽤户定义完整性规则也称域完整性规则。包括字段的值域、字段类型和字段的有效规则等约束，是由确定关系结构时所定义的字段属性决定的。

    > 字段的值必须是设定的范围之内。  

------

### 4. MySQL数据库客户端

* 时间：2020.07.29

#### 4.1 MySQL命令行客户端

* Linux系统

  * 安装MySQL数据库客户端

    > ```bash
    > sudo apt-get install mysql-client
    > ```

  * 连接MySQL服务器

    > ```bash
    > mysql -u root -p
    > ```

* Window系统

  > 安装MySQL数据库时自动安装客户端
  >
  > 相关操作参考MySQL环境搭建中Windows系统部分的2.5及以后

#### 4.2 Navicat Premium 安装

> 图形化MySQL数据库的开发工具

* Ubuntu 版

  > ```bash
  > # 解压
  > tar	zxvf navicat112_mysql_cs_x64.tar.gz
  > # 启动
  > ./start_navicat
  > # 乱码解决，打开start_navicat⽂件
  > # 将export LANG="en_US.UTF-8"改为export LANG="zh_CN.UTF-8"
  > 
  > # 破解：删除⽤户⽬录下的.navicat64⽬录
  > cd ~ 
  > rm -r .navicat64
  > ```

* Windows 版

  > [软件及破解文件下载地址](https://pan.baidu.com/s/1GPEUyeERNJexT4kT2FuO0w)

#### 4.3  Navicat Premium 使用

* 建立连接

  连接  ---> MySQL ---> 主机 、用户名、端口、密码 --> 确定

* 创建数据库
  1）连接

  2）右键 --> 创建数据库  --> 名称、字符集utf8、排序规则: utf8_general_ci

* 创建数据表

  1）打开数据库

  2）右键创建表

  3）输入字段 （光标移动）

  4）保存，输入表名

* 修改数据表

  1) 右键表名  ---> 设计表  ---> 保存

* 打开表

  > 双击表名，打开表

* 添加数据

  > 单击单元格，可以添加
  >
  > 自动增长列，不需要写
  >
  > 保存： 1） ctrl+s       2） 底部 ✅

* 修改表数据

  > 双击单元格，修改内容

* 删除数据

  > 选中某行 ，  点击底部  "- " 号

* 导出 、备份、导入

  > 导出： 右键数据库 --> 转储SQL ---> 数据和结构
  >
  > 导入：
  >
  > 1） 新建数据库
  >
  > 2）右键 运行 sql文件

------

### 5. SQL基础语法

* 时间：2020.07.30

#### 5.1 MySQL数据类型

> 选择合适的类型保存数据，优化数据库
>
> 好处：1）节省存储空间  2）提升查询效率

* 数值

  * 整形

    > int  4个字节 小型项目
    >
    > tinyint  1个字节
    >
    > -128~127 / 0~255 适用于年龄、状态号 

  * 浮点型

    > float 4字节，精度：小数点后6位有效    
    >
    > double  8字节，精度：小数点后16位有效   
    >
    > float(M,D) 

  * 定点数

    > decimal 浮点型，用来更加精确的保存小数，适用于价格
    >
    > decimal (M,D)

* 字符串

  > 知道字符串长度时使用char，不知道使用varchar
  >
  > 尽可能用varchar

  * char:   255个字符, 固定大小
  * varchar: 65535 字节，不固定（可变、更省空间 utf8---3n+1）
  * text: 64Kb
  * mediumtext：16Mb

* 枚举

  > 把可能取到的值列出来，适用于性别、星期、⽉份、布尔
  >
  > 最多可以存储65535个值，1~2字节

  * enum()   1-2个字节存储数据
  * enum("值1","值2",.....)

* 时间

  * date    年月日

  * datetime 年月日 时分秒   

    > 5字节，范围：'1000-01-01  00:00:00' 到 '9999-12-31  23:59:59'

  * timestamp 年月日 时分秒  

    > 4字节，范围：'1970-01-01  00:00:01' 到 '2038-01-19  03:14:07'

  * time  时分秒

#### 5.2 SQL基础语法---数据库操作

> SQL语句最后需要有分号 `;` 结尾
>
> 属于DQL、DDL语句

* 登录

  > mysql -u 用户名 -p

* 退出

  * exit、quit、ctrl+d

* 查看数据库的版本

  > ```sql
  > select version();
  > ```

* 查询当前时间

  > ```sql
  > select now();     
  > ```

* 数据库操作 

  * 查看：show、select

    > 语法格式：`show create database 数据库名;`
    >
    > ```sql
    > -- 查看当前使用的数据库
    > mysql> select database();
    > -- 查看所有数据库
    > mysql> show databases;
    > 
    > -- 查看创建数据库的SQL语句
    > mysql> show create database python_db;
    > ```

  * 创建：create

    > 语法格式：`create database 数据库名 charset=utf8;`
    >
    > ```sql
    > -- 创建数据库(python_db)
    > mysql> create database python_db;
    > 
    > -- 指定编码的数据库创建
    > mysql> create database python_db1 charset=utf8;
    > ```

  * 使用：use

    > 语法格式：`use 数据库的名字;`
    >
    > ```sql
    > -- 使用数据库
    > mysql> use python_db1;
    > ```

  * 删除：drop

    > 语法格式：`drop database 数据库名;`
    >
    > ```sql
    > -- 删除数据库
    > mysql> drop database python_db;
    > ```

#### 5.3 SQL基础语法---数据表结构的操作

> 表结构的操作主要为：创建、查看、删除表；添加、重命名、删除字段和修改字段类型。
>
> 在开发过程中不需要高频度的操作表结构，所以该章节的目的就是让我们能够在需要的时候 根据这些语法写出复合要求的SQL语句即可。
>
> 属于DDL语句

* 表结构的创建：create

  > 语法格式： `create table 数据表名字 (字段 类型 约束[, 字段 类型 约束]);`
  >
  > ```sql
  > -- 创建表---格式
  > 
  > create table table_name( 
  > 字段名称 数据类型 可选的约束条件,
  > column1 datatype contrai,
  > column2 datatype,
  > column3 datatype,
  > ..... 
  > columnN datatype,
  > -- 主键说明可以放在字段中单独说明，也可以放在最后统⼀说明 
  > PRIMARY KEY(one or more columns)
  > );
  > 
  > -- int unsigned 无符号整形
  > -- auto_increment 表示自动增长
  > -- not null 表示不能为空
  > -- primary key 表示主键
  > -- default 默认值
  > 
  > -- 创建 classes 表(id、name)
  > create table classes(
  > 	id int unsigned primary key auto_increment,
  > 	name varchar(10) not null,
  > 	num tinyint
  > );
  > 
  > -- 创建 students 表(id、name、age、high (decimal)、gender (enum)、cls_id)
  > create table students(
  > 	id int unsigned primary key auto_increment,
  > 	name varchar(20) not null,
  > 	age tinyint(1) ,
  > 	hight decimal(3,2),
  > 	gender enum("男","女","妖"),
  > 	cls_id int unsigned
  > );
  > ```

* 表结构的查看

  * 查看表：show

    > 语法格式：`show create table 表名字;`
    >
    > ```sql
    > -- 查看表
    > show tables;
    > 
    > -- 查看表的创建语句
    > show create table students;
    > ```

  * 查看字段：desc

    > 语法格式：`desc table_name;`
    >
    > ```sql
    > -- 查看字段
    > desc classes;
    > ```

* 表结构的修改---字段的操作

  * 添加字段：alter+add

    > 语法格式：`alter table 表名 add 列名 类型;`
    >
    > ```sql
    > -- 修改表-添加字段 生日 datatime
    > alter table students add birthday datetime;
    > ```
    >

  * 修改字段类型：alter+modify

    > 语法格式：`alter table 表名 modify 列名 类型及约束;`
    >
    > ```sql
    > -- 修改字段类型：不重命名版
    > alter table students modify birth date not null;
    > ```

  * 删除字段：alter+drop

    > 语法格式：`alter table 表名 drop 列名;`
    >
    > ```sql
    > -- 修改表-删除字段
    > alter table students drop birth;
    > ```

  * 重命名字段：alter+change

    > 语法格式：`alter table 表名 change 原名 新名 类型及约束;`
    >
    > ```sql
    > -- 修改表-修改字段：重命名版
    > alter table students change birthday birth datetime;
    > ```

* 删除表：drop

  > 语法格式：`drop table 表名;`
  >
  > ```sql
  > -- 删除表
  > drop table students;
  > ```

* 清空表的数据，并将id重置为1：turncate

  > 语法格式：`truncate table 表名`
  >
  > ```sql
  > -- 清空表
  > truncate table test;
  > ```

#### 5.4 SQL基础语法——数据表中数据的操作

* 表数据的增删改查(curd)

  > 创建（Create）
  > 更新（Update）
  > 读取（Retrieve/Read）
  > 删除（Delete）

* 查询数据——读取(Retrieve/Read)：select

  * 语法格式

    > 查询指定列：`select 列1名(字段1名),列2名... from 表名;`
    >
    > 查询所有列：`select * from 表名;`
    >
    > 查询行(指定列、全部)：`select * from 表名 where 条件(id=1);`
    >
    > 查询并以别名显示：`select id as '编号', name as ‘姓名’ from 表名;`

    > ```sql
    > -- 查询表的所有字段 *
    > select * from classes;
    > -- 查询指定的字段(列)
    > select id from classes;
    > ```

* 增加数据——创建(Create)：insert

  * 语法格式

    > 插入指定列：`insert into 表名 (列1名,列2名... ) values (值1,值2...);`
    >
    > 全列插入：`insert into 表名 values (值1,值2...);`
    >
    > 插入多条记录(行) ：`insert into 表名 (列1名,列2名...)  values (值1,值2...),(....);`

  * 注意：

    > 主键列是⾃动增⻓，但是在全列插⼊时需要占位，通常使⽤空值(0或者null);字段默认值 default来占位，插⼊成功后以实际数据为准
    > 全列插⼊：值的顺序与表结构字段的顺序完全⼀⼀对应

    > ```sql
    > -- 全列多行插入
    > insert into classes values (null,'Python',50),(null,'Java',66),(3,'C++',45);
    > -- 指定列插入
    > insert into students(id,name,gender) values (1,'liu','男'),(0,'高','妖');
    > ```

* 修改数据——更新(Update)：update

  > 语法格式：`update 表名 set 列1=值1,列2=值2,... where 条件(id=值);`
  >
  > ```sql
  > -- 修改数据
  > update students set gender='男',height=1.75 where name='wang';
  > ```

* 删除数据——删除(Delete)：delete

  * 物理删除：不可恢复

    > 语法格式：`delete from 表名 where 条件(name=‘li');` 
    >
    > ```sql
    > -- 删除数据
    > delete from students where name=’li'；
    > ```

  * 逻辑删除：可恢复——本质上是修改操作update

    > 方法：为table添加一个新的字段is_delete(bit类型)，通过该字段的0,1状态来表征该数据(记录)是否删除 。
    >
    > ```sql
    > -- 添加is_delete字段，默认值为0
    > alter table students add is_delete bit default 0;
    > -- 逻辑删除——修改字段状态值表示删除
    > update students set is_delete=1 where name='li';
    > ```



