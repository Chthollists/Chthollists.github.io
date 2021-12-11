---
title: Python高阶语法系列CP7——知识点扩充：模块与MRO(2/4)
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Python
  - Advanced
tags:
  - Python
  - MRO
abbrlink: 98bf047c
date: 2020-08-25 09:16:28
top:
---

---

* Python高阶语法系列CP7——知识点扩充：模块与MRO(2/4)

* CP7主要分享了

* m模块导入

  * 路径搜索与`sys.path`

  * 私有变量与`from module_name import *`
  * 同名模块重新导入：reload()
  * `__import__()`动态导入加载模块、类、函数

* 函数传递时的拆包：`*args, **kwargs`

* 单继承的父类方法覆盖、super()调用父类方法

* 多继承时的MRO顺序

<!-- more -->

# Python高阶语法系列CP7——知识点扩充：模块与MRO(2/4)

### 2. 模块导入进阶

* 时间：2020.08.25

#### 2.1 模块导入时的路径搜索

* 搜索顺序

  * 在当前目录，即当前执行的程序文件所在目录下查找；

  * 到 PYTHONPATH（环境变量）下的每个目录中查找；

    > Python会先按照系统 path 变量的路径去查找，然后再按照用户 path 变量的路径去查找。

  * 到 Python 默认的安装目录下查找。

* Python自动搜索的路径查看：sys模块

  > ```python
  > import sys
  > 
  > print(sys.path)
  > ```

* 找不到模块所在路径的解决方法

  * 向 sys.path 中临时添加模块文件存储位置的完整路径；

    > ```python
    > import sys
    > 
    > print(sys.path)
    > # 添加在path的末尾
    > sys.append('module_path')
    > # 添加在开头
    > sys.path.insert(0, 'module_path')
    > ```

  * 将模块放在 sys.path 变量中已包含的模块加载路径中；

    > 一般将扩展模块添加在sys.path中的 `lib\site-packages` 路径下，该路径专门用于存放 Python 的扩展模块和包。

  * 设置 path 系统环境变量

    * Windows 

      > 直接在环境变量设置里添加 PYTHONPATH变量，值为模块的路径

    * Linux

      > Linux 平台的环境变量是通过 .bash_profile 文件来设置的，在home路径下打开.bash_profile 文件，添加 PYTHONPATH 环境变量，值为模块的路径。
      >
      > 然后在.bash_profile 文件末尾添加导出 PYTHONPATH 变量的语句：
      >
      > ```bash
      > export PYTHONPATH
      > ```
      >
      > 最后重启Linux平台，或执行：
      >
      > ```bash
      > source.bash_profile
      > ```

