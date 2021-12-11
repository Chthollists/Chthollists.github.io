---
title: Python基础语法系列CP2——字符串
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Python
  - Syntax
tags:
  - Python
abbrlink: 8c346747
date: 2020-08-03 10:41:49
top:
---

---

* Python基础语法系列CP2——字符串
* CP2主要分享了Python中的字符串数据类型的特点和操作

<!-- more -->

# Python基础语法系列CP2——字符串

## 1. 查找与计数

### 1.1 查找：find

* 语法格式：` find('子串')`默认左侧查找

* `rfind('子串')`从右侧查找

  > 查找成功返回子串位置下标
  >
  > 查找失败返回 -1
  >
  > ```python
  > str = 'abcdefghijk poaykjakdljl'
  > a1 = str.find('bc')
  > a2 = str.find('a',10,20)
  > a3 = str.find('sss')
  > print(a1,a2,a3)
  > ```

### 1.2 查找：index 

* 语法格式：` index('子串')`默认左侧查找

* `rindex('子串')`从右侧查找

  > 查找成功返回子串位置下标
  >
  > 查找失败抛出异常——报错  
  >
  > ```python
  > b1 = str.index('bc')
  > b2 = str.index('a',10,20)
  > b3 = str.index('sss')
  > print(b1,b2)
  > ```

### 1.3 查找计数：count

* 语法格式：`count('子串')` 

* 返回子串的个数，包含0

  > ```python
  > c1 = str.count('a')
  > c2 = str.count('a',10,20)
  > print(c1,c2)
  > ```

---

## 2. 字符串修改

### 2.1 替换:replace

* 语法格式：`replace(旧子串，新子串，次数)`

  > ```python
  > str = 'abc and cba and nba and oop'
  > new_str1 = str.replace('and','A')
  > new_str2 = str.replace('and','A',1)
  > new_str3 = str.replace('and','A',100) # 次数超出，替换所有
  > print(str+'\n'+new_str1+'\n'+new_str2+'\n'+new_str3)
  > ```

### 2.2 分割:split

* 语法格式：`split(分割字符，次数)`

  > ```python
  > list1 = str.split(' and')
  > list2 = str.split('and',2)
  > print(list1)
  > print(list2)
  > ```

### 2.3 连接:join

* 语法格式：` '连接字符'.join(多字符串序列-list/tuple/set)`

  > ```python
  > str1 = '...'.join(list1)
  > str2 = ' and'.join(tuple(list1))
  > str3 = ' 666'.join(frozenset(list1))
  > print(str1+'\n'+str2+'\n'+str3)
  > ```
  
* 注意

  > join()操作的序列对象(如：list)，其中的元素必须是str类型，即list1=['1', '2', '3']这种形式，才可以使用join连接。

* 实例：将元素非str型的list用join连接

  > 利用map函数和lambda匿名函数
  >
  > ```python
  > list1 = [1, 2, 3]
  > # 将list中的每个元素转变为str类型，再用join连接
  > str1 = ''.join(map(lambda x:str(x), list1))
  > print(map(lambda x:str(x), list1))   # <map object at 0x0383AD50>
  > print(str1)   # 123
  > print(list(str1))  # ['1', '2', '3']
  > ```

---

## 3. 字符串操作

### 3.1 大小写转换

* 首字母大写：`capitalize()`

* 各个单词首字母大写：` title() `

* 全小写：`lower()`

* 全大写：`upper()`

  > ```python
  > print(new_str1+'\n'+new_str1.capitalize()+'\n'+new_str1.title()+'\n'+new_str1.lower()+'\n'+new_str1.upper())
  > ```

### 3.2 去两端空格 

* 两端均去除：`strip()` 

* 左端去除：`lstrip() `

* 右端去除：`rstrip()`

  > ```python
  > print(str3.strip())
  > ```

### 3.3 字符串对齐  

* 左对齐：`ljust(长度,填充字符) `

* 右对齐：`rjust(长度,填充字符) `

* 居中对齐：`center(长度,填充字符) ` 

  > ```python
  > print('hello'.ljust(10,'.'))
  > print('hello'.rjust(10,'.'))
  > print('hello'.center(10,'.'))
  > ```

---

## 4. 字符串判断

### 4.1 判断开头结尾  

* 开头：`startswith(子串,开始下标,结束下标)`  

* 结尾：`endswith(子串,开始下标,结束下标)`

  > ```python
  > print('hello world '.startswith('he'))
  > print('hello world '.startswith('he',3))
  > print('hello world '.endswith('d '))
  > ```

### 4.2 判断内容

* 全字母：`isalpha()`
* 全空格：`true isspace()`
* 全数字：`isdight()`
* 全字母+数字：`isalnum()`