---
title: Python实战框架MiniWeb框架CP4——伪静态与元类ORM
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Python
  - Frame
  - MiniWeb
tags:
  - Python
  - MiniWeb
  - metaclass
  - ORM
abbrlink: d30e7248
date: 2020-09-02 18:06:40
top:
---

---

* Python实战框架MiniWeb框架CP4——伪静态与元类ORM

* CP4主要分享了

* URL的类型：静态、动态、伪静态

* 元类

  * metaclass属性
  * type()函数

* 自定义元类：为原始类扩展功能——类似装饰器

* ORM：对象关系映射

  > 通过元类实现对象关系映射，将SQL语句功能像实例属性一样调用

<!-- more -->

# Python实战框架MiniWeb框架CP4——伪静态与元类ORM

### 5. miniWeb高阶

* 时间：2020.09.03

#### 5.1 URL类型

* 静态URL

  * 概念

    > 也称为真静态URL，每个网页有真实的物理路径，即真实存在服务器里的。

  * 结构：`domain_name/news/2020-5-18/110.html` 

  * 静态网站对SEO的影响

    > 静态URL对SEO肯定有加分的影响，因为打开速度快，这个是本质。

  * 优点

    > 网站打开速度快，因为它不用进行运算；
    >
    > 网址结构友好，利于记忆。

  * 缺点

    > 对于中大型网站，则产生的页面特别多，不好管理；
    >
    > 会占用更多的磁盘空间。

* 动态URL

  * 概念

    > 每个URL只是一个逻辑地址，并不是真实物理存在服务器硬盘里的。

  * 结构：`domain_name/NewsMore.asp?id=5`、`domain_name/DaiKuan.php?id=17`

    > 带有`?`

  * 优点

    > 适合中大型网站，修改页面很方便；
    >
    > 因为是逻辑地址，所以占用硬盘空间要比纯静态网站小。

  * 缺点

    > 因为要进行运算，所以打开速度稍慢，服务器缓存技术可以解决速度问题；
    >
    > URL结构稍稍复杂，不利于记忆。

  * 动态URL对SEO的影响：

    > 目前百度SE已经能够很好的理解动态URL，所以对SEO没有什么减分的影响。

* 伪静态URL

  * 概念

    > 真静态URL类似。他是通过伪静态规则把动态URL伪装成静态网址。也是逻辑地址，不存在物理地址。

  * 结构：与真静态类似

  * 优点

    > URL比较友好，利于记忆。非常适合大中型网站，是个折中方案。

  * 缺点

    > 设置麻烦，服务器要支持重写规则；
    >
    > 进行了伪静态网站访问速度并没有变快，因为实质上它会额外的进行运算解释，反正增加了服务器负担，速度反而变慢；
    >
    > 有可能会造成动态URL和静态URL都被搜索引擎收录，不过可以用robots禁止掉动态地址。

#### 5.2 元类

##### 5.2.1 类的本质与创建

* 类的实质

  > Python中，类实质上也是一个对象，类是一组用来描述如何生成一个实例对象的代码段。实际上，Python中万物皆对象，当使用关键字class时，Python解释器在执行的时候就会创建一个类对象。

* 类的用法：与普通的对象一致

  > 可以将它赋值给一个变量；
  >
  > 可以拷贝它；
  >
  > 可以为它增加属性；
  >
  > 可以将它作为函数参数进行传递。

* 动态创建类

  * type内置函数

    > type可以接受一个类的描述作为参数，然后返回一个类。
    >
    > 语法格式：`type(类名, 由父类名称组成的元组（继承，可以为空），包含属性的字典（名称和值）)`
    >
    > ```python
    > # 变量名 = type(类名, (), {}) 
    > Myclass = type('Test1', (), {})
    > 
    > class A():
    >  pass
    > # 等价于
    > A = type('A', (), {})
    > ```

  * 特点

    * type的第2个参数，元组中是父类的名字，而不是字符串，如Test1，而不是'Test1'；
    * 添加的属性是类属性，并不是实例属性。

  * 添加方法：现在外部定义方法(函数)，包括类方法、实例方法，再通过type()函数传递参数，创建类对象。

    > ```python
    > # 实例方法
    > def print_b(self):
    >  print(self.num)
    > 
    > @staticmethod
    > def print_static():
    >  print("----haha-----")
    > 
    > @classmethod
    > def print_class(cls):
    >  print(cls.num)
    > 
    > class A(object):
    >  num = 100
    >  
    > B = type("B", (A,), {"print_b": print_b, "print_static": print_static, "print_class": print_class})
    > b = B()
    > b.print_b()
    > b.print_static()
    > b.print_class()
    > ```

