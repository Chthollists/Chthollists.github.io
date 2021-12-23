---
title: Python高阶语法系列CP9——知识点扩充：with与format(4/4)
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Python
  - Advanced
tags:
  - Python
  - ContextManager
abbrlink: 1aab2355
date: 2020-08-29 12:08:45
top:
---

---

* Python高阶语法系列CP9——知识点扩充：with与format(4/4)
* CP9主要分享了
  * 上下文管理器
  * 自定义上下文管理类
  * 用于上下文管理的装饰器
  * format格式化输出的格式符与语法

<!-- more -->

# Python高阶语法系列CP9——知识点扩充：with与format(4/4)

### 6. With上下文管理

* 时间：2020.08.28

#### 6.1 上下文管理

* 上下文(context)概念

  > 上下文和语文中的语境概念类似。
  >
  > 以 with open 来说，打开文件在上文，关闭文件在下文

* 上下文管理器

  * 实质

    > 上下文管理器的实质就是能够⽀持with操作。

  * 特点

    > 任何实现了`__enter__()`和`__exit__()`方法的对象都可称之为上下文管理器；
    >
    > `__enter__()`方法表示上文，`__exit__()`方法表示下文
    >
    > 上下⽂管理器对象可以使用`with`关键字；
    >
    > ⽂件(file)对象也实现了上下文管理器协议。

* 适用场景

  * 文件IO操作
  * socket创建关闭
  * 数据库的connect与close

#### 6.2 自定义上下文管理类

* open()

  > open就是一个用于打开关闭文件的上下文管理器。

  > ```python
  > with open ('url', 'r') as file:
  >  file.read()
  > ```

