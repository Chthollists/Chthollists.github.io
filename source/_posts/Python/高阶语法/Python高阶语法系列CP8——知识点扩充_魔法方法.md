---
title: Python高阶语法系列CP8——知识点扩充：魔法属性与方法(3/4)
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Python
  - Advanced
tags:
  - Python
  - OOP
abbrlink: 3d92fe76
date: 2020-08-27 20:20:59
top:
---

---

* Python高阶语法系列CP8——知识点扩充：魔法属性与方法(3/4)
* CP8主要分享了
  * 常见的魔法属性
    * `__doc__`
    * `__module__`、`__class__`
    * `__dict__`
  * 常见的魔法方法
    * `__new__()`：元类
    * `__init__()`、`__call__()`：装饰器
    * `__dir__()`：`__dict__`是dir的子集
    * `__str__()`、`__repr__()`：调用对象时字符串输出
    * `__iter__()`、`__next__()`：迭代器、生成器
  * 用于访问属性的魔法方法：API调用链接
    * `__getattr__(self, name)`、`__getattribute__(self, name)`、`__setattr__(self, name, value)`、`__delattr__(self, name)`
    * 重写该方法时要注意调用父类的该方法
  * 用于索引、字典操作的魔法方法：斐波那契数列
    * `__getitem__()`、`__setitem__()`、`__delitem__()`

<!-- more -->

# Python高阶语法系列CP8——知识点扩充：魔法属性与方法(3/4)

### 5. 魔法属性和方法

* 时间：2020.08.27

#### 5.1 常见魔法属性

> 大多数都是用来查看类或者对象的信息。

* `__doc__`：魔法属性

  * 作用

    * 获取类的描述、说明信息
    * 获取方法(函数)的描述、说明信息

  * 语法格式

    * 类描述：`类名.__doc__` 或者 `对象.__doc__` 
    * 方法描述：`类名.方法名.__doc__` 或者 `对象.方法名.__doc__` 
    * 函数描述：`函数名.__doc__` 

    > ```python
    > # test.py
    > class Goods(object):
    >  """类的描述信息：商品类"""
    > 
    >  def __init__(self, value):
    >      self.price = value
    > 
    >  def get_price(self):
    >      """get_price方法的描述信息：获取价格的方法"""
    >      return self.price
    > 
    > def test_doc():
    >  """__doc__测试函数：test"""
    >  goods = Goods(100)
    >  # 类的描述信息
    >  print(Goods.__doc__)
    >  print(goods.__doc__)
    >  # 类中方法的描述信息
    >  print(Goods.get_price.__doc__)
    >  print(goods.get_price.__doc__)
    >  # 函数的描述信息
    >  print(test_doc.__doc__)
    >  
    >  
    > if __name__ == '__main__':
    >  test_doc()
    > ```

* `__module__` 和`__class__`：魔法属性

  * 作用

    * `__module__` ：获取类、对象、方法、函数所属的模块

    * `__class__`：获取对象所属的类，实质上是返回对象的类型：int、class、function等

      > 如果获取一个类的`__class__`属性，返回值为：<class 'type'>
      >
      > 如果获取一个实例对象的`__class__`属性，返回值为其所属的类，<class '模块名.类名'>
      >
      > 如果获取一个函数、或`类名.方法名`的`__class__`属性，返回值为：<class 'function'>
      >
      > 如果获取一个方法、即`对象.方法名`的`__class__`属性，返回值为：<class 'method'>
      >
      > 如果获取一个变量的`__class__`属性，返回值为变量的类型：int、str等

  * 语法格式

    * `类名/对象/方法名/函数名.__module__`
    * `类名.__class__` 、`变量.__class__`
    * `对象.__class__`
    * `函数名.__class__`、`类名.方法名.__class__`、`对象.方法名.__class__`

    > ```python
    > from test import Goods
    > 
    > def test_module():
    >  """
    >  __module__测试函数：test
    >  __class__测试函数：test
    >  """
    >  goods = Goods(2)
    >  # test-模块名、class type
    >  print(Goods.__module__, Goods.__class__)
    >  # test、模块名.类名
    >  print(goods.__module__, goods.__class__)
    >  # 函数
    >  print(Goods.get_price.__module__, Goods.get_price.__class__)
    >  # 方法
    >  print(goods.get_price.__module__, goods.get_price.__class__)
    >  # 函数
    >  print(test_module.__module__, test_module.__class__)
    > 
    > 
    > if __name__ == '__main__':
    >  test_module()
    > ```

