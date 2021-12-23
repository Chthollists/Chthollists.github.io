---
title: Python高阶语法系列CP1——迭代器与生成器
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Python
  - Advanced
tags:
  - Python
  - Iterator
  - Generator
abbrlink: de8ac5da
date: 2020-08-15 13:14:58
top:
---

---

* Python高阶语法系列CP1——迭代器与生成器
* CP1主要分享了
  * 可迭代对象(Iterable)：iter
  * 迭代器(Iterator)：iter、next
  * 生成器(Generator)：yield、return、send
  * 实例：斐波那契数列、素数、回文数

<!-- more -->

# Python高阶语法系列CP1——迭代器与生成器

## 一、迭代器与生成器

* 时间：2020.08.14~2020.08.16

### 1. 可迭代对象(Iterable)

* 时间：2020.08.14

* 概念与特点

  * 可遍历对象就是可迭代对象 
  * 列表、元组、集合、字典、字符串、range(n)都是可迭代对象
  * int、float、bool和自定义myclass对象，默认都是不可以迭代的

* 检测方法：isinstance()函数

  * 一个对象是否可以迭代，用isinstance() 函数检测，需要导入collections模块的Iterable类
  * 可迭代对象类——Iterable
  * 语法格式： `isinstance(数据,Iterable)`

* 可迭代对象：

  > ```python
  > from collections import Iterable
  > from collections import Iterator
  > 
  > list1 = [1, 5, 9, 233, 666]
  > print(isinstance(list1, Iterable))
  > print(isinstance(tuple(list1), Iterable))
  > print(isinstance(set(list1), Iterable))
  > print(isinstance(dict(), Iterable))
  > print('-' * 20)
  > ```

* 不可迭代对象

  > ```python
  > # for i in Iterable:
  > # TypeError: 'int' object is not iterable
  > ```

* 类

  * 自定义的Class类，默认是不可迭代对象

    > ```python
    > class Myclass(object):
    >  pass
    > 
    > myclass = Myclass()
    > print(isinstance(myclass, Iterable))  # False
    > print('-' * 20)
    > ```

  * myclass 对象所属的类 MyClass 如果包含了` __iter__() `方法，此时myclass就是一个可迭代对象

    > ```python
    > class MyclassIter(Myclass):
    >  # __iter__() 实质上就是为可迭代对象提供一个可以获得迭代器的方法
    >  def __iter__(self):
    >      return
    > 
    > myclass_iter = MyclassIter()
    > print(isinstance(myclass_iter, Iterable)) # True
    > print('-' * 20)
    > ```

* 可迭代对象的本质

  > 对象所属的类中包含了iter() 方法

---

### 2. 迭代器(Iterator)

* 时间：2020.08.14

#### 2.1 迭代器的概念与特点

* 概念：

  > 迭代是访问集合元素的⼀种⽅式。迭代器是⼀个可以记住遍历的位置的对象。

* 特点：

  > 迭代器对象从集合的第⼀个元素开始访问，直到所有的元素被访问完结束。
  >
  > 迭代器只能往前不会后退。

* 迭代器的作用：

  > 记录当前迭代的位置、配合next() 获取可迭代对象的下一个元素值

* 可迭代对象Iterable--->迭代器Iterator---iter(Iterable)--->可迭代对象Iterable中的元素---next(Iterator)

#### 2.2 获取迭代器

* 获取可迭代对象的迭代器

  > 语法格式：`iter(可迭代对象Iterable)`
  >
  > ```python
  > # 获取可迭代对象的迭代器
  > list1_iter = iter(list1)
  > print(list1_iter)   
  > # <list_iterator object at 0x00000140EC46C7B8>
  > ```

* 判断一个对象是否为迭代器

  > ```python
  > print(isinstance(list1_iter, Iterator))
  > ```

* 获取可迭代对象的值

  > 语法格式：`next(迭代器Iterator)`
  >
  > ```python
  > print(next(list1_iter))
  > print(next(list1_iter))
  > ```

* for 循环的本质：

  * 通过 iter(要遍历的对象) 获取要遍历的对象的迭代器 
  * next（迭代器）获取下一个元素
  * 捕获了 StopIteration 异常

#### 2.3 自定义迭代器类