##### 5.2.2 元类

* 概念

  > 元类是用来创建类对象的一个特殊的类，即元类就是类的类，而普通的类对象是用来创建实例对象的；
  >
  > 函数type实际上就是一个元类，Python的内建元类。

* 自定义元类

  * `__metaclass__`属性

    > 如果一个类(包括其父类以及存放该类的模块)中没有`__metacalss__`属性，默认按照内建元类type来创建类对象；
    >
    > 如果有该属性的话，按照该属性指向的自定义的元类来创建类对象。

  * 作用

    > 拦截类的创建；修改类；返回修改之后的类；
    >
    > 适用于需要对整个类中的内容进行修改时，在自定义的元类中实现修改，通过自定义的元类代替type来创建修改后的类；
    >
    > 类似装饰器扩展函数的功能，自定义元类用来扩展类的功能。

  * 实例：将非私有属性名全部改为大写

    > meteclss 属性指向函数
    >
    > ```python
    > def upper_attr(class_name, class_parents, class_attr):
    >  # 遍历属性字典，把不是__开头的属性名字变为大写
    >  new_attr = {}
    >  for name, value in class_attr.items():
    >      if not name.startswith("__"):
    >          new_attr[name.upper()] = value
    > 
    >  # 调用type来创建一个类
    >  return type(class_name, class_parents, new_attr)
    > 
    > 
    > class Foo(object, metaclass=upper_attr):
    >  # Python 2 的写法
    >  # __metaclass__ = upper_attr
    >  bar = 'upper done'
    > 
    > 
    > print(hasattr(Foo, 'bar'))
    > print(hasattr(Foo, 'BAR'))
    > 
    > f = Foo()
    > print(f.BAR)
    > ```
    >
    > meteclss 属性指向真正的类
    >
    > ```python
    > class UpperAttrMetaClass(type):
    >  # __new__ 是在__init__之前被调用的特殊方法
    >  # __new__是用来创建对象并返回之的方法
    >  # 而__init__只是用来将传入的参数初始化给对象
    >  # 你很少用到__new__，除非你希望能够控制对象的创建
    >  # 这里，创建的对象是类，我们希望能够自定义它，所以我们这里改写__new__
    >  # 如果你希望的话，你也可以在__init__中做些事情
    >  # 还有一些高级的用法会涉及到改写__call__特殊方法
    >  def __new__(mcs, class_name, class_parents, class_attr):
    >      # 遍历属性字典，把不是__开头的属性名字变为大写
    >      new_attr = {}
    >      for name, value in class_attr.items():
    >          if not name.startswith("__"):
    >              new_attr[name.upper()] = value
    > 
    >      # 方法1：通过'type'来做类对象的创建
    >      return type(class_name, class_parents, new_attr)
    > 
    >      # 方法2：复用type.__new__方法
    >      # 这就是基本的OOP编程，没什么魔法
    >      # return type.__new__(cls, class_name, class_parents, new_attr)
    > 
    > # python3的用法
    > class Test(object, metaclass=UpperAttrMetaClass):
    >  bar = 'UpperAttrMetaClass done'
    > 
    > # python2的用法
    > # class Foo(object):
    > #     __metaclass__ = UpperAttrMetaClass
    > #     bar = 'bip'
    > 
    > print(hasattr(Test, 'bar'))
    > # 输出: False
    > print(hasattr(Test, 'BAR'))
    > # 输出:True
    > 
    > f = Test()
    > print(f.BAR)
    > # 输出:'bip'
    > ```

  * 注意

    * `__metaclass__`属性指向的可以为一个真正的类(class)，该类的父类时type内建元类；也可以指向一个返回值为type()的自定义的函数；
    * `metaclass`实质上是把类名、父类名的元组、类的属性方法的字典三个参数传递给其指向的元类、函数；
    * Python2中，`__metaclass__`属性只能卸载类中，赋值，而在Python3中需要写为：`class Class_name(object, metaclass=user_type):`；
    * `__metaclass__`属性查询顺序：当前类、各个父类(MRO顺序)、模块层次。