* `__dict__`：魔法属性

  * 作用

    > 以字典的形式显示类或者对象所拥有的所有的属性、方法和其存储的值。
    >
    > 类的`__dict__`属性：类的静态函数、类函数、普通函数、全局变量以及一些内置的属性，不包括实例属性；
    >
    > 对象的`__dict__`属性：实例属性。

  * 特点：

    * 内置的数据类型没有`__dict__`属性；
    * 类的`__dict__`存储所有实例共享的变量和函数(类属性，方法等)；
    * 父类和子类的`__dict__`属性各自存储自己类的属性、方法，同名的属性和方法也各自存储(即地址不同)；
    * 父类和子类的对象的`__dict__`属性存储各自的实例属性。

  * 语法格式：`类名/对象.__dict__`

    > ```python
    > from test import Goods
    > 
    > class Subgoods(Goods):
    >  def get_price(self):
    >      pass
    >  
    > goods = Goods(0)
    > subgoods = Subgoods(0)
    > print(Goods.__dict__)
    > print(goods.__dict__)
    > print(Subgoods.__dict__)
    > print(subgoods.__dict__)
    > ```

#### 5.2 常见魔法方法

* `__new__()`：创建新的内存空间

* `__init__()`：初始化内存空间，即初始化属性

  > 对于一个类，将类实例化为一个实例对象的过程就是分别调用该类的`__new__`和`__init__`方法；
  >
  > 为实例对象创建一个新的内存空间，并将该内存空间初始化；
  >
  > `__new__`和`__init__`合称为构造方法。

  * `__init__()`初始化属性可以传入位置参数、关键字参数、可变参数、不定长参数等

    > ```python
    > # 关键字参数确定，个数较少
    > class Player():
    > 
    >  def __init__(self, age, score, last_name=None, first_name=None):
    >      self.age = age
    >      self.score = score
    >      self.last_name = last_name
    >      self.first_name = first_name
    >      
    > # 关键字参数不确定，或者个数较多        
    > class Player():
    >  def __init__(self, **kwargs):
    >      self.last_name = kwargs.get('last_name')
    >      
    > # 属性未定义
    > class Player:
    >  _VALID_KEYWORDS = {'last_name', 'first_name', 'score'}
    >  def __init__(self, **kwargs):
    >      for keyword, value in kwargs.items():
    >          if keyword in self._VALID_KEYWORDS:
    >              setattr(self, keyword, value)
    >              # self.__dict__[key] = val
    >          else:
    >              raise ValueError(
    >                 "Unknown keyword argument: {!r}".format(keyword))
    > ```

* `__del__()`：当对象在内存中被释放时，自动触发执行

  > 手动调用：执行`del 类名/对象/类名.类属性/对象.实例属性/变量`时可手动调用

  > ```python
  > class A(object):
  >  num1 = 10
  >  def __init__(self, num):
  >      self.num2 = num
  >      
  >  def __del__(self):
  >      print('__del__已执行')
  >      
  > a = A(1)
  > b = 3
  > print(a.num1,a.num2)
  > del A.num1, a.num2, a, b, A
  > # print(A.num1, a.num2, a, b, A)
  > ```