* 自定义迭代器类 MyIterator 要满足：

  * 必须含有` __iter__()`
  * 必须含有 `__next__()`

* 特点：

  * 当调用iter(迭代器对象)时，会调用对象的`__iter__()`方法
  * 当调用next(迭代器对象)时，会调用对象的`__next__()`方法
  * 迭代器自身正是⼀个迭代器，所以迭代器的`__iter__`方法返回自身即可---return self

* 实例

  > ```python
  > from collections import Iterable
  > 
  > class MyIterator(object):
  >  # 必须含有 __iter__()
  >  # 必须含有 __next__()
  >  def __init__(self, items):
  >      # 保存数据到当前类的实例变量中
  >      self.items = items
  >      # 初始化迭代器默认下标
  >      self.current_index = 0
  > 
  >  def __iter__(self):
  >      return self
  > 
  >  # 当 next(迭代器) 的时候，会自动调用该方法
  >  def __next__(self):
  >      # 判断下标是否越界
  >      if self.current_index < len(self.items):
  >          # 根据下标index获取列表的元素的值
  >          data = self.items[self.current_index]
  >          # 下标+1
  >          self.current_index += 1
  >          return data
  >      # 如果下标越界，捕获异常
  >      else:
  >          raise StopIteration
  > ```

* 应用：自定义列表类——可迭代

  * 注意：也可以直接把`__next()__`写到Mylist类中，使其直接为自定义的迭代器，就不需要和另一个class传参了

  > ```python
  > class Mylist(object):
  >  def __init__(self):
  >      self.items = []
  > 
  >  def additem(self, data):
  >      self.items.append(data)
  > 
  >  # 获取对象的迭代器
  >  def __iter__(self):
  >      # 调用自定义的迭代器类MyIterator，创建对象
  >      mylist_iter = MyIterator(self.items)
  >      return mylist_iter
  > 
  >  # __getitem__用于使该类支持使用下标 如：class()[5]
  >  def __getitem__(self, n):
  >      return self.items[n]
  > 
  > 
  > if __name__ == '__main__':
  >  mylist = Mylist()
  >  mylist.additem('I')
  >  mylist.additem('love')
  >  mylist.additem('you !')
  >  print(mylist.items)
  >  print(isinstance(mylist, Iterable))
  > 
  >  print(mylist[2])   # Mylist()[2]
  > 
  >  for value in mylist:
  >      print(value)
  > ```

#### 2.4 迭代器案例——斐波那契数列

* 斐波那契数列：Fibnacci

* 基础版：Fib()  支持索引

  > ```python
  > # 迭代器应用---斐波那契数列(Fibnacci)
  > # 1. __iter__ 用于生成迭代器
  > # 2. __next__ 用于返回下一个元素的值，以及捕获异常
  > # 3. __getitem__ 用于使该迭代器类支持索引
  > # 4. FibImprove中修改__getitem__，加一个切片判断，使其不仅支持索引，也支持切片
  > 
  > class Fib():
  >  def __init__(self, num):
  >      self.a, self.b = 0, 1
  >      self.num = num
  >      # 记录下标位置的实例属性
  >      self.current_index = 0
  > 
  >  def __iter__(self):
  >      return self
  > 
  >  # 用于返回循环的下一个值
  >  def __next__(self):
  >      if self.current_index < self.num:  # 限制数列范围
  >          self.a, self.b = self.b, self.a + self.b
  >          self.current_index += 1
  >          return self.a
  >      else:
  >          raise StopIteration
  > 
  >  # 用于使该类可以使用下标 如：Fib()[5]
  >  def __getitem__(self, n):
  >      a, b = 1, 1
  >      for m in range(n):
  >          a, b = b, a + b
  >      return a
  > ```

