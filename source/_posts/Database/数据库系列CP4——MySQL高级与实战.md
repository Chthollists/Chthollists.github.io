---
title: 数据库系列CP4——MySQL高级与实战
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Middleware
  - MySQL
tags:
  - MySQL
abbrlink: fafa9f41
date: 2020-08-12 10:18:30
top:
---

---

* 数据库系列CP4——MySQL高级与实战
* CP4主要分享了：
  * 索引的创建与使用
  * MySQL账户管理与授权
  * 主从同步及其配置方法
  * 实战：简单爬虫——数据库版
  * 实战：简易Web——数据库版

<!-- more -->

# 数据库系列CP4——MySQL高级与实战

## 四、MySQL高级与实战

* 时间：2020.08.11 ~ 2020.08.13

* 主要内容
  * 索引index
  * MySQL账户管理与授权grant
  * MySQL主从同步与配置
  * 数据库版爬虫
  * 数据库版Web

### 1. 索引

* 时间：2020.08.11

#### 1.1 索引的基础知识

* 概念：类似目录

  > 索引是⼀种特殊的文件(InnoDB数据表上的索引是表空间的⼀个组成部分)，它们包含着对数据表中所有记录的位置信息。

* 作用：

  > 对于数据量很大的情况，索引可以加快数据库的查询速度
  >
  > 索引可以明显提高某些字段的查询效率

* 原理：索引的类型——B + tree 平衡查找树

  > 把数据分成段，然后分段查询
  >
  > MySQL中的索引查询原理是B + tree，其他原理还有哈希表等。

#### 1.2 索引的创建与使用

* 查询索引

  > 语法格式：`show index from 表名;`
  >
  > ```sql
  > show index from goods;
  > ```
  >
  > 主键、外键都是一种特殊的索引

* 创建索引

  > 语法格式：`create inded 索引名称 on 表名(字段名(长度))`
  >
  > ```sql
  > create index name_idx1 on goods(name(150));
  > ```
  >
  > 如果指定字段是字符串，需要指定长度，建议长度与定义字段时的长度⼀致 
  >
  > 字段类型如果不是字符串，可以不填写长度部分

* 删除索引

  > 语法格式：`drop index 索引名称 on 表名;`
  >
  > ```sql
  > drop index name_idx1 on goods;
  > ```

#### 1.3 索引查询效率检测

* 创建测试表`test_index`，插入数据(10万条)

  > ```python
  > import pymysql
  > 
  > conn = pymysql.connect(host='localhost', port=3306, user='root', password='******', database='python_test',
  >                     charset='utf8')
  > cur = conn.cursor()
  > 
  > sql0 = "create table test_index(" \
  >     "id int unsigned primary key auto_increment not null," \
  >     "title varchar(10) not null);"
  > 
  > try:
  >  cur.execute(sql0)
  >  for i in range(100000):
  >      sql1 = "insert into test_index(title) values('ha-%d');" % i
  >      cur.execute(sql1)
  >      
  >  conn.commit()
  > except:
  >  print('RollBack!')
  >  conn.rollback()
  > 
  > # 6. 关闭游标和连接
  > cur.close()
  > conn.close()
  > ```

* 非索引查询

  > 查询最后一条数据

  > ```sql
  > set profiling = 1;
  > select * from test_index where title = "ha-99999";
  > show profiles;
  > -- 0.02788700 s
  > ```

* 索引查询

  > 查询最后一条数据
  >
  > ```sql
  > set profiling = 1;
  > create index title_idx1 on test_index(title(10));
  > select * from test_index where title = "ha-99999";
  > show profiles;
  > -- 0.00030675 s
  > ```

* 注意：

  * 在MySQL中，查看语句执行所用时长的方法是

    > ```sql
    > -- 开启运行时间监测
    > set profiling = 1;
    > -- 查询执行时间
    > show profiles;
    > ```

  * 索引的使用原则：

    * 建立太多的索引将会影响更新和插入的速度，因为更新、插入数据时需要同步更新每个索引文件；
    * 建立索引会占用磁盘空间；
    * 因此，对于经常会更新的数据表，数据量较小的数据表而言，一般不需要创建索引；
    * 而对于数据量非常大、且更新频率较低、内容较少的数据表，可以通过建立索引来提高查询效率。

---

### 2. MySQL账户管理

* 时间：2020.08.11

#### 2.1 管理员用户

* root 管理员用户

  > root用户属于数据库系统中的超级管理员，有权限对mysql进行任何想要做的操作；
  >
  > root用户用于管理用户，而其他普通用户才是数据库操作时的用户；
  >
  > 进行账户操作时，需要使使用root账户登录，这个账户拥有最高的实例级权限；
  >
  > 账户的操作主要包括创建账户、删除账户、修改密码、授权权限等。

* MySQL用户类型：

  * 服务实例级账号：启动了⼀个mysqld，即为⼀个数据库实例；如果某用户(如root)，拥有服务实例级分配的权限，那么该账号就可以删除所有的数据库、连同这些库中的表 
  * 数据库级别账号：可以对特定数据库执行增删改查的所有操作 
  * 数据表级别账号：可以对特定表执行增删改查等所有操作
  * 字段级别的账号：可以对某些表的特定字段进进行操作 
  * 存储程序级别的账号：可以对存储程序进行增删改查的操作