* `__dir__()`

  * 作用

    > 输出一个类、实例对象、变量或者函数(方法)拥有的所有的属性名和方法名，该函数会返回一个包含该类及其父类中的所有属性名和方法名的有序列表。

  * 特点：

    * 不仅输出本类中新添加的属性和方法，还会输出从父类继承得到的属性和方法；

    * 通过类调用` __dir__() `，相比通过实例对象调用` __dir__() `，返回值会缺少实例属性；

    * 与`__dict__`不同，内置的数据类型也可以使用该方法，返回的数据类型是列表；

    * `__dict__`方法输出的属性、方法是`__dir__()`的子集，且对于同一个类，该类+其各个父类+该类实例对象调用`__dict__`方法得到的属性、方法的和与该类的实例对象直接调用`__dir__()`方法的结果相同。

      > ```python
      > from test import Goods
      > 
      > goods_dict = goods.__dict__.keys()
      > Goods_dict = Goods.__dict__.keys()
      > object_dict = object.__dict__.keys()
      >  dict_goods = []
      >  tmp = [goods_dict, Goods_dict, object_dict]
      >  for i in tmp:
      >      dict_goods.extend(i)
      >  print(set(dir(goods)) == set(dict_goods)) # True
      > ```

  * 与内置函数dir()对比

    > 对于一个类的实例属性，使用` __dir__() `方法和 dir() 函数的结果相同，但是返回的列表顺序不同，即dir() 函数实质上是在调用参数对象` __dir__() `方法的基础上，对该方法返回的属性名和方法名做了排序；

  * 语法格式：

    * `类名.__dir__(对象/类名)`
    * `对象.__dir__()`

    > ```python
    > from test import Goods
    > 
    > # 输出元类(类的类)：type 的属性和方法
    > Goods.__dir__(Goods)
    > 
    > # 类调用，结果比实例对象调用少实例属性
    > dir(Goods)
    > 
    > # 实例对象调用，dir()与__dir__()相同：三者等价
    > dir(goods)
    > Goods.__dir__(goods)
    > goods.__dir__()
    > print(set(goods.__dir__())==set(dir(goods))==set(Goods.__dir__(goods)))   # True
    > ```

* `__str__()`和`__repr__()`

  * `__str__()`作用

    > print输出对象名时自动调用，可以将原来输出的实例对象地址信息替换为`__str__()`方法的返回值，一般返回一个描述该实例对象的字符串。

  * `__str__()`与str()函数

    * str()函数将对象转化为字符串形式
    * str()实际上就是调用了对象`__str__()`方法

  * `__repr__()`作用

    > 实例对象会直接调用该方法，同样是用于将实例对象的地址信息替换为`__repr__()`方法的返回值，一般为字符串。

  * `__repr__()`与repr()函数

    * repr() 函数将对象转化为供解释器读取的形式；
    * repr()实际上就是调用了对象`__repr__()`方法

* 注意

  * 默认的object类中，`__str__ `的默认实现就是调用` __repr__ `方法；

  * 列表和字典等容器会调用`__repr__()`，而不是`__str__()`；

  * 在自定义的类中，优先调用`__str__()`，`__str__()`不存在时调用`__repr__()`，都不存在时取父类中优先查找`__str__()`；

  * 在属性后加上`!r` 标记可以使得此属性转换为字符串时，以repr()的方式转换，而不是str()方式；

    > ```python
    > class Parent(object):
    >  a = 0
    >  b = 1
    > 
    >  def __init__(self):
    >      self.a = 2
    >      self.b = 3
    > 
    >  def __str__(self):
    >      return f"{self.a,self.b}str"
    > 
    >  def __repr__(self):
    >      return f'{self.__class__.__name__}('f'{self.a!r}, {self.b!r})'
    > 
    > p = Parent()
    > print(p)   # 调用__str__()
    > print({p})  # 调用__repr__()
    > print([p])   # 调用__repr__()
    > print(str([p])  # 调用__repr__()
    > ```

  * 对于默认的`__str__()`和`__repr__()`方法，即不在自定义的类中重载这两个方法时，str()函数返回的是可读性高的字符串，而repr()函数返回结果应更准确，其结果可以直接在命令行上运行，存在目的在于调试，便于开发者使用。

    > ```python
    > import datetime
    > 
    > today = datetime.date.today()
    > print(today, type(today))  # 2020-08-19 <class 'datetime.date'>
    > print(str(today), type(str(today)))   # 2020-08-19 <class 'str'>
    > print(repr(today), type(repr(today)))   # datetime.date(2020, 8, 19) <class 'str'>
    > ```

