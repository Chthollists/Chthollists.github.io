---
title: Python高阶语法系列CP4——GIL全局锁
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Python
  - Advanced
tags:
  - Python
  - GIL全局锁
abbrlink: cf16f693
date: 2020-08-18 20:20:18
top:
---

---

* Python高阶语法系列CP4——GIL全局锁
* CP4主要分享了
  * GIL全局解释器锁的概念和限制
  * GIL锁的解决方法
  * gcc编译环境的安装：MinGW
  * Python导入C的扩展库：`my_lib = ctypes.cdll.LoadLibrary('导入库的路径')`
  * Python导入库时遇到32/64位不匹配的解决方法
  * GIL锁面试问题

<!-- more -->

# Python高阶语法系列CP4——GIL全局锁

## 三、GIL锁

* 时间：2020.08.21

### 1. GIL锁简介

* 时间：2020.08.21

#### 1.1 GIL锁引入

* 查看CPU信息

  > Linux 系统：`htop`
  >
  > Windows 系统 ：`systeminfo`、`wmic`、`cpu get *`

* CPU与进程、线程

  * 单进程——单核CPU

  * 多进程——多核CPU

  * 多线程——多核CPU，但是每个CPU使用率较低

    > 当程序为死循环时，单进程、多进程的单核、多核CPU使用率都为100%
    >
    > 而多线程的多核CPU使用率只有百分之几十，说明在多线程时，CPU是轮询处理各个线程的
    >
    > 即同一时间只有一个CPU在处理线程，这是由于GIL锁的限制。

#### 1.2 GIL锁的概念

* GIL锁概念

  * GIL：Global Interpreter Lock，全局解释器锁
  * 任何Python线程执行前，必须先获得GIL锁，然后，每执行100条字节码，解释器就⾃动释放GIL锁，让其他线程有机会执行。
  * 这个GIL全局锁实际上把所有线程的执行代码都给上了锁，所以，多线程在Python中只能交替执行，使得多线程实现多任务时，在同一时间只能有一个线程使用一个CPU，即CPU轮询执行每个线程。

* GIL锁与Python

  * GIL是Python解释器（Cpython）时引入的概念，在JPython、PyPy解释器中没有GIL；
  * GIL并不是Python的语言缺陷，是解释器层级的锁，跟Python语⾔特性无关；
  * GIL就是为了锁定整个解释器内部的全局资源，每个线程想要运行首先要获取GIL，而GIL本身又是⼀把互斥锁，造成所有线程只能⼀个⼀个one-by-one-并发-交替的执行。

* GIL锁作用

  * 早期计算机都是单核CPU设计，GIL锁可以提高程序的稳定性

  * CPython在执行多线程的时候并不是线程安全的，所以为了程序的稳定性，加⼀把全局解释锁，能够确保任何时候都只有⼀个Python线程执行(一个CPU)

    > 在CPython解释内部运行多个线程的时候，每个线程都需要解释器内部申请相应的全局资源，由于C语⾔本身比较底层造成CPython在管理所有全局资源的时候并不能应对所有线程同时的资源请求，因此为了防止资源竞争而发生错误，对所有线程申请全局资源增加了限制全局解释器锁。

* GIL锁的缺陷

  * GIL对计算密集型的程序会产生影响。因为计算密集型的程序，需要占用系统资源；
  * GIL的存在，相当于始终在进行单线程运算，降低程序运算的速度、效率； 
  * GIL对IO密集型程序影响不大，因为IO操作(input/output)的瓶颈在于输入所耗费的时间，线程大部分时间在等待，所以它们是多个⼀起等(多线程)还是单个(单线程)是区别不大的。

---

### 2. GIL锁解决方法

* 时间：2020.08.21

#### 2.1 GIL锁释放

* 在当前线程执行超时后会自动释放 
* 在当前线程执行阻塞操作时会自动释放 
* 当前执行完成时，自动释放，去轮询下一个线程

#### 2.2 GIL锁解决方案

* 方法一：使用JPython、PyPy等没有GIL锁限制的解释器。

* 方法二：使用多进程代替多线程，虽然单个进程中仍然存在GIL锁，但是多进程可以同时利用多个CPU资源，执行速度增加，但是由于子进程会复制主进程的资源，所以会有资源占用的问题。