* 进阶版：FibImprove(Fib) 支持索引、切片，以及输入值的判断

  > ```python
  > class FibImprove(Fib):
  >  def __getitem__(self, n):
  >      if isinstance(n, int):  # n是索引
  >          a, b = 1, 1
  >          # 索引从0开始
  >          if n >= 0:
  >              for x in range(n):
  >                  a, b = b, a + b
  >              return a
  >          else:
  >              # 不指定return内容的话，return一个None
  >              return '索引值错误(为负数)，请重试！'
  >      if isinstance(n, slice):  # n是切片
  >          start = n.start
  >          stop = n.stop
  >          if start is None:
  >              start = 0
  >          a, b = 1, 1
  >          L = []
  >          if start <= stop:
  >              for x in range(stop):
  >                  if x >= start:
  >                      L.append(a)
  >                  a, b = b, a + b
  >          else:
  >              for x in range(start):
  >                  a, b = b, a + b
  >              for x in range(start-stop):
  >                  a, b = b - a, a
  >                  L.append(a)
  >          return L
  > ```

* 测试：

  > ```python
  > if __name__ == '__main__':
  >  num = int(input('请输入要生成的斐波那契数列的长度:\n'))
  >  n = int(input('请输入要显示的斐波那契数列的索引:\n'))
  >  fib = Fib(num)
  >  # print(type(fib))
  >  print('-' * 30)
  > 
  >  # fib 是一个迭代器，迭代器也是可迭代对象
  >  for value in fib:
  >      print(value)
  >  print('-' * 30)
  > 
  >  # 如果没有__getitem__  报错--TypeError: 'Fib' object does not support indexing
  >  print(Fib(num)[n])
  >  print('-' * 30)
  > 
  >  print(FibImprove(num)[n])
  >  print(FibImprove(num)[5:11])   # [a:b]---a、a+1、...、b-1 共b-a个数
  >  print(FibImprove(num)[11:5])
  > ```

---

### 3. 生成器(Generator)

* 时间：2020.08.15

#### 3.1 生成器概念和创建

* 概念：

  > 生成器 Generator是一种特殊的迭代器
  >
  > next(迭代器) --- 获取下一个值
  >
  > next(生成器) --- 同样可以获取下一个值

* 特点：

  > 可以节省内存、空间

* 创建生成器

  > 两种方式

  * 把一个列表生成式的[]改成()，就创建了一个generator

    > ```python
    > # 列表推导式
    > list1 = [i for i in range(1, 6)]  # for 推导式
    > print(list1)  # [1,2,3,4,5]
    > # 生成器创建(列表生成式)
    > g1 = (i for i in range(1, 6))  # for 推导式
    > print(g1)  
    > # <generator object <genexpr> at 0x0000029ECE2D4468>
    > 
    > # 把生成器转化成列表——一次性显示出生成器对应的所有数据
    > print(list(g1))
    > ```

  * 用yield实现generator

    > 变成generator，在每次调用next()的时候执行，遇到yield语句返回，
    >
    > 再次执行时从上次返回的yield语句处继续执行
    >
    > ```python
    > def test1():
    >  yield 10
    >  
    > g2 = test1()
    > print(g2) 
    > # <generator object test1 at 0x0000023A69A19360>
    > ```

* 生成器创建(yield)特点：

  > 使用了yield关键字的函数不再是函数，而是生成器。

* yield 作用:

  * 保存当前运行状态（断点），然后暂停执行，即将生成器（函数）挂起
  * 将yield关键字后面表达式的值作为返回值返回，此时可以理解为起到了return的作用

* 获取生成器的值

  > ```python
  > # 获取生成器对象的下一个值
  > # print(next(g1))
  > print(next(g2))
  > ```

#### 3.2 生成器的return与唤醒

* 生成器可以使用return关键字，语法上没有问题；

  * 但是如果执行到return语句以后，生成器会停止迭代，抛出停止迭代的异常

  * 用for循环调用generator时，发现拿不到generator的return语句的返回值。

  * 如果想要拿到返回值，必须捕获StopIteration错误，返回值包含在StopIteration的value中

  * Python3中的生成器可以使用return返回最终运行的返回值

  * 而Python2中的生成器不允许使用 return返回⼀个返回值

  * 即可以使用return从生成器中退出，但return后不能有任何表达式

    > ```python
    > def fib(n):
    >  a, b = 0, 1
    >  if n == 0:
    >      yield b
    >  elif n > 0:
    >      for i in range(n):
    >          a, b = b, a + b
    >          flag = yield a
    >          if flag:
    >              return '生成器结束'
    >  else:
    >      yield '输入的数列长度为负数，请重试！'
    >  return 'done'
    > 
    > 
    > G = fib(10)
    > # print('G---->', next(G))
    > print('G---->', G.send(None))
    > ```