* `__iter__()`和`__next__()`

  * 作用

    > 用于使类变成可迭代对象、迭代器，常用于生成器的相关操作。

  * iter()函数和next()函数

    > 实质上就是调用类的`__iter__()`、`__next__()`方法

  * 注意

    > `__iter__()`方法的返回值是自身self(实例对象)，同时存在`__next__()`方法，此时该类为迭代器

* `__call__()`

  * 作用

    > 用于实现类装饰器，使类变成一个callable对象

  * 语法格式：`对象()`

    > 使用`对象()`时会自动调用该方法；

  * 特点

    * 在实现类装饰器时，需要在`__init__()`中传递被装饰的函数名func，初始化为实例属性；

    * 然后在`__call__()`方法中，将返回值设为该函数名实例属性，`return self.func()`；

      此外，此时的`__call__()`方法应该设置成：`__call__(self, *args, **kwargs)`。

#### 5.3 魔法方法属性访问

* 属性访问相关方法

  * `__getattr__(self, name)`: 
    * 访问不存在的属性时调用；
    * `obj.name` ：该属性name在类中不存在。
  * `__getattribute__(self, name)`：
    * 访问属性时调用，`obj.name`；
    * 若该属性存在，继续执行自身代码，返回该属性的值；
    * 若不存在，去调用`__getattr__(self, name)`。
  * `__setattr__(self, name, value)`：
    * 设置实例对象的一个新的属性时调用
    * `obj.name = value`。
  * `__delattr__(self, name)`：
    * 删除一个实例对象的属性时调用
    * `del obj.name`。

  > ```python
  > def test_attr():
  >  class NewGoods(Goods):
  > 
  >      def __getattribute__(self, item):
  >          print('getattribute:属性访问初始调用的方法')
  >          return super().__getattribute__(item)
  > 
  >      def __getattr__(self, item):
  >          print('getattr: 该属性:%s不存在！' % item)
  >          return 'Property:%s is Not Found' % item
  > 
  >      def __setattr__(self, name, value):
  >          print('setattr : 将属性为:%s的对应数据修改为:%s' % (name, value))
  >          # self.__dict__[name] = value
  >          object.__setattr__(self, name, value)
  > 
  >      def __delattr__(self, item):
  >          print('delattr : 删除属性%s。' % item)
  >          super().__delattr__(item)
  > 
  >  new_goods = NewGoods(100)
  >  print(new_goods.price)
  >  print(new_goods.id)
  > 
  >  new_goods.id = '001'
  >  print(new_goods.id)
  > 
  >  del new_goods.id
  >  print(new_goods.id)
  > 
  > 
  > if __name__ == '__main__':
  >  test_attr()
  > ```

* 实例属性相关操作的原理

  * 访问实例属性的实质 ：`obj.name`

    > 访问一个实例属性时，先调用`__getattribute__(self, name)`方法，如果该属性存在，返回该属性的值，如果不存在，进一步去调用`__getattr__(self, name)`，抛出AttributeError异常。

  * 实例属性赋值的实质 ：`obj.name = value`

    > 对实例属性赋值时，包括将类实例化为实例对象调用`__init__()`方法初始化时，会调用`__setattr__(self, name, value)`方法来对实例属性赋值，无论该实例属性是否存在都可以赋值。

    > 实例属性赋值时，可以有两种方法重写`__setattr__(self, name, value)`方法
    >
    > ```python
    > # 方法一：借用__dict__魔法属性，直接修改字典里实例属性对应的值，不会调用自身(递归)，避免死循环
    > def __setattr__(self, name, value):
    >      self.__dict__[name] = value
    >      
    > # 方法二：直接使用基类的__setattr__()覆盖
    > def __setattr__(self, name, value):
    >      super().__setattr__(self, name, value)
    > ```

  * 实例属性的删除实质：`del obj.name`

    > 删除实例属性时，会调用`__delattr__(self, name)`。