* 方法三：通过Python的胶水特性，将子线程要执行的部分用C语言编写，从而跨过CPython解释器。

  * 配置gcc编译环境

    > Linux系统自带gcc，Windows下要自行安装配置，参考[Windows 下使用 GCC](https://www.cnblogs.com/valor-xh/p/7371710.html)

    * 安装MinGW，MinGw 是 Minimal GNU on Windows 的缩写，允许在 GNU/Linux 和 Windows 平台生成本地的 Windows 程序而不需要第三方运行时库。

      > 下载 min-gw 安装程序：[下载链接](http://sourceforge.net/projects/mingw/files/)，下载 Download mingw-get-setup.exe 并安装

    * 配置环境变量，将MinGW/bin配置到系统环境变量Path中

    * 查看MinGW安装情况，在cmd中键入mingw-get，如果弹出 MinGw installation manager 窗口，说明安装正常。此时，关闭 MinGw installation manager 窗口，否则接下来的步骤会报错。

      > ```bash
      > $ mingw-get
      > ```

    * 安装gcc，在cmd中键入mingw-get install gcc

      > ```bash
      > $ mingw-get install gcc
      > ```

  * 将子线程要处理的部分写为C语言，例如：

    > ```c
    > # include <stdio.h>
    > int main (void)
    > {
    > 	printf("hello world!!!");
    > 	return 0;
    > }
    > ```

  * 使用gcc将其编译为so文件(Share Object)

    > 语法格式：`gcc 要编译的文件名 [-编译的格式] [-o] 保存的文件名`
    >
    > -o 表示输出、编译格式有多种：S、E、I、默认为exe链接
    >
    > ```bash
    > $ gcc test.c -shared -o libtest.so
    > ```

  * 在Python中加载该C语言so库文件

    > 语法格式：`my_lib = ctypes.cdll.LoadLibrary('导入库的路径')`
    >
    > ```python
    > import ctypes
    > import threading
    > 
    > # 加载C语言编写的库so文件
    > my_lib = ctypes.cdll.LoadLibrary('./libtest.so')
    > 
    > # 子线程
    > t1 = threading.Thread(target=my_lib.main)
    > 
    > t1.daemon = True
    > 
    > t1.start()
    > ```

  * 注意：

    * **如果出现 OSError: [WinError 193] %1 不是有效的 Win32 应用程序错误**

    * 原因：Python位数与so位数不匹配，so是32位而python是64位，该错误同样会在一些其他库的导入时出现。

    * 解决方法：创建虚拟环境，安装对应位数的Python，利用conda更方便

      > ```bash
      > # 查看planform位数32/64
      > $ conda info
      > # 查看Python的位数
      > $ python
      > # 设置conda的platform位数为32，切换为64位只需要赋值为0
      > $ set conda_force_32bit=1
      > # 创建虚拟环境并安装Python
      > $ conda create -n py37by32 python = 3.7
      > # 激活虚拟环境
      > $ activate py37by32
      > ```
      >
      > 同时修改项目的解释器路径为新建的32位Python路径。

---

### 3. GIL面试常见问题

* 时间：2020.08.21

* 描述Python中GIL的概念，以及它对Python多线程的影响？
  * GIL是全局解释器锁，每个线程
  * 因此，GIL锁使得Python的多线程并发实际上为单线程运算，不能高效的利用CPU资源，降低程序运行的速度，对于计算密集型程序，影响较大，对于IO密集型，影响较小。
  * GIL当遇到IO操作等引起的阻塞、执行超时、执行完成时会自动释放，
* 编写⼀个多线程抓取网页的程序，并阐明多线程抓取是否可以比单线程性能有提升，并解释原因。
  * 多线程爬虫，主要使用urllib模块请求网页资源、re模块正则匹配要获取的信息、pymysql模块用于将爬去的信息保存到数据库、threading模块用于实现多线程爬取信息。
  * 多线程爬虫比单线程效率高，虽然由于GIL锁的存在，多线程的实质也是同一时间只有一个线程在执行代码，但是爬虫过程中存在大量的IO操作，GIL锁遇到IO操作引起的阻塞会自动释放，使得某个线程阻塞时，会有另一个线程在工作，从而提高效率。
* GIL锁与手动设置的Lock
  * GIL锁和手动设置的锁同样是互斥锁，都是用于防止程序运行过程中出现资源竞争而导致出现错误的问题
  * GIL锁是CPython解释器自带的全局锁，无法由用户控制其的锁定释放位置，所以仍需要对需要锁定的操作设置threading.Lock()