* 自定义上下文管理类

  * `__enter__()`

  * ``__exit__()`

    > ```python
    > # 2. 自定义上下文管理类
    > # 功能：打开文件、关闭文件
    > def test_open():
    >  class MyFile(object):
    >      def __init__(self, file_path, file_mode, encoding = 'utf8'):
    >          self.file_path = file_path
    >          self.file_mode = file_mode
    >          self.encoding = encoding
    > 
    >      # 上文管理：打开文件，定义并返回实例属性file，表示被请求文件资源的open对象
    >      # file = open('data.txt', 'r', encoding='utf8')
    >      def __enter__(self):
    >          self.file = open(self.file_path, self.file_mode, encoding=self.encoding)
    >          return self.file
    > 
    >      # 下文管理：关闭文件
    >      def __exit__(self, exc_type, exc_val, exc_tb):
    >          self.file.close()
    > 
    >  with MyFile('data.txt', 'r', encoding='utf8') as file:
    >      data = file.read()
    >      print(type(data), type(data.split('\t')))
    >      data_modify = data.split('\t')
    >      data_modify[-1] = data_modify[-1].strip()
    >      print(data_modify)
    >      print(list(map(lambda x: float(x), data_modify)))
    > 
    > 
    > if __name__ == '__main__':
    >  test_open()
    > ```

#### 6.3 装饰器方式实现上下文管理

* `contextmanager`装饰器

  > 模块`contextlib`中提供了一个`contextmanager`装饰器，进⼀步简化了上下文管理器的实现方式。
  >
  > ```python
  > from contextlib import contextmanager
  > @contextmanager
  > def func_name():
  >  pass
  > ```

* 被装饰的函数

  > 通过`yield`将函数分割成两部分，`yield`之前的语句在`__enter__`方法中执行，表示上文；
  >
  > `yield`之后的语句在`__exit__`方法中执行，表示下文；
  >
  > 紧跟在`yield`后面的值是函数的返回值。
  >
  > ```python
  > def my_open(file_path, file_mode, encoding='utf8'):
  >      file = open(file_path, file_mode, encoding=encoding)
  >      yield file
  >      file.close()
  > ```

* 文件IO操作

  > ```python
  > from contextlib import contextmanager
  > 
  > 
  > def test_context():
  >  @contextmanager
  >  def my_open(file_path, file_mode, encoding='utf8'):
  >      file = open(file_path, file_mode, encoding=encoding)
  >      yield file
  >      file.close()
  > 
  >  with my_open('data.txt', 'r', encoding='utf8') as file:
  >      data = file.read()
  >      data_modify = data.split('\t')
  >      data_modify[-1] = data_modify[-1].strip()
  >      print(data_modify)
  >      print(list(map(lambda x: float(x), data_modify)))
  > 
  > 
  > if __name__ == '__main__':
  >  test_context()
  > ```

---

### 7. format格式化输出

* 时间：2020.08.29

#### 7.1 基本知识

* 概念

  > 相对基本格式化输出采用`%`的方法，format()功能更强大，该函数把字符串当成一个模板，通过传入的参数进行格式化，并且使用大括号`{}`和`:`作为特殊字符代替`%`。

* 语法格式

  * `输出格式.format(输出内容)`
  * `format(输出内容, 输出格式)`

* 特点

  * format 函数可以接受不限个参数，位置可以不按顺序；
  * 可以通过具体位置、字典映射、列表索引等方法来格式化输出参数的顺序；
  * 可以向format()中传入实例对象。

#### 7.2 格式化输出语法

* 位置参数顺序输出

  > ```python
  > # 不设置指定位置，按默认顺序
  > print("{} {}".format("hello", "world")) 
  > # 设置指定位置
  > print("{1} {0} {1}".format("hello", "world"))
  > ```

* 字典、列表方式输出

  * 字典方式：输出格式设为字典的key，可以使用关键字参数，或者在需要格式化输出的字典前加`**`，如果format()中的字典有多个，key值不能重复。
  * 列表方式：需要在format前面的输出格式中列表的索引前加上0，即`{0[index]}`，且format()中的待输出列表只能有一个。

  > ```python
  > # 通过关键字参数设置输出
  > print("网站名：{name}, 地址 {url}".format(name="baidu", url="www.baidu.com"))
  > 
  > # 通过字典设置参数
  > site = {"name": "baidu", "url": "www.baidu.com"}
  > print("网站名：{name}, 地址 {url}".format(**site))
  > 
  > # 通过列表索引设置参数
  > my_list = ['baidu', 'www.baidu.com']
  > print("网站名：{0[0]}, 地址 {0[1]}".format(my_list))  # "0" 是必须的
  > ```

* 实例对象方式输出

  > 需要在format前面的输出格式中实例属性前加上`0.`，即`{0.name}`。

  > ```python
  > class Test(object):
  >  def __init__(self, c):
  >      self.a = 10
  >      self.b = 8
  >      self.c = c
  >      
  >   def __str__(self):
  > 	return "{self.a!r}, {self.b!r}".format(self=self)
  > 
  > test = Test(3)
  > print("a = {0.a}, c =  {0.c}".format(test))  # "0" 是必须的
  > ```

* 数字格式化

  * `{:.3f}`：小数点后保留三位

  * `{:+.3f}`：带符号输出，`+-`都输出小数点后保留三位(正数负数)，0输出为+0.00

  * `{:-.3f}`：带符号输出，`-`输出，`+`号省略，小数点后保留三位(正数负数)，0输出为+0.00

  * `{:*>.3d}`：输出3位，右对齐，左侧补`*`，如果没有`*`，默认补充空格

    > `<, >, ^`分别表示左对齐、右对齐，居中对齐

  * `{:0=30}`：输出30位，右对齐，左侧补0，必须是数字才可以使用`=`

  * `{:,}`：以逗号分隔的形式输出，三位分隔一次，一般用于统计人口、金额

  * `{:.2%}`：以百分数形式输出，保留两位小数，默认六位小数

  * `{:.2e}`：以指数形式输出，保留两位小数，默认是保留六位小数

  * `{:b/d/o/x/#x/#X}`：转换进制输出，b/d/o/x分别表示二、十、八、十六进制

  * 总结：

    * `^, <, >` 分别是居中、左对齐、右对齐，后面带宽度；
    * `:`号后面带填充的字符，只能是一个字符，不指定则默认是用空格填充；
    * `+` 表示带符号输出；
    * 可以使用大括号`{}`来转义`{}`。

* 其他

  * `{:c}`：字符。在打印之前将整数转换成对应的Unicode字符串。·
  * `{:g}`：一般格式。将数值以fixed-point格式输出。当数值特别大的时候，用幂形式打印
  * `{:f}`：小数格式输出，默认六位小数。
  * `{:s}`：字符串格式输出

#### 7.3 转换符和时间格式

* 转换符号：用`!`开头，表示将待输出的参数通过转换符对应的函数输出

  * `!r`对应 repr()，输出内容相比str()会多一个`''`；
  * `!s`对应 str()；
  * `!a`对应ascii()。

  > ```python
  > my_list = ['baidu', 'www.baidu.com']
  > print("网站名：{0[0]!r}, 地址 {0[1]!s}".format(my_list))  # "0" 是必须的
  > # 网站名：'baidu', 地址 www.baidu.om
  > ```

* 时间格式输出：`{:%Y-%m-%d %H:%M:%S}`

  > ```python
  > import datetime
  > d = datetime.datetime(2020, 7, 4, 12, 15, 58)
  > '{:%Y-%m-%d %H:%M:%S}'.format(d)
  > # '2020-07-04 12:15:58'
  > ```

---