* 对应的函数

  * `getattr(object, name[, default])`

    > 调用`__getattr__(self, name)`方法来为获取属性的值，不存在时返回default的值，如果没有设置default，抛出AttributeError异常，object表示实例对象。

  * `setattr(object, name, value)`

    > 调用`__setattr__(self, name, value)`方法来为存在和不存在的属性赋值，object表示实例对象。

  * `delattr(object, name)`

    > 调用`__delattr__(self, name)`方法来删除属性，object表示实例对象，当要删除的属性为类属性时，object为类名。

  * `hasattr(object, name)`

    > 查看类中是否存在name属性，返回布尔型True或False，object表示实例对象。

* 注意

  * 属性访问所指的属性可以使类属性、也可以是实例属性，还可以是从父类继承的属性；

  * 除了`__getattr__(self, name)`方法，可以自定义访问不存在实例属性时的操作，其余有关属性访问的方法不应在自定义的类中重写；

  * 如果重写了属性访问的方法，应该在重载的方法结尾重新调用父类或基类(object)的该方法；

    * `object.__setattr__(self, name, value)`
    * `super().__setattr__(self, name, value)`

  * 如果重写了属性访问`__getattribute__(self, name)`方法，需要将该方法的返回值写为对其基类方法的调用；

    > `return super().__getattribute__(self, name)`

  * 在重写属性访问的方法时，一定要确保不能出现会调用自身方法的语句，否则会出现死循环。

    > ```python
    > # 死循环举例
    > class A(object):
    >  def __init__(self, value):
    >      self.value = value
    > 
    >  def __setattr__(self, name, value):
    >      self.name = value
    >      
    > # 修改解决死循环
    > class A(object):
    >  def __init__(self, value):
    >      self.value = value
    > 
    >  def __setattr__(self, name, value):
    >      self.__dict__[name] = value
    > ```

* 实例：重写dict类

  > ```python
  > class Dict(dict):
  >  '''
  >  通过使用__setattr__,__getattr__,__delattr__
  >  可以重写dict,使之通过“.”调用
  >  '''
  >  def __setattr__(self, key, value):
  >      print("In '__setattr__")
  >      self[key] = value
  >      
  >  def __getattr__(self, key):
  >      try:
  >          print("In '__getattr__")
  >          return self[key]
  >      except KeyError as k:
  >          return None
  >          
  >  def __delattr__(self, key):
  >      try:
  >          del self[key]
  >      except KeyError as k:
  >          return None
  >          
  >  # __call__方法用于实例自身的调用,达到()调用的效果
  >  def __call__(self, key):    # 带参数key的__call__方法
  >      try:
  >          print("In '__call__'")
  >          return self[key]
  >      except KeyError as k:
  >          return "In '__call__' error"
  >          
  > s = Dict()
  > print(s.__dict__)
  > # {}
  > 
  > s.name = "hello"    # 调用__setattr__
  > # In '__setattr__
  > 
  > print(s.__dict__) # 由于调用的'__setattr__', name属性没有加入实例属性字典中。
  > # {}
  > 
  > print(s("name"))    # 调用__call__
  > # In '__call__'
  > # hello
  > 
  > print(s["name"])    # dict默认行为
  > # hello
  > 
  > # print(s)
  > print(s.name)       # 调用__getattr__
  > # In '__getattr__
  > # hello
  > 
  > del s.name          # 调用__delattr__
  > print(s("name"))    # 调用__call__
  > # None
  > ```

