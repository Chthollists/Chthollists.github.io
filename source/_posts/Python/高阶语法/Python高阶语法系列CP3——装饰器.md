---
title: Python高阶语法系列CP3——装饰器
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Python
  - Advanced
tags:
  - Python
  - closure
  - Decorator
  - Property
  - functools
abbrlink: 9fe65dd
date: 2020-08-18 20:18:30
top:
---

---

* Python高阶语法系列CP3——装饰器
* CP3主要分享了
* 函数闭包
* 装饰器(含参、返回值)
* 装饰函数名：@functools.wraps(func)`
* 装饰器工厂：外部变量
* 多重装饰器
* 类装饰器：init、call
* 类中Property属性的实现：装饰器方式、类属性方式
* 类方法：classmethod
* 静态方法：staticmethod

<!-- more -->

# Python高阶语法系列CP3——装饰器

## 二、装饰器

* 时间：2020.08.17~2020.08.20

### 1. 函数闭包(closure)

* 时间：2020.08.17

#### 1.1 基础知识

* 函数的本质

  > 函数名实际上是一个特殊的变量，存储函数在内存中的首地址；
  >
  > 函数名()表示通过函数名保存的地址，调用函数，执行函数体；
  >
  > 函数名是函数代码空间的引用，当函数名赋值给⼀个对象时，就是引用传递。

* 闭包的概念

  > 闭包(closure)是函数式编程的重要的语法结构；
  >
  > 在⼀个外函数中定义了⼀个内函数，内函数里运用了外函数的临时变量，并且外函数的返回值是内函数的引用(内函数名)，这样就构成了⼀个闭包；
  >
  > 闭包实质上就是⼀个嵌套定义的函数，在外层运行时才开始内层函数的定义，然后将内部函数的引用传 递函数外的对象。

* 闭包的作用

  > 闭包可以用来在⼀个函数与⼀组“私有”变量之间创建关联关系。在给定函数被多次调用的过程中，这些私有变量能够保持其持久性。

* 闭包的构成条件

  * 必须有⼀个内嵌函数(函数里定义的函数），即函数嵌套 
  * 内嵌函数必须引用⼀个定义在闭合范围内(外部函数中)的变量，即内部函数引用外部函数的变量 
  * 外部函数必须返回内嵌函数的引用，即必须返回内部函数的函数名

* 闭包的优势

  * 闭包可以提高代码的可复用性；
  * 闭包可以减少传递的参数个数，增加代码的可移植性；
  * 闭包可以在⼀个函数与⼀组“私有”变量(外函数变量)之间创建关联，使私有变量保持持久性；

  > 注意：由于闭包引用了外部函数的局部变量，所以需要及时释放外部函数的局部变量，以免消耗内存。

#### 1.2 闭包中的变量问题

* 闭包内函数变量与外函数变量同名：nolocal

  > 当闭包的内函数调用外函数的变量时：
  >
  > 如果在内函数中先定义了(赋值)与外函数变量同名的变量，则内函数中调用的外函数变量的值为内函数中赋值后的值；
  >
  > 如果在内函数中先调用外函数变量，再给其同名变量赋值，则会抛出异常：local variable  referenced before assignment——本地变量在赋值前已经引用；
  >
  > 如果需要既使用外函数变量的值，又需要在内函数中定义同名变量，需要使用nolocal关键字。

  > ```python
  > """
  > 1.存在函数嵌套关系
  > 2.内层函数引用了外层函数的变量
  > 3.外层函数返回内层函数的地址
  > """
  > 
  > def function_out(num):
  >  def function_in():
  >      
  >      # 造成错误的原因是：编译器认为内层函数已经定义了 num 变量，优先使用内层
  >      # 如果在内层定义了和外层同名的变量，而且需要使用外层的变量
  >      # nonlocal 不使用内层函数的变量，而是使用函数的变量
  >      
  >      nonlocal num
  > 
  >      print(" function_out num = ", num)
  >      # 内部自定义的变量
  >      num = 88
  >      print(" function_in num = ", num)
  > 
  >  return function_in
  > 
  > # 调用外层函数
  > ret = function_out(66)
  > # 调用内层函数
  > ret()
  > ```

---

### 2. 装饰器(Decorator)

* 时间：2020.08.18

#### 2.1 基础知识

* 装饰器概念

  > 在不改变函数的代码前提下，给函数添加新的功能，本质上是一个闭包函数。，本质上是一个闭包函数。

* 装饰器使用条件

  * 存在闭包
  * 存在需要扩展功能(装饰)的普通函数

* 装饰器作用

  > 满足封闭开放原则：在不改变原函数的前提下(封闭)，扩展功能(开放)。

* 装饰器使用方法

  > 语法格式：
  >
  > ```python
  > # 设置装饰器  @闭包的外函数名——@func_deco
  > @func_out
  > def func_deco():
  >  pass
  > # 调用 要装饰的函数——func_deco 装饰函数
  > func_deco()
  > ```

* 装饰器原理：函数名引用的传递

  * `@func_out`语句底层原理

    > ```python
    > func_deco = func_out(func_deco)
    > ```

  * `func_deco()`等价于

    > ```python
    > func_in()
    > ```

* 装饰器完整语法

  > ```python
  > def func_out(func):
  >  def func_in():
  >      # 新功能
  >      # xxxxx
  >      # 原始功能 func = func_deco
  >      func()
  >  return func_in
  >  
  > @func_out
  > # 等价于 func_deco = func_out(func_deco)
  > def func_deco():
  >  pass
  > # 调用 要装饰的函数func_deco()
  > func_deco()
  > # 等价于func_in()
  > ```

* 装饰器应用场景

  * 引⼊日志
  * 函数运行时间统计
  * 执行函数前预备处理
  * 执行函数后清理功能 
  * 权限校验等场景
  * 缓存

#### 2.2 装饰函数含参数

> 内层函数和调用被装饰的函数时，都传入相应参数即可

* 装饰的函数含有位置参数

  > ```python
  > # 装饰器含参数
  > def func_out(func):
  >  def func_in(num):
  >      # 新功能
  >      # xxxxx
  >      # 原始功能 func = func_deco
  >      print('funcin:', num)
  >      func(num)
  > 
  >  return func_in
  > 
  > @func_out
  > # 等价于 func_deco = func_out(func_deco)
  > def func_deco(num):
  >  print('deco:', num)
  > 
  > # 调用 要装饰的函数func_deco()
  > func_deco(233)
  > ```

* 参数为可变参数

  > ```python
  > # 装饰器含参数
  > def func_out(func):
  >  def func_in(*args, **kwargs):
  >      # 新功能
  >      # xxxxx
  >      # 原始功能 func = func_deco
  >      print('funcin: args=', args)
  >      print('funcin: kwargs=', kwargs)
  >      func(*args, **kwargs)
  > 
  >  return func_in
  > 
  > @func_out
  > # 等价于 func_deco = func_out(func_deco)
  > def func_deco(*args, **kwargs):
  >  print('deco: args=', args)
  >  print('deco: kwargs=', kwargs)
  > 
  > # 调用 要装饰的函数func_deco()
  > func_deco(22, a=33)
  > ```

#### 2.3 装饰函数有返回值

* 在闭包的内函数中也加入return 被装饰的函数来实现

  > ```python
  > def func_out(func):
  >  def func_in(a, b):
  >      # 新功能
  >      # xxxxx
  >      # 原始功能 func = func_deco
  >      print('funcin:a+b')
  >      return func(a, b)
  >  return func_in
  > 
  > @func_out
  > # 等价于 func_deco = func_out(func_deco)
  > def func_deco(a, b):
  >  print('deco:a+b')
  >  return a+b
  > 
  > # 调用 要装饰的函数func_deco()
  > result = func_deco(22, 33)
  > print(result)
  > ```

#### 2.4 装饰器工厂

* 概念：在原有装饰器上设置外部变量，即在装饰器外层再嵌套一个函数

* 语法：

  * 外部添加的函数(如，log(path))的返回值为原装饰器的外层函数(func_out)

  * 在向装饰函数(如，login)设置装饰器的时候需要用

    > ```python
    > # 设置装饰器
    > @log(path)
    > # 等价于：
    > log(path)
    > @func_out  # 等价于 login = func_out(login)
    > ```

  * 注意：设置装饰器时不可以使用@log，除非通过判断修改返回的装饰器外函数

    > ```python
    > if isinstance(path, str):  # 如果传递的参数是path的话
    >  return decorator
    > else:  # 如果传递的只是函数名
    >  return decorator(path)  # path 其实是函数名
    > ```

* 作用：

  > 可以通过外部新嵌套的函数，向装饰器内部传入参数

* 装饰函数的函数名

  * 在装饰器设置时，会改变装饰函数的函数名，将其改为引用传递后的函数名，即内函数的函数名

  * 查看函数名的方法：`函数名.__name__`

  * 为了不改变装饰函数的函数名，使用`@functools.wraps(func)`，func为装饰器外函数的参数(函数名)

  * 注意：使用`@functools.wraps(func)`后，装饰器的内函数的函数名会变成装饰函数的函数名

    > ```python
    > # 装饰函数名被改变
    > def func_out(func):
    >  def func_in():
    >      print('func_in:', func_in.__name__)   #  func_in: func_in
    >      func()
    >  return func_in
    > 
    > @func_out
    > # 等价于 func_deco = func_out(func_deco)
    > def func_deco():
    >  print('func_deco:', func_deco.__name__)    # func_deco: func_in
    >  print('func_out:', func_out.__name__)         # func_out: func_out
    > 
    > func_deco()
    > ```
    >
    > ```python
    > # 装饰函数不变
    > import functools
    > 
    > def func_out(func):
    >  @functools.wraps(func)
    >  def func_in():
    >      print('func_in:', func_in.__name__)   #  func_in: func_deco
    >      func()
    >  return func_in
    > 
    > @func_out
    > # 等价于 func_deco = func_out(func_deco)
    > def func_deco():
    >  print('func_deco:', func_deco.__name__)    # func_deco: func_deco
    >  print('func_out:', func_out.__name__)         # func_out: func_out
    > 
    > func_deco()
    > ```

* 装饰器工厂通用版——添加路径与日志(运行时间)

  > ```python
  > # 装饰器工厂：在原有装饰器的基础上增加一个外部变量
  > # 即在外层在嵌套一个函数
  > # 显示程序执行的开始、结束，显示函数执行时间
  > 
  > import functools
  > import time
  > import sys
  > 
  > 
  > def log(path):  # log装饰器
  >  path_head = sys.argv[0]
  > 
  >  def decorator(fn):
  >      text = 'Execute'
  > 
  >      @functools.wraps(fn)
  >      # 因为fn=log("text")(fn)=decorator(fn)=counter, 也就是函数变量相当于是重新定义了
  >      # 所以需要把原始函数的__name__等属性复制到counter()函数中
  >      def counter(*args, **kw):
  >          print("程序开始执行".center(20, '*'))
  >          start = time.time()
  >          # print("程序开始执行的时间为：%s" %start)
  >          if isinstance(path, str):
  >              print(" %s  %s...... " % (text, fn.__name__))
  >          else:
  >              print(" Execute %s......" % fn.__name__)
  >          result = fn(*args, **kw)
  >          end = time.time()
  >          print("程序结束执行".center(20, '*'))
  >          print('%s executed in %s ms' % (fn.__name__, end - start))
  >          return result
  > 
  >      return counter
  > 
  >  if isinstance(path, str):  # 如果传递的参数是path的话
  >      print(path_head + "/" + path)
  >      return decorator
  >  else:  # 如果传递的只是函数名
  >      print(path_head)
  >      return decorator(path)  # path 其实是函数名
  > 
  > 
  > # 测试
  > @log("fast.function")  # 传递文本  等价于 fast = log('Execute')(fast)
  > def fast(x, y):
  >  time.sleep(0.0012)
  >  print("第一个程序执行中ing")
  >  return x + y
  > 
  > 
  > @log  # 等价于 slow = log(slow)
  > def slow(x, y, z):
  >  time.sleep(0.1234)
  >  print("第二个程序执行中ing")
  >  return x * y * z
  > 
  > 
  > f = fast(11, 22)
  > s = slow(11, 22, 33)
  > print("fast result=%d, slow result= %d" %(f, s))
  > ```

#### 2.5 多重装饰器

* 概念：

  > 多个装饰器作用于同一个装饰函数

* 语法：注意：先装饰离装饰函数近的装饰器的功能

  > ```python
  > # 先装饰2，再装饰1
  > @decorator1
  > @decorator2
  > ```

* 实例

  > ```python
  > # 多重装饰器
  > def makeBlod(func):
  >  def wrapped():
  >      return '<b>' + func() + '</b>'
  > 
  >  return wrapped
  > 
  > def makeItalic(func):
  >  def wrapped():
  >      return "<I>" + func() + "</I>"
  > 
  >  return wrapped
  > 
  > @makeItalic
  > @makeBlod
  > def text():
  >  return 'Hello World!'
  > 
  > print(text())
  > ```

#### 2.6 类装饰器

* 装饰器的本质

  > 装饰器函数本质上是⼀个接⼝约束，它必须接受⼀个callable对象作为参数，然后返回⼀个callable对象；
  >
  > Python中callable对象：函数、或者重写了`__call__()`方法的对象；
  >
  > 所以，除了通过闭包的函数实现装饰器外，还可以通过重写了`__call__()`方法的类来实现装饰器。

* 类装饰器

  * 原理

    > 对于一个类 class MyDeco():
    >
    > mydeco = MyDeco()会调用`__init__`方法  
    >
    > mydeco() 会调用`__call__`方法
    >
    > 即：`__init__`方法等价于闭包的外函数，`__call__`方法等价于内函数

  * 语法格式：`@类名`

    > ```python
    > class MyDeco(object):
    >  def __init__(self, func):
    >      print('func =', func)
    >      print('func_name =', func.__name__)
    >      self.__func = func
    > 
    >  # 通过在__call__中调用self.__func()来实现装饰函数的原功能
    >  def __call__(self, *args, **kwargs):
    >      # 新功能
    >      print('call!')
    >      # 装饰函数功能
    >      self.__func()
    > 
    > # @MyDeco等价于：
    > # 1. 创建类的实例对象，且将装饰函数的引用(函数名)login传入到__init__当中，func指向装饰函数的原地址
    > # mydeco = MyDeco(login)
    > # 2. 再将装饰函数的引用login指向该实例对象mydeco
    > # login = MyDeco(login) = mydeco  
    > # 因此在调用装饰函数login()时，等价于调用mydeco()，即调用__call__方法
    > 
    > @MyDeco
    > def login():
    >  print('login!')
    > 
    > 
    > login()  # 等价于mydeco()、MyDeco(login)()
    > ```

---

### 3. Property属性

* 时间：2020.08.19

* Property属性实现方法：
  * 装饰器方式
  * 类属性方式

#### 3.1 装饰器基本使用方法

* 概念

  > property是一种特殊的装饰器，使得类中的一些方法可以像属性一样调用；
  >
  > property属性内部进行⼀系列的逻辑计算，最终将计算结果返回；
  >
  > 即调用被装饰的方法时，不必使用method()，直接使用method。

* 作用

  > 将⼀个属性的操作方法封装为⼀个属性，操作被装饰的方法时就和操作普通属性完全⼀致，从而简化对一个类的实例对象的属性的操作。
  >
  > 可以使属性不直接暴露出来。

* 应用场景

  > 在使用类的一些属性的设置set与获取get方法时更加简便。

* 语法格式：直接在想要简化的方法定义前加上`@property`

  > ```python
  > class Student(object):
  >  def __init__(self, name):
  >      self.name =name
  >    
  >  @property
  >    def get_name(self):
  >          return self.name
  > 
  > stu = Student('li')
  > # 没有@property
  > print(stu.get_name())
  > # 有@property
  > print(stu.get_name)
  > ```

* 注意

  * 要想使用@property简化方法，该方法必须是只有一个参数：self
  * 使用@property后，调用该方法时的格式和调用属性相同
  * 如果被装饰的方法有return，则该方法名就等于该返回值，数据类型和内存地址也相同

* 实例：电商网页的分页显示，获取每页显示的起止商品编号

  * 无property装饰器

    > ```python
    > import pymysql
    > 
    > 
    > class Pager(object):
    >  def __init__(self, page_num, page_size):
    >      self.current_pages = page_num
    >      self.pages_size = page_size
    >      self.total_num = self.get_total()
    > 
    >  def get_total(self):
    >      conn = pymysql.connect(host='localhost', user='root', password='ly666996', database='python_test')
    >      cur = conn.cursor()
    >      sql = "select * from areas;"
    >      total_num = cur.execute(sql)
    >      cur.close()
    >      conn.close()
    >      return total_num
    > 
    >  def get_start_num(self):
    >      if (self.current_pages - 1) * self.pages_size >= self.total_num:
    >          print('该页数:%d页不存在！' % self.current_pages)
    >          start_num = None
    >      else:
    >          start_num = (self.current_pages - 1) * self.pages_size + 1
    >      return start_num
    > 
    >  def get_end_num(self):
    >      if (self.current_pages - 1) * self.pages_size < self.total_num <= self.current_pages * self.pages_size:
    >          print('该页数:%d页为最后一页！' % self.current_pages)
    >          end_num = self.total_num
    >      elif self.current_pages * self.pages_size < self.total_num:
    >          end_num = self.current_pages * self.pages_size
    >      else:
    >          print('该页数:%d页不存在！' % self.current_pages)
    >          end_num = None
    >      return end_num
    > 
    >  # 数据库中的商品的索引是从0开始的，但是编号为第一个的商品与索引间要有个转换，如商品编号为1
    >  def read_data(self):
    >      start_num = self.get_start_num()
    >      conn = pymysql.connect(host='localhost', user='root', password='******', database='python_test')
    >      cur = conn.cursor()
    >      sql = "select * from areas limit %d, %d;" %(start_num-1, self.pages_size)
    >      cur.execute(sql)
    >      result = cur.fetchall()
    >      for line in result:
    >          print(line)
    >      cur.close()
    >      conn.close()
    > 
    > 
    > pager = Pager(1, 10)
    > print(pager.total_num)
    > print(pager.get_start_num())
    > print(pager.get_end_num())
    > pager.read_data()
    > ```

  * 使用property装饰器

    > ```python
    > import pymysql
    > 
    > 
    > class Pager(object):
    >  def __init__(self, page_num, page_size):
    >      self.current_pages = page_num
    >      self.pages_size = page_size
    >      self.total_num = self.get_total()
    > 
    >  def get_total(self):
    >      conn = pymysql.connect(host='localhost', user='root', password='ly666996', database='python_test')
    >      cur = conn.cursor()
    >      sql = "select * from areas;"
    >      total_num = cur.execute(sql)
    >      cur.close()
    >      conn.close()
    >      return total_num
    > 
    >  @property
    >  def get_start_num(self):
    >      if (self.current_pages - 1) * self.pages_size >= self.total_num:
    >          print('该页数:%d页不存在！' % self.current_pages)
    >          start_num = None
    >      else:
    >          start_num = (self.current_pages - 1) * self.pages_size + 1
    >      return start_num
    > 
    >  @property
    >  def get_end_num(self):
    >      if (self.current_pages - 1) * self.pages_size < self.total_num <= self.current_pages * self.pages_size:
    >          print('该页数:%d页为最后一页！' % self.current_pages)
    >          end_num = self.total_num
    >      elif self.current_pages * self.pages_size < self.total_num:
    >          end_num = self.current_pages * self.pages_size
    >      else:
    >          print('该页数:%d页不存在！' % self.current_pages)
    >          end_num = None
    >      return end_num
    > 
    >  # 数据库中的商品的索引是从0开始的，但是编号为第一个的商品与索引间要有个转换，如商品编号为1
    >  @property
    >  def read_data(self):
    >      start_num = self.get_start_num
    >      conn = pymysql.connect(host='localhost', user='root', password='ly666996', database='python_test')
    >      cur = conn.cursor()
    >      sql = "select * from areas limit %d, %d;" %(start_num-1, self.pages_size)
    >      cur.execute(sql)
    >      result = cur.fetchall()
    >      for line in result:
    >          print(line)
    >      cur.close()
    >      conn.close()
    >      return 'Done'
    > 
    > 
    > pager = Pager(1, 10)
    > print(pager.total_num)
    > print(pager.get_start_num)
    > print(pager.get_end_num)
    > print(pager.read_data)
    > ```

#### 3.2 装饰器其他使用方法

* 经典类与新式类

  * 经典类概念

    >  Python2及以前版本中的类，如果不是由任意内置类型派生出的类，称为经典类。

  * 新式类概念

    > Python2及以前版本中的类，如果是由任意内置类型派生出的类(只要一个内置类型位于类树的某个位置)，都属于新式类；而Python3中所有类默认都是新式类。

  * 经典类的property装饰器只有一种形式：获取(get)

    * @property

  * 新式类的property装饰器具有三种形式：获取(get)、修改(set)、删除(delete)

    * @property
    * @method_name.setter
    * @method_name.deleter

* property的修改形式

  * 作用

    > 用来装饰需要传入其他参数的方法，从而通过传入的参数对实例属性进行修改

  * 语法格式

    > ```python
    > @method_name.setter
    > def method_name(self, args)
    > 	self.prop = args
    > ```

* property的删除形式

  * 作用

    > 用来装饰实现一个删除属性的方法，从而释放内存

  * 语法格式：

    > ```python
    > @method_name.deleter
    > def method_name(self)
    > 	del self.prop
    > ```

* 实例：出生年与年龄

  > ```python
  > class Student(object):
  >     def __init__(self, date):
  >         self._birth = date
  > 
  >     @property
  >     def birth(self):
  >         return self._birth
  > 
  >     @birth.setter
  >     def birth(self, new_date):
  >         self._birth = new_date
  > 
  >     @birth.deleter
  >     def birth(self):
  >         del self._birth
  > 
  >     @property
  >     def get_age(self):
  >         return 2020 - self._birth
  > 
  > print('-' * 30)
  > s = Student(2000)
  > 
  > print(s.birth)
  > print(s.get_age)
  > s.birth = 1996
  > print(s.birth)
  > print(s.get_age)
  > del s.birth
  > # print(s.birth())   # TypeError: 'int' object is not callable
  > # s.get_age = 25      # error AttributeError: can't set attribute
  > ```

* 注意

  * 要想使用property装饰器的修改、删除功能，需要先设置好@property；
  * 三种形式装饰的方法名必须相同。

#### 3.3 类属性方式

* property()方法概念

  > 通过property()方法可以将一个方法传递给一个类属性，从而通过调用该类属性来调用被传递的方法。

* property()方法特点

  * property()方法可以返回一个property对象，从而将类中的一个方法传递给一个类属性；
  * 当通过类调用该类属性时，值为property对象；
  * 当通过实例对象调用该类属性时，等价于直接调用property()方法作用的方法，执行该方法的代码，与property装饰器效果一致。

* 语法格式：`class_prop = property(method_name1, method_name2, method_name3, 'description')`

  * 参数1：方法名，调用`对象.属性`时自动触发执行该方法，对应@property；
  * 参数2：方法名，调用`对象.属性 = value `时自动触发执行该方法，对应@method_name.setter；
  * 参数3：方法名，调用`del 对象.属性`时，自动触发执行该方法，对应@method_name.deleter；
  * 参数4：字符串，调用`类名.属性.__doc__`时，返回此字符串，表示该属性的描述信息。

* 实例：价格获取修改删除

  > ```python
  > class Goods(object):
  >     def __init__(self, value):
  >         self.price = value
  > 
  >     def get_price(self):
  >         print(self.price, id(self.price))
  >         print('self.get_price:', id(self.get_price))
  >         print('--' * 20)
  >         # self.set_price(1)
  >         return self.price
  > 
  >     def set_price(self, value):
  >         self.price = value
  >         print('self.set_price:', id(self.set_price))
  >         return self.price
  > 
  >     def del_price(self):
  >         print('self.del_price:', id(self.del_price))
  >         del self.price
  >         return 'del done'
  > 
  >     cost = property(get_price, set_price, del_price, 'Property()方法设置类属性')
  > 
  > def property_method():
  >     # 类实例化，实例对象，及其实例属性
  >     goods = Goods(100)
  >     print(goods.price)
  >     # 类.方法名，实例对象.方法名 及其地址
  >     print(Goods.get_price, goods.get_price)
  >     print(id(Goods.get_price), id(goods.get_price))
  >     # 实例方法
  >     print(id(goods.get_price()))
  >     # 类.类属性(property)及其地址
  >     print(Goods.cost, id(Goods.cost))
  >     # 实例对象.类属性(property)及其地址
  >     print(id(goods.cost))
  >     # goods.cost  # 等价于 goods.get_price()
  >     goods.cost = 233  # 等价于 goods.set_price(args)
  >     # print(goods.price, id(goods.price))
  >     print(id(goods.cost))
  >     print(Goods.cost.__doc__)
  >     # print(goods.cost.__doc__)
  >     del goods.cost  # 等价于 goods.del_price()
  >     # print(goods.cost)
  > 
  > 
  > if __name__ == '__main__':
  >     # property_test()
  >     property_method()
  > ```

* 注意：
  * 定义property属性共有两种方式，分别是【装饰器】和【类属性】，而装饰器方式针对经典类和新式类⼜有所不同；
  * 使用property()方法将方法像属性一样调用时，与property装饰器不同，函数名要不同，一般设置为get_name、set_name、del_name这种形式；
  * 调用被传递的实例方法时与装饰器方式不同，只能通过实例对象，不能通过类调用，通过类调用不会执行被传递的函数，而是返回一个property对象。

---

### 4. 类方法和静态方法

* 2020.08.20

#### 4.1 类方法

* 概念

  > 类方法：通过classmethod装饰器修饰的方法

* 作用

  > 使用@classmethod装饰后的方法被称为类方法，类方法既可以被类的实例对象调用，也可以被类直接调用，普通的方法只能被实例对象调用。

* 适用场景

  > 类方法一般用于操作类属性或私有类属性，即该方法不传递实例对象，没有其他参数

* 语法格式

  > ```python
  > class Student(object):
  >     class_id = 414
  > 
  >     def __init__(self, name):
  >         self.name = name
  > 
  >     @classmethod
  >     def get_class_id(cls):
  >         cls().get_name()
  >         return cls.class_id
  > 
  >     def get_name(self):
  >         return self.name
  > 
  > stu = Student('li')
  > # 没有@classmethod
  > print(stu.get_name())
  > print(stu.get_class_id())
  > # print(Student.get_class_id())   # TypeError: get_class_id() missing 1 required positional argument: 'cls'
  > 
  > # 有@classmethod
  > print(stu.get_class_id())
  > print(Student.get_class_id())
  > ```

* 注意

  * 实际上，对于普通的方法，self表示的是实例对象本身，与实例对象内存地址相同；
  * 对于类方法，cls表示的是类本身，与类的内存地址相同。
  * 类方法的参数只能有一个，就是类本身cls。

#### 4.2 静态方法

* 概念

  > 静态方法：通过staticmethod装饰器修饰的方法

* 作用

  > 使用@staticmethod装饰后的方法被称为静态方法，静态方法同样既可以被类的实例对象调用，也可以被类直接调用，普通的方法只能被实例对象调用。

* 适用场景

  > 当方法中既不需要定义或传递实例对象，也不需要定义或传递类对象时，使用静态方法，减少参数传递、内存占用。

* 语法格式

  > ```python
  > class Student(object):
  >     class_id = 414
  > 
  >     def __init__(self, name):
  >         self.name = name
  > 
  >     @classmethod
  >     def get_class_id(cls):
  >         cls().get_name()
  >         return cls.class_id
  > 
  >     def get_name(self):
  >         return self.name
  >     
  >     @staticmethod
  >     def print_info():
  >         # print('说明信息')
  >         return '说明信息'
  > 
  > stu = Student('li')
  > # 没有@staticmethod
  > print(stu.get_name())
  > print(stu.print_info())
  > # print(Student.print_info())   # TypeError: get_class_id() missing 1 required positional argument: 'cls'
  > 
  > # 有@staticmethod
  > print(stu.print_info())
  > print(Student.print_info())
  > ```

