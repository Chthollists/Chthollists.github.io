---
title: Python高阶语法系列CP6——知识点扩充：深浅拷贝(1/4)
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Python
  - Advanced
tags:
  - Python
  - DeepCopy
abbrlink: f6b21625
date: 2020-08-24 18:15:36
top:
---

---

* Python高阶语法系列CP6——知识点扩充：深浅拷贝(1/4)
* CP6主要分享了
  * 可变类型与不可变类型
  * 可变类型的深、浅拷贝
  * 不可变类型的深、浅拷贝
  * 切片拷贝
  * 字典拷贝

<!-- more -->

# Python高阶语法系列CP6——知识点扩充：深浅拷贝(1/4)

## 五、其他知识点

* 时间：2020.08.24~2020.08.29

### 1. 浅拷贝与深拷贝

* 时间：2020.08.24

#### 1.1 可变类型与不可变类型

* 概念：

  > 可变与不可变，指的是内存中的值(value)是否可以被改变

* 可变数据类型：mutable

  > 字典、列表、集合
  >
  > 创建后可以继续修改对象的内容
  >
  > 当变量的值发生了变化，其对应的内存地址不变，这种数据类型的变量称为可变数据类型

* 不可变类型：unmutable

  不可变数据类型

  > 数值、字符串、元组
  >
  > 在对不可变类型进行第一次赋值时，会在内存中开辟一个空间来存储这个值，所以该变量实际上存储的是存放这个值的内存地址；当变量的值发生修改时，计算机重新分配一块内存空间
  >
  > 当变量的值发生了变化，其对应的内存地址也发生变换，称为不可变数据类型
  >
  > ```python
  > # 不可变
  > x = 15
  > print('x=', x, id(x))
  > y = x
  > print('y=', y, id(y))
  > x = 18
  > print('x=',x, id(x))
  > print(15, id(15), 18, id(18))
  > # 可变
  > list1 = [1, 2, 3]
  > print('list1=', list1, id(list1))
  > list2 = list1
  > print('list2=', list2, id(list2))
  > list1.append(5)
  > print('listl=', list1, id(list1))
  > ```

#### 1.2 深、浅拷贝

* 浅拷贝

  * 引用（地址）拷贝，并没有产生新的空间；

  * 如果拷贝的是对象，原对象和copy对象都指向同⼀个内存空间，只拷贝父对象，不会拷贝对象的内部的子对象。
  * 语法格式：`copy.copy(对象名)`

* 深拷贝

  * 会产生新的空间，即copy对象与原对象地址不同；

  * 如果拷贝的是对象，原对象和copy对象都指向不同的内存空间，会拷贝对象及其子对象；
  * copy对象具有数据独立性，与原对象互不影响。
  * 语法格式：`copy.deepcopy(对象名)`

* 作用：
  * 浅拷贝减少内存的使用
  * 深拷贝可以确保数据的独立性，copy对象与原对象互不影响
  * 以后在做数据的清洗、修改或者⼊库的时候，对原数据进行复制⼀份，以防数据修改之后，找不到原数据。

#### 1.3 可变类型的深浅拷贝

##### 1.3.1 简单可变类型拷贝

* 特点

  * 对于可变类型，无论是深拷贝还是浅拷贝，都会产生新的空间，
  * copy对象的内存地址都会改变，数据独立，互不影响。

* 浅拷贝

  > ```python
  > import copy
  > 
  > def list_copy():
  >  list1 = [1, 3, 5]
  >  list2 = list1
  >  print('listl=', list1, id(list1))  # listl= [1, 3, 5] 49723752
  >  print('list2=', list2, id(list2))  # list2= [1, 3, 5] 49723752
  > 
  >  list2.append(8)
  >  print('list1=', list1, id(list1))  # list1= [1, 3, 5, 8] 49723752
  > 
  >  list3 = copy.copy(list1)
  >  print('list3=', list3, id(list3))  # list3= [1, 3, 5, 8] 50676240
  > 
  >  list3.append(10)
  >  print('list3=', list3, id(list3))  # list3= [1, 3, 5, 8, 10] 50676240
  >  print('list1=', list1, id(list1))  # list1= [1, 3, 5, 8] 49723752
  > 
  > if __name__ == '__main__':
  >  list_copy()
  > ```