* 查看所有用户

  * 关键的字段：

    * Host表示允许访问的主机

    * User表示用户名 

    * authentication_string表示密码，为加密(MD5加密)后的值

      > MySQL数据库中的所有用户信息都保存在默认的mysql数据库中的user表中
      >
      > ```sql
      > use mysql;
      > desc user;
      > select Host, User, authentication_string from user;
      > ```

  * 默认用户

    * mysql.infoschema
    * mysql.session
    * mysql.sys
    * root

* 创建用户及授权

  > 常用权限主要包括：create、alter、drop、insert、update、delete、select 
  >
  > 分配所有权限：`all privileges`
  >
  > 分配权限后需要刷新权限才会生效：`flush privileges`

  * 登录root用户

    > ```bash
    > $ mysql -u root -p
    > ```

  * 创建用户

    > 语法格式：`create user '用户名'@'host' identified by '密码';`
    >
    > ```sql
    > create user 'test_user'@'localhost' identified by 'mysql';
    > create user 'test_remoteuser'@'%' identified by 'mysql';
    > ```

  * 授权：grant

    > 语法格式：`grant 权限列表 on 数据库名.表名 to '用户名'@'host';`
    >
    > ```sql
    > -- 授予查询权限
    > grant select on jingdong.* to 'test_remoteuser'@'%';
    > -- 授予所有权限
    > grant all privileges on jingdong.* to 'test_user'@'localhost';
    > -- 刷新权限
    > flush privileges;
    > -- 查看权限
    > show grants for 'test_user'@'loclahost';
    > show grants for 'test_remoteuser'@'%';
    > ```

* 注意：

  * 访问主机通常使用百分号%：表示此账户可以使用任何ip的主机登录访问此数据库 
  * 访问主机可以设置成localhost或具体的ip：表示只允许本机或特定主机访问
  * `*.*` 表示所有数据库的所有数据表

#### 2.2 用户操作

* 修改用户权限

  > 语法格式：`grant 权限列表 on 数据库名.表名 to '用户名'@'主机' with grant option;`
  >
  > ```sql
  > grant update, insert on jingdong.* to 'test_remoteuser'@'%' with grant option;
  > flush privileges;
  > ```

* 修改用户密码

  > 语法格式：`alter user '用户名'@'host' identified by '新密码';`
  >
  > ```sql
  > alter user 'test_user'@'localhost' identified by 'Mysql';
  > flush privileges;
  > ```