#### 5.3 ORM

* 概念：Object Relational Mapping

  > ORM 是 Python编程语言后端web框架 Django的核心思想，称为对象与关系映射，简称ORM。

* 作用

  > 用于简化Python与数据库交互时的SQL语句的代码，无需手动书写SQL，即通过元类来修改类属性进而实现生成SQL语句的功能，代替直接书写SQL语句；
  >
  > 在操作数据库的时候，能够像操作实例对象和属性、方法一样简单。

* 简单思路

  > 通过定义一个类，类名为表名，类的类属性为表的字段名和其数据类型及约束，此外，在类中通过定义不同的实例方法，用来实现SQL中`insert`、`select`、`update`、`delete`等功能(Curd)。
  >
  > ```python
  > # 父类省略
  > class User(object):
  >  uid = ('uid', "int unsigned")
  >  name = ('username', "varchar(30)")
  >  email = ('email', "varchar(30)")
  >  password = ('password', "varchar(30)")
  > 
  >  def __init__(self, uid, name, email, password):
  >      pass
  > 
  >  def save(self):
  >      # ...省略...
  >      pass
  > 
  >  # ...省略...
  > 
  > u = User(uid=12345, name='Michael', email='test@orm.org', password='my-pwd')
  > u.save()
  > # 对应如下sql语句
  > # insert into User (username,email,password,uid) values ('Michael','test@orm.org','my-pwd',12345)
  > ```

* 实例：实现`insert`

  > ```python
  > class ModelMetaclass(type):
  >  def __new__(cls, name, bases, attrs):
  >      mappings = dict()
  >      # 判断是否需要保存
  >      for k, v in attrs.items():
  >          # 判断是否是指定的StringField或者IntegerField的实例对象
  >          if isinstance(v, tuple):
  >              print('Found mapping: %s ==> %s' % (k, v))
  >              mappings[k] = v
  > 
  >      # 删除这些已经在字典中存储的属性
  >      for k in mappings.keys():
  >          attrs.pop(k)
  > 
  >      # 将之前的uid/name/email/password以及对应的对象引用、类名字
  >      attrs['__mappings__'] = mappings  # 保存属性和列的映射关系
  >      attrs['__table__'] = name  # 假设表名和类名一致
  >      return type.__new__(cls, name, bases, attrs)
  >      # return type(name, bases, attrs)
  > 
  > class User(metaclass=ModelMetaclass):
  >  uid = ('uid', "int unsigned")
  >  name = ('username', "varchar(30)")
  >  email = ('email', "varchar(30)")
  >  password = ('password', "varchar(30)")
  >  # 当指定元类之后，以上的类属性将不在类中，而是在__mappings__属性指定的字典中存储
  >  
  >  def __init__(self, **kwargs):
  >      for name, value in kwargs.items():
  >          setattr(self, name, value)
  >          # self.__dict__[name] = value
  > 
  >  def save(self):
  >      fields = []
  >      args = []
  >      for k, v in self.__mappings__.items():
  >          fields.append(v[0])
  >         args.append("{!r}".format(getattr(self, k, None)))
  >          # 也可以通过__dict__魔法属性中的实力属性来赋值
  >          # if k in self.__dict__:
  >          #     fields.append(v[0])
  >          #     args.append("{!r}".format(self.__dict__[k]))
  > 
  >      sql = 'insert into %s (%s) values (%s)' % (self.__table__, ','.join(fields), ','.join([str(i) for i in args]))
  >      print('SQL: %s' % sql)
  > 
  > 
  > u = User(uid=12345, name='Michael', email='test@orm.org', password='my-pwd')
  > # print(u.__dict__)
  > u.save()
  > ```
  >
  > 实际的类属性
  >
  > ```python
  > # 以上User类中有
  >  __mappings__ = {
  >      "uid": ('uid', "int unsigned")
  >      "name": ('username', "varchar(30)")
  >      "email": ('email', "varchar(30)")
  >      "password": ('password', "varchar(30)")
  >  }
  >  __table__ = "User"
  > ```
  >
  > 将字符串类型的数据添加引号
  >
  > ```python
  > # 方法一：isinstance判断
  >  args_temp = list()
  >      for temp in args:
  >          # args_temp.append("{!r}".format(temp))
  >          # 或者
  >          # 判断入如果是数字类型
  >          if isinstance(temp, int):
  >              args_temp.append(str(temp))
  >          elif isinstance(temp, str):
  >              args_temp.append("'%s'" % temp)
  > 
  >      sql = 'insert into %s (%s) values (%s)' % (self.__table__, ','.join(fields), ','.join(args_temp))
  >      
  > # 方法二：format格式化输出
  >          args.append("{!r}".format(getattr(self, k, None)))
  > 
  >      sql = 'insert into %s (%s) values (%s)' % (self.__table__, ','.join(fields), ','.join([str(i) for i in args]))
  > ```