* 深拷贝

  > ```python
  > import copy
  > def list_deepcopy():
  >  list1 = [1, 3, 5]
  >  print('list1=', list1, id(list1))  #  list1= [1, 3, 5] 30222376
  > 
  >  list2 = copy.deepcopy(list1)
  >  print('list2=', list2, id(list2))  # list2= [1, 3, 5] 29276520
  > 
  >  list2.append(10)
  >  print('list2=', list2, id(list2))  # list2= [1, 3, 5, 10] 29276520
  >  print('list1=', list1, id(list1))  # list1= [1, 3, 5] 30222376
  > 
  > 
  > if __name__ == '__main__':
  >  list_deepcopy()
  > ```

##### 1.3.2 复杂可变类型(多层嵌套，子对象)

* 特点

  * 浅拷贝
    * 只能拷贝最顶层的数据(父对象)，为父对象开辟新的空间，无法拷贝子对象，无法为子对象开辟空间；
    * copy对象中的子对象的内存地址与原对象子对象的内存地址相同；
    * 子对象不具有数据的独立性，copy对象与原对象可以互相影响。
  * 深拷贝
    * 拷贝全部对象，为所有对象开辟新空间；
    * copy对象中的子对象的内存地址改变，与原对象中不同；
    * 具有数据独立性，copy对象与原对象互不影响。

* 浅拷贝

  > ```python
  > import copy
  > 
  > def list_copy():
  >  a = [2, 3]
  >  b = [5, 8]
  >  list1 = [1, a, b]
  >  print('list1=', list1, id(list1))  #  list1= [1, [2, 3], [5, 8]] 53225472
  >  print('a=', a, id(a))   # a= [2, 3] 53232504
  >  print('b=', b, id(b))  # b= [5, 8] 53225552
  >  print('list1[1]=', list1[1], id(list1[1]))  #  list1[1]= [2, 3] 53232504
  >  print('list1[2]=', list1[2], id(list1[2]))  #  list1[2]= [5, 8] 53225552
  > 
  >  list2 = copy.copy(list1)
  >  print('list2=', list2, id(list2))    # list2= [1, [2, 3], [5, 8]] 52279616
  >  print('list2[1]=', list2[1], id(list2[1]))   # list2[1]= [2, 3] 53232504
  >  print('list2[2]=', list2[2], id(list2[2]))   # list2[2]= [5, 8] 53225552
  > 
  >  list2[2][0] = 10
  >  print('list2[2]=', list2[2], id(list2[2]))   # list2[2]= [10, 8] 53225552
  >  print('list1=', list1, id(list1))   # list1= [1, [2, 3], [10, 8]] 53225472
  >  print('list2=', list2, id(list2))   # list2= [1, [2, 3], [10, 8]] 52279616
  >  
  >  
  > if __name__ == '__main__':
  >  list_copy()  
  > ```

* 深拷贝

  > ```python
  > import copy
  > 
  > def list_deepcopy_complex():
  >  a = [2, 3]
  >  b = [5, 8]
  >  list1 = [1, a, b]
  >  print('list1=', list1, id(list1))  #   list1= [1, [2, 3], [5, 8]] 30550016
  >  print('a=', a, id(a))   # a= [2, 3] 30557088
  >  print('b=', b, id(b))   # b= [5, 8] 30550096
  >  print('list1[1]=', list1[1], id(list1[1]))   # list1[1]= [2, 3] 30557088
  >  print('list1[2]=', list1[2], id(list1[2]))   # list1[2]= [5, 8] 30550096
  > 
  >  list2 = copy.deepcopy(list1)
  >  print('list2=', list2, id(list2))    # list2= [1, [2, 3], [5, 8]] 30325096
  >  print('list2[1]=', list2[1], id(list2[1]))  # list2[1]= [2, 3] 30504920
  >  print('list2[2]=', list2[2], id(list2[2]))  # list2[2]= [5, 8] 30558728
  > 
  >  list2[2][0] = [10]
  >  print('list2[2]=', list2[2], id(list2[2]))  # list2[2]= [[10], 8] 30558728
  >  print('list2=', list2, id(list2))   # list2= [1, [2, 3], [[10], 8]] 30325096
  >  print('list1=', list1, id(list1))   # list1= [1, [2, 3], [5, 8]] 30550016
  > 
  > 
  > if __name__ == '__main__':
  >  list_deepcopy_complex()
  > ```

#### 1.4 不可变类型的深浅拷贝

##### 1.4.1 简单不可变类型

* 特点：对于不可变类型，无论是深拷贝还是浅拷贝，都不会开辟新的空间，而是直接引用了原对象的内存地址，copy对象与原对象内存地址一致。等价于赋值操作。