* 生成器的唤醒

  * next()-----yield

  * send(参数)

    * 在生成器中：  参数(para) = yield data

    * 在生成器外：  generator.send(para)

  * send的作用

    > 可以用来传入参数，用该参数判断是否结束生成器的工作
    >
    > 使用send启动生成器(第一次)的时候传⼊的参数必须是None，下次启动生成器的时候可以加上参数
    >
    > ⼀般第⼀次启动生成器使⽤next,可以使用next()函数让生成器从断点处继续执行，即唤醒生成器

    > ```python
    > while True:
    >  try:
    >      x = G.send(0)   # flag = yield a   if flag:
    >      print('G:', x)
    >  except StopIteration as e:
    >      # 或者
    >      # except Exception as e:
    >      # print('Generator return value:', e)
    >      print('Generator return value:', e.value)
    >      # 如果next(生成器)执行成功了，e.value 为生成器函数的返回值return
    >      # 如果执行失败(比如之前已经next到了最后)，e.value为return返回的默认值None
    >      break
    > ```

#### 3.3 生成器案例——斐波那契数列

* 斐波那契数列 yield

  > ```python
  > # 生成器应用---斐波那契数列(Fibnacci)
  > def fib(n):
  >  a, b = 0, 1
  >  if n == 0:
  >      yield b
  >  elif n > 0:
  >      for i in range(n):
  >          a, b = b, a + b
  >          yield a
  >  else:
  >      yield '输入的数列长度为负数，请重试！'
  >  return 'done'
  > 
  > 
  > if __name__ == '__main__':
  >  g1 = fib(10)
  >  g2 = fib(0)
  >  g3 = fib(-3)
  >  # 用for循环调用generator时，发现拿不到generator的return语句的返回值。
  >  # 如果想要拿到返回值，必须捕获StopIteration错误，返回值包含在StopIteration的value中
  >  for i in g1:
  >      print(i)
  >  print('-' * 20)
  >  # print(next(g1))
  >  print(next(g2))
  >  print(next(g3))
  > ```

#### 3.4 扩充：回文数、素数判断

* 回文数：IsPalindrome

  > ```python
  > def _not_divisible(n):
  >  return lambda x: x % n > 0
  > # fun1 = _not_divisible(n)
  > # bool1 = fun1(x)
  > 
  > def _not_palindrome(num):
  >  #  s=str(num)
  >  #  return s==s[-1::-1]
  >  s1 = str(num)
  >  flag = True
  >  for i in range(0,len(s1)-1):
  >      if s1[i] != s1[len(s1)-1-i]:
  >          flag = False
  >  return flag
  > 
  > def _odd_iter():
  >  n = 0
  >  while True:
  >      n = n + 1
  >      yield n
  > 
  > def primes():
  >  yield 0
  >  it = _odd_iter() # 初始序列
  >  while True:
  >      # it = filter(_not_palindrome, it)
  >      n = next(it) # 返回序列的第一个数
  >      yield n
  >      it = filter(_not_palindrome, it) # 构造新序列
  > 
  > if __name__ == '__main__':
  >  # 打印回文数:
  >  for n in primes():   # 调用primes()
  >      if n < 200:
  >          print(n)
  >      else:
  >          break
  > ```

* 素数：IsPrimeNum

  > ```python
  > def _not_divisible(n):
  >  return lambda x: x % n > 0
  > # fun1 = _not_divisible(n)
  > # bool1 = fun1(x)
  > 
  > def _odd_iter():
  >  n = 1
  >  while True:
  >      n = n + 2
  >      yield n
  > 
  > def primes():
  >  yield 2
  >  it = _odd_iter() # 初始序列
  >  while True:
  >      n = next(it) # 返回序列的第一个数
  >      yield n
  >      it = filter(_not_divisible(n), it) # 构造新序列
  > 
  > 
  > # 打印1000以内的素数:
  > for n in primes():   # 调用primes()
  >  if n < 1000:
  >      print(n)
  >  else:
  >      break
  > 
  > # print(next(primes()))
  > ```

