---
title: Python基础语法系列CP1——初识Python
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Python
  - Syntax
tags:
  - Python
abbrlink: 6fa17b20
date: 2020-08-03 08:48:10
top:
---

---

* Python基础语法系列CP1——初识Python
* CP1主要分享了Python最基本的语法

<!-- more -->

# Python基础语法系列CP1——初识Python

## 1. 语言类型

### 1.1 解释型语言和编译型语言

#### 1.1.1 机器语言

* 机器语言与高级语言

  > 使用高级语言编写的程序要想被计算机运行，必须将其转换为计算机语言——机器语言(机器码)

  |                机器语言：机器码                 |
  | :---------------------------------------------: |
  |                伪机器语言(汇编)                 |
  | Fortran      Pascal      Basic      C      其他 |
  |      Delphi    Visual Basic    C++    Java      |

* 转换方式
  * 编译
  * 解释

#### 1.1.2 编译型语言

* 概念

  > 使用专门的编辑器，针对特定的平台，将高级语言的代码一次性编译为可执行的机器码，并包装成该平台可以识别的可执行程序的格式。

* 特点

  * 先编译，生成机器码可执行的文件(如，exe文件)，再执行；
  * 编译一次性完成，以后需要在此执行该程序，直接使用之前生成的机器码文件；
  * 运行时脱离开发环境，执行效率高，但是基本上不具备跨平台能力，可移植性低。

* 编译型语言

  * C
  * C++
  * Objective
  * Fortran
  * Pascal、Delphi

* 适用场景：对执行效率要求高

  * 开发操作系统
  * 大型应用程序
  * 数据库系统

#### 1.1.3 解释型语言

* 概念

  > 使用专门的解释器对源程序进行逐行解释，解释的同时立即执行，即是在代码执行的同时被解释器动态编译和执行。

* 特点

  * 编译和执行同时进行，不需要生成机器码可执行程序，直接通过平台提供的解释器运行；
  * 每次执行时都需要重新编译为机器码；
  * 执行效率低，由于解释器的存在，跨平台能力良好，可移植性高。

* 解释型语言

  * Java
  * Python
  * JavaScript
  * Ruby
  * Basic、VB
  * Matlab

* 适用场景：对效率要求不高，对不同系统平台兼容性要求高

  * 网页开发
  * 服务器脚本
  * 辅助开发接口

### 1.2 动态语言与静态语言

* 类型检查

  > 类型检查是验证类型约束的过程，编译器或解释器通常在编译阶段或运行阶段进行类型检查，查看变量及其类型。

* 动态语言(Dynamically Typed Languages)

  * 概念

    > 当类型检查发生在程序运行阶段(run time)时，称为动态语言。

  * 常见动态语言

    * Python
    * JavaScript
    * PHP
    * VB

  * 特点

    > 动态语言都是解释型语言；
    >
    > 不用给变量指定数据类型，动态语言会在第一次赋值给变量时，在内部记录数据类型；
    >
    > 代码简洁，但是不易于调试，代码命名容易混淆。

* 静态语言(Statically Typed Languages)

  * 概念

    > 当类型检查发生在程序编译阶段(compile time)时，称为静态语言。

  * 常见静态语言

    * C、C++、C#
    * Java
    * Scala
    * Delphi

  * 特点

    > 定义变量时需要给出类型；
    >
    > 代码复杂。便于调试。

### 1.3 强类型语言与弱类型语言

* 强类型语言

  * 概念

    > 不管是在编译阶段，还是运行阶段，一旦每种类型绑定到变量后 ，该变量便会持有此类型，并且不能和其他类型在计算表达式中混合使用。

  * 常见强类型语言

    * Python
    * Java
    * C#
    * Scala

* 弱类型语言

  * 概念

    > 与强类型语言相对，不同类型的变量可以在计算表达式中混合使用。

  * 常见弱类型语言

    * C
    * C++
    * PHP
    * JavaScript

### 1.4 Python简介

* 语言类型

  > Python属于解释型语言、动态语言、强类型语言

* 特点

  * 执行效率不高，跨平台性高；
  * 定义变量时不用给出类型；
  * 不同类型变量不能在计算表达式中混合使用；
  * 扩展库丰富，可扩展性强。

* 适用场景

  * 人工智能
  * 数据挖掘
  * 机器学习
  * 深度学习
  * 爬虫
  * 自动化测试
  * 运维

  * GUI开发
  * Web开发

---

## 2. 输入与输出

### 2.1 输出：`print`

* 格式化输出%s %d %f %e

  > ```python
  > a = 10
  > b = 5
  > print('练习%03d' %a,end = '666')
  > print('练习%e,ABA%s' %(a,b))
  > ```


### 2.2 输入：input
* 输入的数据会自动转化为str类型

  > ```python
  > n = input('输入：')
  > ```

---

## 3. 数据类型及转换 

### 3.1 主要数据类型

* 数值类

  * int/long 
  * float 
  * bool 
  * str 

* 容器类

  * 列表 list-[] 

  * 元组 tuple-()

  * 集合 set-{}  

    > 可变`set()`
    >
    > 不可变`frozenset()  `

  * 字典`dict = {'key': 'value', ...}`

### 3.2 数据类型转换

> ```python
> # base 表示进制
> n1 = int(n, base=10)  # int(n) int(n,10)
> print(n1,type(n1))
> 
> # 匹配类型转换
> n2 = eval(n)  
> print(n2)
> 
> # 复数
> n3 = complex(3,imag=2) 
> print(n3)
> 
> # 将对象转换为表达式字符串
> n4 = repr(n) 
> print(n4)
> 
> ```

---

## 4. 条件结构 

* 语法结构：`if elif else:`

  > ```python
  > if n1>3:
  >     d = n1*n1
  > else:
  >     d = n1+1
  > ```

---


## 5. 循环 
### 5.1 while循环

* 语法结构：`while else：`

  > ```python
  > i = 0
  > while i % 2 == 0:
  >     print(i)
  >     i += 1
  > else:
  >     print('abc %d' %i)
  > ```

### 5.2 for循环 
* 语法结构：`for ... in ... ` 和`break  continue`   

  > ```python
  > str = 'abcdefghijk'
  > for j in str:
  >     print(j)
  > else:
  >     print('hi')
  > ```

---

## 6. 其他

### 6.1 range 范围
* `range(m,n,step)`用于生成序列，常与生成器联用

  > ```python
  > range(1,10,1)   #  返回值类型为range
  > for k in range(1,10):
  >     print(k)
  > ```

### 6.2 enumerate  索引对应数据

* 语法：`enumerate('可遍历序列', start = 0)`

* enumerate  返回值类型为元组tuple，元组第一项为index，第二项为对象原数据

  > ```python
  > list1 = ['a','c',5,9,2.33]
  > for k in enumerate(list1):
  >     print(k,type(k))
  > 
  > # 拆包
  > for index,s in enumerate(list1,start=1):
  >     print('%d::%s ---%s' %(index,s,type(s)))
  > ```

### 6.3 切片
* 下标(可正可负)

* 字符串、列表、元组均可切片

  > ```python
  > print(str[0:6])
  > print(str[6:0:-1])
  > print(str[:5:-1])
  > print(str[-1:-3:-1])
  > print(str[-4-:1:1])
  > ```