* 实例：`insert`功能，修改代码结构，抽取到基类中

  > 将初始化、SQL语句生成的函数(方法)抽取到基类中`Model`中，然后对于不同的表，创建一个不同的类，类中只需要定义包含字段信息的类属性，从而实现对ORM功能的复用。
  >
  > ```python
  > class ModelMetaclass(type):
  >  def __new__(mcs, name, bases, attrs):
  >      mappings = dict()
  >      # 判断是否需要保存
  >      for k, v in attrs.items():
  >          # 判断是否是指定的StringField或者IntegerField的实例对象
  >          if isinstance(v, tuple):
  >              print('Found mapping: %s ==> %s' % (k, v))
  >              mappings[k] = v
  > 
  >      # 删除这些已经在字典中存储的属性
  >      for k in mappings.keys():
  >          attrs.pop(k)
  > 
  >      # 将之前的uid/name/email/password以及对应的对象引用、类名字
  >      attrs['__mappings__'] = mappings  # 保存属性和列的映射关系
  >      attrs['__table__'] = name  # 假设表名和类名一致
  >      return type.__new__(mcs, name, bases, attrs)
  > 
  > 
  > class Model(object, metaclass=ModelMetaclass):
  >  def __init__(self, **kwargs):
  >      for name, value in kwargs.items():
  >          setattr(self, name, value)
  > 
  >  def save(self):
  >      fields = []
  >      args = []
  >      for k, v in self.__mappings__.items():
  >          fields.append(v[0])
  >          args.append("{!r}".format(getattr(self, k, None)))
  >          # if k in self.__dict__:
  >          #     fields.append(v[0])
  >          #     args.append(self.__dict__[k])
  > 
  >      sql = 'insert into %s (%s) values (%s)' % (self.__table__, ','.join(fields), ','.join([str(i) for i in args]))
  > 
  >      print('SQL: %s' % sql)
  > 
  > 
  > class User(Model):
  >  uid = ('uid', "int unsigned")
  >  name = ('username', "varchar(30)")
  >  email = ('email', "varchar(30)")
  >  password = ('password', "varchar(30)")
  > 
  > class Goods(Model):
  >  pass
  > 
  > u = User(uid=12345, name='Michael', email='test@orm.org', password='my-pwd')
  > # print(u.__dict__)
  > u.save()
  > ```

* 注意

  * 类属性的属性名最好为字段名(也可以为其他)，值为一个存储了字段名、字段数据类型、字段约束的元组；
  * 元类`ModelMetaclass`实现的功能是修改了`User`类的类属性，变为了两个类属性，table和mapping，分别对应数据库的表名和字段映射；
  * 元类的返回值要喂`type(name, bases, attrs)`或`type.__new__(cls, name, bases, attrs)`方法重用；
  * `__init__()`中创建实例对象时要使用`setattr(self, name, value)`，不能通过`self.name = value`；
  * 由于在数据库中，字符串类型数据要加`''`，而直接`getattr()`得到的字符串没有`''`，所以需要添加该功能，`args_temp.append("{!r}".format(temp))`。

---