* 注意

  * 向 sys.path 中添加路径只能在执行当前文件的窗口中生效，即临时生效；
  * 添加变量要主要使用`\`对`\`进行转义。

#### 2.2 同名模块重新加载

* 问题

  > 模块⼀旦导⼊，就会被导⼊当前⽂件，随后开始执行，期间如果模块修改，需要重新加载模块文件才能生效；
  >
  > 修改后，即使通过`import`再次导入模块，也不能执行修改后的代码，这是由于Python解释器的规则，对于同名模块，只有第一次导入生效，从而避免重复导⼊⼀个模块带来的其他问题。

* 解决方法

  > 如果要实现同名模块的重新加载，需要使用imp包下的reload模块
  >
  > ```python
  > from imp import reload
  > reload('module_name')
  > ```

#### 2.3 模块导入方式扩展

* 导入方式：

  * `import module_name`
  * `from module_name import func_name`
  * `from module_name import *`

* `__import__('module_name')`

* 函数：dir(模块名)

  > 查看该模块中包含的所有子模块名、函数名、变量名，以列表类型返回。
  >
  > ```python
  > dir("module_name")
  > ```

* 模块中变量私有化

  * 对于一个模块，如果有的变量不希望被其他人导入，可以设置为私有变量；

  * 变量私有化方法：`_variable_name`

    > 增加下划线

  * 私有化生效导入方式：`from module_name import *`

    > ```python
    > from test import *
    > print(age)
    > print(_name)  # NameError : not defined
    > ```

  * 注意

    > 只有`from module_name import *`这种导入方式才能实现变量私有化；
    >
    > 使用`from module_name import _variable_name`可以导入"私有"变量。

* import和from...import的对比

  * import  模块名

    > 直接引用了源模块的变量、函数、类，修改变量会影响到源模块中的对应变量。

  * from 模块名 import *　　

    > 拷贝源模块的变量、函数、类到当前模块，修改变量不会影响到源模块中的对应变量。

  * 当一个模块需要被多个模块调用时，使用import，否则无法同步对变量的操作。

* `__import__('module_name')`

  * 作用

    > `__import__()`函数用于动态加载类和函数 ；
    >
    > 如果一个模块经常变化就可以使用该函数来动态载入。

  * 注意：

    * 返回值为模块，class module；
    * 输入的参数为模块名，必须为str类型；
    * 适用于Web服务器调用Web框架应用程序时

  * 语法格式：`__import__(name[, globals[, locals[, fromlist[, level]]]])`

    > ```python
    >  # 导入web框架的主模块
    >  web_frame_module = __import__(web_frame_module_name)  # 'web_frame'
    >  # 获取那个可以直接调用的函数(对象)
    >  app = getattr(web_frame_module, app_name)   # 'application'
    > ```

---

### 3. 函数可变参数传递时的封包与拆包

* 时间：2020.08.25

* 拆包

  > 用于分开接收tuple(爬虫时正则匹配的返回项)、dict中的值

* 函数参数传递时的拆包

  * 可变参数 *args 、**kwargs  默认会封包过程

  * 如果想要继续传递该可变参数到下一个函数，传递的时候 `func(*args,**kwargs)`

    > ```python
    > def func1(*args, **kwargs):
    >  pass
    > 
    > def func2(*args, **kwargs):
    >  print('args=', args, 'kwargs=', kwargs)
    >  # 如果不加*，传入的为元组和字典参数，默认组合为args元组形式传入下个函数
    >  # func1(args, kwargs)
    >  func1(*args, **kwargs)
    >  
    > func2(22, 33, a = 233, b = 666)
    > ```

---

### 4. 类的继承与MRO顺序

* 时间：2020.08.26

#### 4.1 单继承中的super()

* super()的实质

  > super既不是关键字也不是函数等其他数据结构，而是一个类class；
  >
  > 调用 super()的时候，实际上是实例化了一个super类，是用于调用父类(超类)的一个方法，无返回值。

* super()的语法

  * 含参：`super(type[, object-or-type]).方法名(args)`

    > type为类，取值为当前所在的类名；
    >
    > object-or-type一般用self；
    >
    > 在调用的父类中，如果用到了子类中重新赋值过的变量，则使用子类中变量的值。

  * 无参：`super().方法名(args)`

    > 无需参数，Python3的新功能

* 单继承super调用父类方法的顺序

  > 顺序：从存在该方法的上一层父类一直查询到最顶层的父类，执行顺序为最顶层父类一直往下
  >
  > 实际上遵循MRO的规则

  > ```python
  > class A:
  >  def __init__(self):
  >      self.n = 2
  > 
  >  def add(self, m):
  >      print('self is {0} @A.add'.format(self))
  >      self.n += m
  > 
  > 
  > class B(A):
  >  def __init__(self):
  >      super().__init__()
  >      self.n = 3
  > 
  >  def add(self, m):
  >      print('self is {0} @B.add'.format(self))
  >      super().add(m)
  >      self.n += 3
  > 
  > def single_inheritance():
  >  b = B()
  >  b.add(2)
  >  print('b.n = ', b.n)
  > 
  > print('--' * 10 + '单继承' + '--' * 10)
  > single_inheritance()     # b.n = 3+2+3 = 8
  > ```

#### 4.2 多继承中的MRO

* 子类与父类初始化问题

  * 在子类的一些方法中，要使用父类属性

    > 父类初始化：`父类名.__init__(self, args)`

  * 在子类的一些方法中，不使用父类属性，而使用子类重新复制的同名属性

    > 子类初始化：`self.__init__(args)`
    >
    > 在已经调用过执行了父类初始化的方法后，如果不进行子类初始化，子类重写的属性会被父类覆盖

* 多继承

  * 概念

    > 多继承是⼀个子类同时继承自多个父类，⼜称菱形继承、钻⽯继承。

  * 问题

    > 多继承中，如果复杂的继承结构中每一级的子类都对上级的父类的某些方法进行调用，从而使得同一个父类的某些方法被多次调用，就会使同样的代码多次执行，浪费资源。

  * 解决方法

    > **MRO顺序 + super()**

* MRO顺序

  * 概念

    > MRO顺序是通过算法将复杂结构上的所有类映射为一个线性的顺序，从而简化继承的关系，使每个类出现一次。
    >
    > MRO的搜索算法为C3算法(非广度优先)，新式类才具有，经典类的搜索算法为广度优先。

  * 查看MRO的方法：语法格式

    * `类名.__mro__`
    * `类名.mro()`

    > 返回值都是元组，其中的元素为所查询的类的按线性顺序排序的父类

  * super()与MRO

    > super()实质上是一个类，包含了两个非常重要的信息，提供一个 MRO 以及一个 MRO 中的类 C；

  >super() 是使用MRO这个顺序去调用当前类在MRO顺序中下⼀个类；
  >
  >super() 将返回一个从 MRO 中 C 之后的类中查找方法的对象。

    * 一个 MRO顺序：即super所在的当前类的MRO顺序
    * MRO 中的一个类：即super所在的当前类

* 多继承实例

  > ```python
  > class A:
  >  def __init__(self):
  >      self.n = 2
  > 
  >  def add(self, m):
  >      print('self is {0} @A.add'.format(self))
  >      self.n += m
  > 
  > 
  > class B(A):
  >  def __init__(self):
  >      super().__init__()
  >      self.n = 3
  > 
  >  def add(self, m):
  >      print('self is {0} @B.add'.format(self))
  >      super().add(m)
  >      self.n += 3
  > 
  > 
  > class C(A):
  >  def __init__(self):
  >      super().__init__()
  >      self.n = 4
  > 
  >  def add(self, m):
  >      print('self is {0} @C.add'.format(self))
  >      super().add(m)
  >      self.n += 4
  > 
  > 
  > class D(B, C):
  >  def __init__(self):
  >      super().__init__()
  >      self.n = 5
  > 
  >  def add(self, m):
  >      print('self is {0} @D.add'.format(self))
  >      super().add(m)
  >      self.n += 5
  > 
  > 
  > def single_inheritance():
  >  b = B()
  >  b.add(2)
  >  print('b.n = ', b.n)
  > 
  > 
  > def multiple_inheritance():
  >  d = D()
  >  d.add(2)
  >  print('d.n = ', d.n)
  > 
  > #  (<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>)
  > print(D.__mro__)  
  > print('--' * 10 + '单继承' + '--' * 10)
  > single_inheritance()                              #  b.n = 3+2+3 = 8
  > print('--' * 10 + '多继承' + '--' * 10)
  > multiple_inheritance()                          #  d.n = 5+2+4+3+5 = 19
  > ```

* 注意

  * 使用super()时，要记得传递函数的参数；

  * 如果父类和子类的同名方法的参数个数不同：

    * 需要在各级父类的参数列表中添加*args和**kwargs

    * 子类调用父类方法时要传入所有参数，上一级的父类的super中也要添加*args和**kwargs

      > ```python
      > class A():
      >  def __init__(self, name, *args, **kwargs):
      >      self.name = name
      >      pass
      >  
      > class B(A):
      >  def __init__(self, name, age, *args, **kwargs):
      >      self.age = age
      >      super().__init__(name, age, *args, **kwargs)
      >      pass
      >  
      > class C(B):
      >  def __init__(self, name, age):
      >      super().__init__(name, age)
      >      pass 
      > ```

  * 在单继承中，使用super()和父类名调用父类方法是区别不大的，而在多继承中，要尽可能避免使用父类名调用父类方法，因为会使得父类方法中的代码被多次执行，浪费资源，更多地使用super()来调用父类方法。

---