* 修改MySQL数据库密码

  > mysql默认安装时会有⼀个随机的密码

  * Linux 系统

    > ```bash
    > # 1. 停止mysql服务
    > sudo service mysql stop
    > # 2. 打开配置文件 mysqld.cnf
    > sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
    > # 3. 在[mysqld]段下加入一行，使mysql跳过正常验证
    > skip-grant-tables
    > # 保存关闭 mysqld.cnf文件
    > # 4. 开启mysql服务
    > sudo service mysql start
    > # 5. 直接输入mysql登录数据库
    > mysql
    > # 6. 选择mysql数据库
    > use mysql;
    > # 7. 修改密码为空
    > update user set authentication_string = '' where user='root' and host = 'localhost';
    > # 8. 密码修改完成，再次打开配置文件，将登录模式改回正常模式
    > sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
    > # 删除 skip-grant-tables
    > # 9.重启mysql服务
    > sudo service mysql restart
    > # 10. 重新登录并修改密码
    > alter user 'root'@'localhost' identified by 'newpassword';
    > ```

  * Windows 系统：[MySQL8.0版本参考](https://blog.csdn.net/Byemax/article/details/106916992)

    > ```bash
    > # 以管理员身份运行cmd
    > # 1. 停止mysql服务
    > net stop mysql
    > # 2. 设置免验证直接登录
    > mysqld --defaults-file="my.ini文件路径" --console --skip-grant-tables --shared-memory
    > # 3. 在新的cmd中，直接输入mysql登录数据库
    > mysql
    > # 4. 选择mysql数据库
    > use mysql;
    > # 5. 修改密码为空(不可以输入新密码，因为会被加密)
    > # 或者不用use mysql，直接使用update mysql.user
    > update user set authentication_string = '' where user='root' and host = 'localhost';
    > flush privileges;
    > # 6. 启动mysql服务
    > net start mysql
    > # 7. 重新登录并修改密码
    > alter user 'root'@'localhost' identified by 'newpassword';
    > # 或者
    > alter user 'root'@'localhost' identified with mysql_native_password by 'newpassword'; 
    > flush privileges;
    > ```

* 注意：

  * 修改root用户密码时，Windows系统下使用`mysqld --defaults-file="my.ini文件路径" --console --skip-grant-tables`对应的是MySQL 5.7版本，在8.0版本中要在后面加上`--shared-memory`

  * 使用authentication_string=''修改密码时，只能将密码设为空

  * MySQL 8.0版本修改密码

    > ```sql
    > alter user 'test_user'@'localhost' identified by 'Mysql';
    > ```

  * MySQL 5.7版本修改密码

    > ```sql
    > update user set authentication_string=password(‘123456’) where user=‘root’;
    > ```

* 删除用户：使用root用户

  * drop

    > 语法格式：`drop user '用户名'@'主机';`
    >
    > ```sql
    > drop user 'test_user1'@'localhost';
    > flush privileges;
    > ```

  * delete

    > 语法格式：`delete from user where user = '用户名' and host = ’主机';`
    >
    > ```sql
    > delete from user where user = 'test_user1' and host = 'localhost';
    > flush privileges;
    > ```
    >
    > 

* 远程授权登录

  * 当用户的host设置为%或者特定ip地址时，可以进行远程登录

    > ```sql
    > -- 创建可以远程登录的用户，或修改root用户的host
    > create user 'root'@'%' identified by 'remote';
    > grant all on *.* to 'root'@'%;'
    > -- 远程登录 都一个-h参数，数据库所在主机的IP地址
    > mysql -h 192.168.1.100 -u root -p
    > ```

  * 连接失败的原因

    * 网络不通

      > ping	xxx.xxx.xx.xxx可以发现网络是否正常

    * 查看数据库是否配置了bind_address参数

      > 本地登录数据库查看配置⽂件和数据库当前参数show variables like 'bind_add ress';
      > 如果设置了bind_address=127.0.0.1	那么只能本地登录

    * 查看数据库是否设置了skip_networking参数

      > 如果设置了该参数，那么只能本地登录mysql数据库

    * 端口指定是否正确

---

### 3. 主从服务器

* 时间：2020.08.12

#### 3.1 基础概念

* 主从配置概念和用途

  > 主从同步使得数据可以从⼀个数据库服务器复制到其他服务器上，在复制数据时，⼀个服务器充当主服务器(master)，其余的服务器充当从服务器(slave)。
  >
  > 因为复制是异步进行的，所以从服务器不需要⼀直连接着主服务器，从服务器甚至可以通过拨号断断续续地连接主服务器。
  >
  > 通过配置⽂件，可以指定复制所有的数据库、某个数据库，或者是某个数据库上的某个表。

* 主从同步的优点

  * 提供服务可用性，不会因为主服务器出现问题而停止服务； 
  * 通过增加从服务器来提高数据库的性能，在主服务器上执行写⼊和更新，在从服务器上向外提供读功能，可以动态地调整从服务器的数量，从而调整整个数据库的性能； 
  * 提高数据安全，因为数据已复制到从服务器，从服务器可以终止复制进程，所以，可以在从服务器上备份而不破坏主服务器相应数据。

* 主从同步的原理、机制

  > MySQL服务器之间的主从同步是基于⼆进制日志机制，
  >
  > 主服务器使用⼆进制日志来记录数据库的变动情况，
  >
  > 从服务器通过读取和执行该日志文件来保持和主服务器的数据⼀致。

* 从服务器特点

  * 从服务器可以指定执行主服务器二进制日志中的哪⼀类事件（譬如只插⼊数据或者只更新数据），默认会执行所有语句；
  * 每⼀个从服务器会记录关于⼆进制日志的信息：⽂件名和已经处理过的语句，所以不同的从服务器可以分别执行同⼀个⼆进制日志的不同部分，提高数据库性能；
  * 从服务器不需要一直连接主服务器，可以随时连接和中断；
  * 主服务器和每⼀个从服务器都必须配置⼀个唯⼀的ID号，在my.cnf⽂件的[mysqld]模块下有⼀个serverid配置项；
  * 每⼀个从服务器还需要通过CHANGE MASTER TO语句来配置它要连接的主服务器的ip地址，日志⽂件名称和该日志里面的位置。

* 数据库备份与恢复

  > ```bash
  > # 备份 dump
  > $ mysqldump -u root -p 数据库名称 > xxx.sql
  > 
  > # 恢复
  > $ mysql -u root -p 新数据库名 < xxx.sql
  > ```

#### 3.2 主从服务器配置

* 步骤：

  * 在主服务器上，必须开启⼆进制日志机制和配置⼀个独立的ID
  * 在每⼀个从服务器上，配置⼀个唯⼀的ID，创建⼀个用来专门复制主服务器数据的账号
  * 在开始复制进程前，在主服务器上记录⼆进制文件的位置信息
  * 如果在开始复制之前，数据库中已经有数据，就必须先创建⼀个数据快照(可以使用mysqldump导出数据库，或者直接复制数据文件)
  * 配置从服务器要连接的主服务器的IP地址和登陆授权，⼆进制日志文件名和位置

* 实例：Master——Ubuntu MySQL ，slave——Windows MySQL

  > 设置主从配置前，要确保主从服务器的MySQL环境完全一致

  * 如果主服务器存在数据，备份主服务器原有数据到从服务器

    > ```bash
    > #  主服务器备份
    > $ mysqldump -u root -p mysql --all-databases --lock-all-tables > ~/master_db.sql
    > ```
    >
    >  --all-databases 表示导出所有的数据库
    >
    > --lock-all-tables 表示执行操作时锁住所有数据表，以免数据被改变
    >
    > ~/master_db.sql  表示备份文件的存储路径

  * 复制master_db.sql，放在从服务器的mysql根目录下，还原数据

    > ```bash
    > $ mysql –u root –p mysql < master_db.sql
    > ```

  * 配置主服务器的日志文件和id

    > 编辑设置mysqld的配置文件mysqld.cnf，设置log_bin和server-id
    >
    > ```bash
    > sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
    > server-id = 1
    > log_bin = /var/log/mysql/error.log
    > 
    > # 注释地址绑定代码，以免无法远程登录
    > # bind-address = 127.0.0.1
    > ```

  * 重启mysql服务

    > ```bash
    > sudo service mysql restart
    > ```

  * 登录root用户并创建从服务器同步数据使用的账号

    > ```bash
    > $ mysql -u root -p
    > ```
    >
    > ```sql
    > grant replication slave on *.* to 'slave'@'%' identified by 'slave';
    > flush privileges;
    > ```

  * 获取主服务器的二进制日志文件信息

    > ```sql
    > show master status;
    > ```
    >
    > 记录File：使用的日志文件名字，Position：使用的文件位置
    >
    > 用于配置从服务器

  * 配置从服务器的id

    > 编辑设置mysqld的配置文件my.ini，设置server-id，取值与主服务器不同
    >
    > ```bash
    > vim my.ini
    > server-id = 2
    > ```

  * 重启从服务器的mysql服务

    > ```bash
    > net stop mysql
    > net start mysql
    > ```

  * 设置从服务器与主服务器的连接

    > ```sql
    > change master to master_host='主服务器ip地址', master_user='slave(用户名)', master_password='密码', master_log_file='日志文件名', master_log_pos='日志文件位置';
    > ```
    >
    > master_log_file、master_log_pos对应在主服务器数据库中查询到的File和Position

  * 开启主从同步

    > ```sql
    > start slave;
    > ```

  * 查看配置结果

    > \G 表示将结果每行逐条显示，方便查看
    >
    > ```sql
    > show slave status \G;
    > ```
    >
    > 显示内容中包含：
    >
    > Slave_IO_Running:Yes
    >
    > Slave_SQL_Running:Yes
    >
    > 表示主从同步已经正常开启运行

  * 测试主从同步

    > ```sql
    > -- 主服务器创建新的数据库
    > create database synchronization_test charset=utf8;
    > -- 从服务器中查看是否存在该数据库
    > show databases;
    > ```

---

### 4. 实战——爬虫存储到数据库

* 时间：2020.08.13

#### 4.1 爬虫原版(面向对象+多线程)

* 多线程爬取[电影天堂](https://www.dytt8.net/index.htm)中的电影下载链接资源；

* 将爬取的数据储存在文件中

* 封装在类Spider中，面向对象编程

  > ```python
  > # coding:utf-8
  > # 简单爬虫
  > # 多线程版爬取电影天堂dytt8.net的影视资源
  > # 每个线程爬取一个子列表页
  > """
  > 定义爬虫类class Spider()
  > 封装函数
  > 一、定义函数，从主列表页中获取子列表页地址 get_movie_list_url()
  > 1.定义主列表页地址
  > 2.打开列表页地址url，获取数据(二进制)
  > 3.解码获取到的数据
  > 4.利用正则表达式提取资源页的地址链接
  > 
  > 二、函数get_movie_resource_url()与 get_movie_download_url()整合在一起
  > 1.定义子列表页地址
  > 2.打开列表页地址url，获取数据(二进制)
  > 3.解码获取到的数据
  > 4.利用正则表达式提取资源页的地址链接
  > 5.拼接资源页地址
  > 6.打开资源页地址url，获取数据(二进制)
  > 7.解码数据
  > 8.利用正则表达式提取下载地址
  > 
  > 三、主函数 main()
  > 1.1定义主列表页地址
  > 1.2定义子列表页地址开头
  > 1.3定义资源页地址开头
  > 2.创建爬虫对象
  > 3.调用对象的start()方法
  > """
  > import urllib.request
  > import re
  > import time
  > import threading
  > 
  > 
  > class Spider(object):
  >  num = 1
  >  resource_file_name = 'Movie Resource content.txt'
  >  download_file_name = 'Movie Download content.txt'
  > 
  >  def __init__(self, film_main_list_url, url_list_head, url_head):
  >      self.film_main_list_url = film_main_list_url
  >      self.url_list_head = url_list_head
  >      self.url_head = url_head
  >      self.film_sub_list_url = []
  >      self.movie_dict_download_url = {}
  >      self.lock1 = threading.Lock()
  > 
  >  def start(self):
  >      self.film_sub_list_url = self.get_movie_list_url()
  >      # film_sub_list_url = get_movie_list_url(file_main_list_url)
  >      tmp = self.film_sub_list_url[1:5]
  >      # for film_list_url in self.film_sub_list_url:
  >      for film_list_url in tmp:
  >          # 2.1 调用get_movie_resource_url()获取资源页地址
  >          # movie_list_resource_url = get_movie_resource_url(url_list_head + file_list_url)
  >          t1 = threading.Thread(target=self.get_movie_url, args=(self.url_list_head + film_list_url,))
  >          t1.start()
  > 
  >      # 所有线程执行完毕后再退出
  >      # print(len(threading.enumerate()))
  >      while len(threading.enumerate()) != 1:
  >          time.sleep(1)
  > 
  >      # 把字典遍历输出
  >      for film_name, film_link in self.movie_dict_download_url.items():
  >          print("电影名称为:%s | 下载地址为:%s\n" % (film_name, film_link))
  > 
  >  def get_movie_list_url(self):
  >      """从主列表页获取子列表页的地址"""
  >      # 1.定义列表页地址
  >      # file_main_list_url
  > 
  >      # 2.1 打开列表页地址url，获取数据(二进制)
  >      response_list = urllib.request.urlopen(self.film_main_list_url)
  >      # 2.2 通过read()读取数据
  >      response_list_data = response_list.read()
  > 
  >      # 3.解码获取到的数据
  >      response_list_text = response_list_data.decode('gb18030', 'ignore')
  >      # print(response_list_text)
  > 
  >      # 4.利用正则表达式提取各个影片的资源页地址链接
  >      # 例子："<option value='list_23_3.html'>3</option>"
  >      pattern = r"<option value=\'(.*?)\'"
  >      film_sub_list_url = re.findall(pattern, response_list_text)
  >      # movie_list_url是元组组成的list  [(,),(,),...]
  >      # print(movie_list_url)
  >      return film_sub_list_url
  > 
  >  def get_movie_url(self, film_list_url):
  >      """从列表页获取电影资源页的地址，从资源页获取电影下载地址"""
  >      # 1.定义列表页地址
  >      # film_list_url
  > 
  >      # 2.1 打开列表页地址url，获取数据(二进制)
  >      resource_list = urllib.request.urlopen(film_list_url)
  >      # 2.2 通过read()读取数据
  >      resource_list_data = resource_list.read()
  > 
  >      # 2.3解码获取到的数据
  >      resource_list_text = resource_list_data.decode('gb18030', 'ignore')
  >      # print(response_list_text)
  > 
  >      # 2.4利用正则表达式提取各个影片的资源页地址链接
  >      # 例子：<a href="/html/gndy/dyzz/20200629/60155.html" class="ulink">2020年获奖剧情《乳牙/谢谢你爱过我》BD中英双字幕</a>"
  >      pattern = "<a href=\"(.*)?\" class=\"ulink\">(.*)</a>"
  >      movie_list_resource_url = re.findall(pattern, resource_list_text)
  > 
  >      # 2.5 将电影名称和资源页地址
  >      with open(self.resource_file_name, 'a') as file1:
  >          with open(self.download_file_name, 'a') as file2:
  >              # 3.拼接资源页地址
  >              for url_content, movie_name in movie_list_resource_url:
  >                  resource_url = self.url_head + url_content
  >                  file1.write('%d:电影名称为:%s, 资源地址为:%s\n' % (self.num, movie_name, resource_url))
  > 
  >                  # 4.1打开资源页地址url，获取数据(二进制)
  >                  download_list = urllib.request.urlopen(resource_url)
  >                  download_list_data = download_list.read()
  > 
  >                  # 4.2解码数据
  >                  download_list_text = download_list_data.decode('gbk', 'ignore')
  >                  # response_list_text = resource_list_data.decode('gbk', 'ignore')
  > 
  >                  # 4.3利用正则表达式提取下载地址
  >                  pattern = "bgcolor=\"#fdfddf\"><a href=\"(.*?)\">"
  >                  result = re.search(pattern, download_list_text)
  >                  if result:
  >                      # print(result.group(1))
  >                      file2.write('%d:电影名称为:%s, 下载地址为:%s\n' % (self.num, movie_name, result.group(1)))
  >                      self.lock1.acquire()
  >                      self.movie_dict_download_url[movie_name] = result.group(1)
  >                      print('已成功获取第%d个电影下载地址!' % self.num)
  >                      self.lock1.release()
  > 
  >                      self.num += 1
  > 
  > 
  > def main():
  >  """主函数：程序入口"""
  >  # 1.1定义主列表页地址
  >  # 1.2定义子列表页地址开头
  >  # 1.3定义资源页地址开头
  >  film_main_list_url = 'https://www.ygdy8.net/html/gndy/dyzz/index.html'
  >  url_list_head = 'https://www.ygdy8.net/html/gndy/dyzz/'
  >  url_head = 'https://www.ygdy8.net'
  > 
  >  # 2.创建对象
  >  movie_spider = Spider(film_main_list_url, url_list_head, url_head)
  > 
  >  # 3.调用start()启动
  >  movie_spider.start()
  > 
  > 
  > if __name__ == '__main__':
  >  main()
  > ```

#### 4.2 数据库设计与创建

* 数据库：movie_db

* 数据表：movie_link

* 字段：id，film_name，download_link，resource_link

  > ```sql
  > create database movie_db charset=utf8;
  > create table movie_link(
  >     id int(11) unsigned primary key auto_increment not null,
  >     film_name varchar(255) not null,
  >     resource_link varchar(255) not null,
  >     download_link varchar(255) not null
  > );
  > ```

#### 4.3 爬虫进阶：数据库版

* 增加函数：将爬取的数据存入数据库：saveto_mysql()

  > ```python
  >     def saveto_mysql(self, movie_name, resource_link):
  >         # (1) 定义sql语句,插入数据 insert
  >         sql = "insert into movie_link values (null, %s, %s, %s);"
  >         params = [movie_name, resource_link, self.movie_dict_download_url[movie_name]]
  > 
  >         # (2) 执行sql语句
  >         self.cur.execute(sql, params)
  >         print("保存成功！电影资源：", movie_name)
  > ```

* 增加函数：判断插入的数据和数据库中已有数据是否重复：film_isexist()

  > ```python
  >     def film_isexist(self, movie_name, resource_link):
  >         # (1) 定义sql语句,条件查询 select
  >         sql = "select id from movie_link where film_name = %s and resource_link = %s and download_link = %s limit 1;"
  >         params = [movie_name, resource_link, self.movie_dict_download_url[movie_name]]
  > 
  >         # (2) 执行sql语句
  >         ret = self.cur.execute(sql, params)
  > 
  >         # (3) 存在返回True,不存在返回False
  >         if ret:
  >             return True
  >         else:
  >             return False
  > ```

* 完整版

  > ```python
  > # coding:utf-8
  > # 多线程简单爬虫，封装成class
  > # 数据库版
  > """
  > 增加内容：
  > 一、定义新函数,判断数据库是否已经存在要插入的数据: film_isexist()
  > (1) 定义sql语句,条件查询 select
  > (2) 执行sql语句
  > (3) 存在返回True,不存在返回False
  > 
  > 二、定义新函数,向数据库插入数据: saveto_mysql()
  > (1) 定义sql语句,插入数据 insert
  > (2) 执行sql语句
  > 
  > 三、主函数
  > (1) 与数据库建立连接
  > (2) 创建游标对象
  > (3) 执行操作
  > (4) 关闭游标对象和连接
  > 
  > """
  > import urllib.request
  > import re
  > import time
  > import threading
  > import pymysql
  > 
  > 
  > class Spider(object):
  >     num = 1
  >     resource_file_name = 'Movie Resource content.txt'
  >     download_file_name = 'Movie Download content.txt'
  > 
  >     def __init__(self, film_main_list_url, url_list_head, url_head, cur):
  >         self.film_main_list_url = film_main_list_url
  >         self.url_list_head = url_list_head
  >         self.url_head = url_head
  >         self.film_sub_list_url = []
  >         self.movie_dict_download_url = {}
  >         self.lock1 = threading.Lock()
  >         self.cur = cur
  > 
  >     def start(self):
  >         self.film_sub_list_url = self.get_movie_list_url()
  >         # film_sub_list_url = get_movie_list_url(file_main_list_url)
  >         # 测试只选取前几个子列表页tmp
  >         tmp = self.film_sub_list_url[2:4]
  >         # for film_list_url in self.film_sub_list_url:
  >         for film_list_url in tmp:
  >             # 2.1 调用get_movie_resource_url()获取资源页地址
  >             # movie_list_resource_url = get_movie_resource_url(url_list_head + file_list_url)
  >             t1 = threading.Thread(target=self.get_movie_url, args=(self.url_list_head + film_list_url,))
  >             t1.start()
  > 
  >         # 所有线程执行完毕后再退出
  >         # print(len(threading.enumerate()))
  >         while len(threading.enumerate()) != 1:
  >             time.sleep(1)
  > 
  >         self.cur.close()
  >         # 把字典遍历输出
  >         # for film_name, film_link in self.movie_dict_download_url.items():
  >         # print("电影名称为:%s | 下载地址为:%s\n" % (film_name, film_link))
  > 
  >     def get_movie_list_url(self):
  >         """从主列表页获取子列表页的地址"""
  >         # 1.定义列表页地址
  >         # file_main_list_url
  > 
  >         # 2.1 打开列表页地址url，获取数据(二进制)
  >         response_list = urllib.request.urlopen(self.film_main_list_url)
  >         # 2.2 通过read()读取数据
  >         response_list_data = response_list.read()
  > 
  >         # 3.解码获取到的数据
  >         response_list_text = response_list_data.decode('gb18030', 'ignore')
  >         # print(response_list_text)
  > 
  >         # 4.利用正则表达式提取各个影片的资源页地址链接
  >         # 例子："<option value='list_23_3.html'>3</option>"
  >         pattern = r"<option value=\'(.*?)\'"
  >         film_sub_list_url = re.findall(pattern, response_list_text)
  >         # movie_list_url是元组组成的list  [(,),(,),...]
  >         # print(movie_list_url)
  >         return film_sub_list_url
  > 
  >     def get_movie_url(self, film_list_url):
  >         """从列表页获取电影资源页的地址，从资源页获取电影下载地址"""
  >         # 1.定义列表页地址
  >         # film_list_url
  > 
  >         # 2.1 打开列表页地址url，获取数据(二进制)
  >         resource_list = urllib.request.urlopen(film_list_url)
  >         # 2.2 通过read()读取数据
  >         resource_list_data = resource_list.read()
  > 
  >         # 2.3解码获取到的数据
  >         resource_list_text = resource_list_data.decode('gb18030', 'ignore')
  >         # print(response_list_text)
  > 
  >         # 2.4利用正则表达式提取各个影片的资源页地址链接
  >         # 例子：<a href="/html/gndy/dyzz/20200629/60155.html" class="ulink">2020年获奖剧情《乳牙/谢谢你爱过我》BD中英双字幕</a>"
  >         pattern = "<a href=\"(.*)?\" class=\"ulink\">(.*)</a>"
  >         movie_list_resource_url = re.findall(pattern, resource_list_text)
  > 
  >         # 2.5 将电影名称和资源页地址
  >         with open(self.resource_file_name, 'a') as file1:
  >             with open(self.download_file_name, 'a') as file2:
  >                 # 3.拼接资源页地址
  >                 for url_content, movie_name in movie_list_resource_url:
  >                     resource_url = self.url_head + url_content
  >                     file1.write('%d:电影名称为:%s, 资源地址为:%s\n' % (self.num, movie_name, resource_url))
  > 
  >                     # 4.1打开资源页地址url，获取数据(二进制)
  >                     download_list = urllib.request.urlopen(resource_url)
  >                     download_list_data = download_list.read()
  > 
  >                     # 4.2解码数据
  >                     download_list_text = download_list_data.decode('gbk', 'ignore')
  >                     # response_list_text = resource_list_data.decode('gbk', 'ignore')
  > 
  >                     # 4.3利用正则表达式提取下载地址
  >                     pattern = "bgcolor=\"#fdfddf\"><a href=\"(.*?)\">"
  >                     result = re.search(pattern, download_list_text)
  >                     if result:
  >                         # print(result.group(1))
  >                         file2.write('%d:电影名称为:%s, 下载地址为:%s\n' % (self.num, movie_name, result.group(1)))
  > 
  >                         self.lock1.acquire()
  >                         self.movie_dict_download_url[movie_name] = result.group(1)
  >                         print('已成功获取第%d个电影下载地址!' % self.num)
  >                         self.lock1.release()
  >                         self.num += 1
  > 
  >                         if self.film_isexist(movie_name, resource_url):
  >                             print('保存失败！该电影资源：%s已经存在！' % movie_name)
  >                             continue
  >                         else:
  >                             self.saveto_mysql(movie_name, resource_url)
  > 
  >                     else:
  >                         print("获取失败！该电影资源：%s格式不匹配！请修改匹配格式！" % movie_name)
  > 
  >     def film_isexist(self, movie_name, resource_link):
  >         # (1) 定义sql语句,条件查询 select
  >         sql = "select id from movie_link where film_name = %s and resource_link = %s and download_link = %s limit 1;"
  >         params = [movie_name, resource_link, self.movie_dict_download_url[movie_name]]
  > 
  >         # (2) 执行sql语句
  >         ret = self.cur.execute(sql, params)
  > 
  >         # (3) 存在返回True,不存在返回False
  >         if ret:
  >             return True
  >         else:
  >             return False
  > 
  >     def saveto_mysql(self, movie_name, resource_link):
  >         # (1) 定义sql语句,插入数据 insert
  >         sql = "insert into movie_link values (null, %s, %s, %s);"
  >         params = [movie_name, resource_link, self.movie_dict_download_url[movie_name]]
  > 
  >         # (2) 执行sql语句
  >         self.cur.execute(sql, params)
  >         print("保存成功！电影资源：", movie_name)
  > 
  > 
  > def main():
  >     """主函数：程序入口"""
  >     # 1.1定义主列表页地址
  >     # 1.2定义子列表页地址开头
  >     # 1.3定义资源页地址开头
  >     film_main_list_url = 'https://www.ygdy8.net/html/gndy/dyzz/index.html'
  >     url_list_head = 'https://www.ygdy8.net/html/gndy/dyzz/'
  >     url_head = 'https://www.ygdy8.net'
  > 
  >     # (1) 与数据库建立连接
  >     conn = pymysql.connect(host='localhost', user='root', password='******', database='movie_db', charset='utf8')
  > 
  >     # (2) 创建游标对象
  >     cur = conn.cursor()
  > 
  >     # (3) 执行操作
  >     # 2.创建对象
  >     movie_spider = Spider(film_main_list_url, url_list_head, url_head, cur)
  > 
  >     # 3.调用start()启动
  >     movie_spider.start()
  > 
  >     # 提交修改
  >     conn.commit()
  > 
  >     # (4) 关闭游标对象和连接
  >     cur.close()
  >     conn.close()
  > 
  >     
  > if __name__ == '__main__':
  >     main()
  > ```

#### 4.4 爬虫+数据库+Web网页显示

* 原始版：简单Web服务器——返回固定内容

  > ```python
  > # 简单Web服务器--返回固定内容
  > """
  > TCP服务端
  > 1、导入模块
  > 2、创建套接字
  > 3、设置地址重用
  > 4、绑定端口
  > 5、设置监听，让套接字由主动变为被动接收
  > 6、接受客户端连接 定义函数 request_handler()
  > 7、接收客户端浏览器发送的请求协议
  > 8、判断协议是否为空
  > 9、拼接响应的报文
  > 10、发送响应报文
  > 11、关闭操作
  > 
  > """
  > 
  > def request_handler(new_client_socket, ip_port):
  >     # 循环接收同一个浏览器(客户端)的请求报文
  >     # while True:
  >         # 7、接收客户端浏览器发送的请求协议
  >         request_data = new_client_socket.recv(1024)
  >         # 8、判断协议是否为空
  >         if not request_data.decode():
  >             print('客户端%s已下线！' % str(ip_port))
  >             new_client_socket.close()
  >             return
  >         else:
  >             print(request_data.decode())
  >         # 9、拼接响应的报文
  >         # 9.1 响应行
  >         response_line = 'HTTP/1.1 200 OK\r\n'
  >         # 9.2 响应头
  >         response_header = "Server:Python20WS/2.1\r\n"
  >         # 9.3 响应空行
  >         response_blank = "\r\n"
  >         # 9.4 响应主体
  >         response_body = "Hello World!"
  >         # 拼接响应报文
  >         response_data = response_line + response_header + response_blank + response_body
  > 
  >         # 10、发送响应报文
  >         new_client_socket.send(response_data.encode())
  > 
  >         # 11、关闭操作
  >         new_client_socket.close()
  > 
  > 
  > def main():
  >     # 1、导入模块
  >     import socket
  > 
  >     # 2、创建套接字
  >     tcp_webserver_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  > 
  >     # 3、设置地址重用
  >     tcp_webserver_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
  > 
  >     # 4、绑定端口
  >     addr = ('', 2233)
  >     tcp_webserver_socket.bind(addr)
  > 
  >     # 5、设置监听，让套接字由主动变为被动接收
  >     tcp_webserver_socket.listen(128)
  > 
  >     # 6、接受客户端连接 定义函数 request_handler()
  >     # 循环接收客户端的连接
  >     while True:
  >         new_client_socket, ip_port = tcp_webserver_socket.accept()
  >         # 调用函数来进行请求报文的接收，以及响应报文的发送
  >         request_handler(new_client_socket, ip_port)
  > 
  >     # 11、关闭操作
  >     tcp_webserver_socket.close()
  > 
  > 
  > if __name__ == '__main__':
  >     main()
  > ```

* 进阶版：返回内容为数据库的数据

  > 注意：
  >
  > 通过在响应头加上：`"Content-Type:text/html; charset=utf8\r\n"`来使文本正确显示(无乱码)
  >
  > 通过`<a href = '%s'>%s</a>`html标签，使下载地址转换为超链接

  > ```python
  > # 简单Web服务器--返回固定内容
  > """
  > 添加内容：
  > (1) 导入pymysql
  > (2) 连接数据库，创建游标对象
  > (3) 读取数据库中的数据，作为响应主体
  > (4) 关闭游标对象和连接对象
  > 
  > """
  > # (1) 导入pymysql
  > import pymysql
  > 
  > 
  > def request_handler(new_client_socket, ip_port):
  >     # 循环接收同一个浏览器(客户端)的请求报文
  >     # while True:
  >     # 7、接收客户端浏览器发送的请求协议
  >     request_data = new_client_socket.recv(1024)
  >     # 8、判断协议是否为空
  >     if not request_data.decode():
  >         print('客户端%s已下线！' % str(ip_port))
  >         new_client_socket.close()
  >         return
  >     else:
  >         print(request_data.decode())
  > 
  >     # 9、拼接响应的报文
  >     # 9.1 响应行
  >     response_line = 'HTTP/1.1 200 OK\r\n'
  >     # 9.2 响应头
  >     response_header = "Server:Python20WS/2.1\r\n"
  >     response_header += "Content-Type:text/html; charset=utf8\r\n"
  >     # 9.3 响应空行
  >     response_blank = "\r\n"
  >     # 9.4 响应主体
  >     response_body = ""
  > 
  >     # (2) 连接数据库，创建游标对象
  >     conn = pymysql.connect(host='localhost', user='root', password='******', database='movie_db', charset='utf8')
  >     cur = conn.cursor()
  > 
  >     # (3) 读取数据库中的数据，作为响应主体
  >     sql = "select id, film_name, download_link from movie_link;"
  >     cur.execute(sql)
  >     for content in cur.fetchall():
  >         response_body += "<font color='purple' face='Times New Roman, Microsoft YaHei' size= 2><b> %d.&nbsp;&nbsp;  " \
  >                          "电影名称：[%s]&nbsp;&nbsp;  下载链接：[<a href = '%s'>%s</a>]</b></font><br>" % (content[0], content[1], content[2], content[2])
  >         # response_body += "%d.&nbsp;  电影名称：[%s]&nbsp:  下载链接：[%s]<br>" % (content[0], content[1], content[2])
  > 
  > 
  >         # 拼接响应报文
  >     response_data = response_line + response_header + response_blank + response_body
  > 
  >     # 10、发送响应报文
  >     # new_client_socket.send(response_data.encode('GBK'))
  >     new_client_socket.send(response_data.encode())
  > 
  >     # (4) 关闭游标对象和连接对象
  >     cur.close()
  >     conn.close()
  > 
  >     # 11、关闭操作
  >     new_client_socket.close()
  > 
  > 
  > def main():
  >     # 1、导入模块
  >     import socket
  > 
  >     # 2、创建套接字
  >     tcp_webserver_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  > 
  >     # 3、设置地址重用
  >     tcp_webserver_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
  > 
  >     # 4、绑定端口
  >     addr = ('', 2233)
  >     tcp_webserver_socket.bind(addr)
  > 
  >     # 5、设置监听，让套接字由主动变为被动接收
  >     tcp_webserver_socket.listen(128)
  > 
  >     # 6、接受客户端连接 定义函数 request_handler()
  >     # 循环接收客户端的连接
  >     while True:
  >         new_client_socket, ip_port = tcp_webserver_socket.accept()
  >         # 调用函数来进行请求报文的接收，以及响应报文的发送
  >         request_handler(new_client_socket, ip_port)
  > 
  > 
  >     # 11、关闭操作
  >     tcp_webserver_socket.close()
  > 
  > 
  > if __name__ == '__main__':
  >     main()
  > ```