* 浅拷贝

  > ```python
  > import copy
  > 
  > def tuple_copy():
  >  tuple1 = (1, 3, 5)
  >  print('tuple1=', tuple1, id(tuple1))  # tuple1= (1, 3, 5) 16357216
  >  tuple2 = tuple1
  >  print('tuple2=', tuple2, id(tuple2))  # tuple2= (1, 3, 5) 16357216
  > 
  >  tuple3 = copy.copy(tuple1)
  >  print('tuple3=', tuple3, id(tuple3))  # tuple3= (1, 3, 5) 16357216
  >  
  > if __name__ == '__main__':
  >  tuple_copy()
  > ```

* 深拷贝

  > ```python
  > import copy
  > 
  > def tuple_deepcopy():
  >  tuple1 = (1, 3, 5)
  >  print('tuple1=', tuple1, id(tuple1))   #  tuple1= (1, 3, 5) 52207408
  > 
  >  tuple2 = copy.deepcopy(tuple1)
  >  print('tuple2=', tuple2, id(tuple2))    #  tuple2= (1, 3, 5) 52207408
  >  
  > if __name__ == '__main__':
  >  tuple_copy()
  > ```

##### 1.4.2 复杂不可变类型(多层嵌套，子对象)

* 特点

  * 浅拷贝：只关心最外层的数据类型，如果是不可变类型，直接引用，没有办法保证数据的独立性
    * 拷贝父对象和子对象，均不产生新的空间；
    * copy对象中的各个对象的内存地址与原对象中对应的对象的内存地址相同；
    * 不具有数据的独立性，copy对象与原对象可以互相影响，等价于赋值操作。
  * 深拷贝：取决于子对象的数据类型，如果存在可变类型，开辟新空间，否则不开辟
    * 拷贝全部对象，为所有对象开辟新空间；
    * copy对象中的子对象的内存地址改变，与原对象中不同；
    * 具有数据独立性，copy对象与原对象互不影响，与可变类型时的深拷贝相同。

* 浅拷贝

  > ```python
  > import copy
  > def tuple_copy_complex():
  >  a = [2, 3]
  >  b = [5, 8]
  >  tuple1 = (1, a, b)
  >  print('tuple1=', tuple1, id(tuple1))   #  tuple1= (1, [2, 3], [5, 8]) 26298848
  >  print('a = %s, %d, b = %s, %d ' % (a, id(a), b, id(b)))   #  a = [2, 3], 26297168, b = [5, 8], 26290216 
  >  print('tuple1[1]=', tuple1[1], id(tuple1[1]))   #  tuple1[1]= [2, 3] 26297168
  >  print('tuple1[2]=', tuple1[2], id(tuple1[2]))   #  tuple1[2]= [5, 8] 26290216
  > 
  >  tuple2 = copy.copy(tuple1)
  >  print('tuple2=', tuple2, id(tuple2))   #  tuple2= (1, [2, 3], [5, 8]) 26298848
  >  print('tuple2[1]=', tuple2[1], id(tuple2[1]))   #  tuple2[1]= [2, 3] 26297168
  >  print('tuple2[2]=', tuple2[2], id(tuple2[2]))   #  tuple2[2]= [5, 8] 26290216
  > 
  >  tuple2[2][0] = 10
  >  print('tuple2[2]=', tuple2[2], id(tuple2[2]))    #  tuple2[2]= [10, 8] 26290216
  >  print('tuple1=', tuple1, id(tuple1))    #  tuple1= (1, [2, 3], [10, 8]) 26298848
  >  print('tuple2=', tuple2, id(tuple2))    #  tuple2= (1, [2, 3], [10, 8]) 26298848
  >  
  > if __name__ == '__main__':
  >  tuple_copy_complex()
  > ```

* 深拷贝

  > ```python
  > import copy
  > 
  > def tuple_deepcopy_complex():
  >  a = [2, 3]
  >  b = [5, 8]
  >  tuple1 = (1, a, b)
  >  print('tuple1=', tuple1, id(tuple1))   # tuple1= (1, [2, 3], [5, 8]) 17451488
  >  print('a = %s, %d, b = %s, %d ' % (a, id(a), b, id(b)))   #   a = [2, 3], 17449808, b = [5, 8], 17442856
  >  print('tuple1[1]=', tuple1[1], id(tuple1[1]))  #  tuple1[1]= [2, 3] 17449808
  >  print('tuple1[2]=', tuple1[2], id(tuple1[2]))  #  tuple1[2]= [5, 8] 17442856
  > 
  >  tuple2 = copy.deepcopy(tuple1)
  >  print('tuple2=', tuple2, id(tuple2))  #  tuple2= (1, [2, 3], [5, 8]) 46907144
  >  print('tuple2[1]=', tuple2[1], id(tuple2[1]))  #  tuple2[1]= [2, 3] 17217816
  >  print('tuple2[2]=', tuple2[2], id(tuple2[2]))   #  tuple2[2]= [5, 8] 17450008
  > 
  >  tuple2[2][0] = [10]
  >  print('tuple2[2]=', tuple2[2], id(tuple2[2]))   #  tuple2[2]= [[10], 8] 17450008
  >  print('tuple2=', tuple2, id(tuple2))   #  tuple2= (1, [2, 3], [[10], 8]) 46907144
  >  print('tuple1=', tuple1, id(tuple1))   # tuple1= (1, [2, 3], [5, 8]) 17451488
  > 
  > 
  > if __name__ == '__main__':
  >  tuple_deepcopy_complex()
  > ```