* 实例：API调用链接

  > ```python
  > # __getattr__()
  > # 针对完全动态的情况作调用:如---网站搞REST API，比如新浪微博、豆瓣，调用API的URL类似
  > # http://api.server/user/friends
  > # http://api.server/user/timeline/list
  > # 无论API怎么变，SDK都可以根据URL实现完全动态的调用，而且，不随API的增加而改变！
  > class Chain():
  > 
  >  def __init__(self, path=''):
  >      self._path = path
  > 
  >  def __getattr__(self, path):
  >      return Chain('%s/%s' % (self._path, path))
  > 
  >  def __str__(self):
  >      return self._path
  > 
  >  def module_test(self):
  >      pass
  > 
  >  __repr__ = __str__
  > 
  > # 非常方便地调用API
  > print(Chain().status.user.timeline.list)
  > ```

#### 5.4 魔法方法索引、字典操作

* 索引、字典操作相关方法

  * `__getitem__()`：获取
    * 使用`obj[item]`时调用，item一般为key(字典键值)或者index(索引)
    * 可以用来实现索引、key-value查找等功能；
    * 案例：斐波那契数列
  * `__setitem__()`：修改
    * 使用`obj[key/index] = value`时调用；
    * 可以用来实现索引或key-value的赋值、修改等功能；
  * `__delitem__()`：删除
    * 使用`del obj[value]`时调用；
    * 可以用来实现删除索引、key-value功能；

  > ```python
  > def test_item():
  >  class Fib():
  >      def __init__(self, num):
  >          self.a, self.b = 0, 1
  >          self.dict_test = dict(a=1, b=2, c=3)
  >          self.num = num
  >          # 记录下标位置的实例属性
  >          self.current_index = 0
  > 
  >      def __iter__(self):
  >          return self
  > 
  >      # 用于返回循环的下一个值
  >      def __next__(self):
  >          if self.current_index < self.num:  # 限制数列范围
  >              self.a, self.b = self.b, self.a + self.b
  >              self.current_index += 1
  >              return self.a
  >          else:
  >              raise StopIteration
  > 
  >      # 用于使该类可以使用下标 如：Fib()[5]
  >      def __getitem__(self, item):
  >          if isinstance(item, int):  # n是索引
  >              print('索引操作！')
  >              a, b = 1, 1
  >              # 索引从0开始
  >              if item >= 0:
  >                  for x in range(item):
  >                      a, b = b, a + b
  >                  return a
  >              else:
  >                  # 不指定return内容的话，return一个None
  >                  return '索引值错误(为负数)，请重试！'
  >          if isinstance(item, slice):  # n是切片
  >              print('切片索引操作！')
  >              start = item.start
  >              stop = item.stop
  >              if start is None:
  >                  start = 0
  >              a, b = 1, 1
  >              L = []
  >              if start <= stop:
  >                  for x in range(stop):
  >                      if x >= start:
  >                          L.append(a)
  >                      a, b = b, a + b
  >              else:
  >                  for x in range(start):
  >                      a, b = b, a + b
  >                  for x in range(start - stop):
  >                      a, b = b - a, a
  >                      L.append(a)
  >              return L
  >          if isinstance(item, str):
  >              print('字典操作！')
  >              print('key = %s' % item)
  >              return self.dict_test[item]
  > 
  >      def __setitem__(self, key, value):
  >          print('setitem : 将索引或键值为:%s的对应数据修改为:%s' % (key, value))
  >          self.dict_test[key] = value
  >          print('new dict:', self.dict_test)
  >          # return self.dict_test[key]
  > 
  >      def __delitem__(self, key):
  >          del self.dict_test[key]
  >          print('delitem : 删除索引或键值为:%s的对应数据。' % key)
  > 
  > 
  >  fib = Fib(10)
  >  for value in fib:
  >      print(value, end='\t')
  >  print('\n', end='')
  >  print(fib[15])
  >  print(fib[2:6])
  >  print(fib[6:2])
  >  
  >  print('a =', fib['a'])
  >  fib['b'] = 233
  >  print('b =', fib['b'])
  >  del fib['b']
  >  # print('b =', fib['b'])
  >  
  > if __name__ == '__main__':
  >  test_item()
  > ```

---