#### 1.5 切片拷贝和字典拷贝

* 切片拷贝

  * 语法格式：`obj_copy = obj[:]`

  * 实质：浅拷贝，遵守可变、不可变类型在浅拷贝时的规则。

    > 对于可变类型：为父对象开辟新空间，改变地址，不对子对象开辟新空间，copy对象的子对象不具有独立性；
    >
    > 对于不可变类型：不管子对象为什么数据类型，均不开辟新空间，所有对象地址直接引用原对象地址，不具有独立性。

    > ```python
    > def slice_copy():
    >  a = [2, 3]
    >  b = [5, 8]
    >  tuple1 = [1, a, b]
    >  print('tuple1=', tuple1, id(tuple1))
    >  print('a = %s, %d, b = %s, %d ' % (a, id(a), b, id(b)))
    >  print('tuple1[1]=', tuple1[1], id(tuple1[1]))
    >  print('tuple1[2]=', tuple1[2], id(tuple1[2]))
    > 
    >  # tuple2 = copy.copy(tuple1)
    >  tuple2 = tuple1[:]
    >  print('tuple2=', tuple2, id(tuple2))
    >  print('tuple2[1]=', tuple2[1], id(tuple2[1]))
    >  print('tuple2[2]=', tuple2[2], id(tuple2[2]))
    > 
    >  tuple2[2][0] = 10
    >  print('tuple2[2]=', tuple2[2], id(tuple2[2]))
    >  print('tuple1=', tuple1, id(tuple1))
    >  print('tuple2=', tuple2, id(tuple2))
    > 
    > if __name__ == '__main__':
    >  slice_copy()
    > ```

* 字典拷贝：键值对

  * 语法格式：`obj_copy = obj.copy()`

    > 字典是可变类型，自带copy方法

  * 实质：浅拷贝，遵守可变、不可变类型在浅拷贝时的规则。

    > 对于值为可变类型：等价于复杂可变类型时的情况，字典的值等价于子对象，字典拷贝不为其开辟新空间，copy对象的子对象不具有独立性；
    >
    > 对于值为不可变类型：不管子对象为什么数据类型，均不开辟新空间，所有对象地址直接引用原对象地址，不具有独立性。

    > ```python
    > def dict_copy():
    >  a = [22, 33]
    >  dict1 = {'age': 12, 'num': a}
    >  print('dict1=', dict1, id(dict1))    #  dict1= {'age': 12, 'num': [22, 33]} 54819072
    >  print('a = %s, %d' % (a, id(a)))   #  a = [22, 33], 54835480
    >  print("dict1['num']=", dict1['num'], id(dict1['num']))  #  dict1['num']= [22, 33] 54835480
    >  print("dict1['age']=", dict1['age'], id(dict1['age']))       #  dict1['age']= 12 1606216080
    > 
    >  dict2 = dict1.copy()
    >  print('dict2=', dict2, id(dict2))    #  dict2= {'age': 12, 'num': [22, 33]} 55774688
    >  print("dict2['num']=", dict2['num'], id(dict2['num']))   #  dict2['num']= [22, 33] 54835480
    >  print("dict2['age']=", dict2['age'], id(dict2['age']))    # dict2['age']= 12 1606216080
    > 
    > 
    >  dict2['num'][0] = 666
    >  print("dict2['num']=", dict2['num'], id(dict2['num']))    # dict2['num']= [666, 33] 54835480
    >  print('dict2=', dict2, id(dict2))    #   dict2= {'age': 12, 'num': [666, 33]} 55774688
    >  print('dict1=', dict1, id(dict1))    #  dict1= {'age': 12, 'num': [666, 33]} 54819072
    > 
    > 
    > if __name__ == '__main__':
    >  dict_copy()
    > ```

---