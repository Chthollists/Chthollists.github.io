---
title: Python高阶语法系列CP2——协程
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Python
  - Advanced
tags:
  - Python
  - Coroutine
  - greenlet
  - gevent
  - urllib
abbrlink: '86835662'
date: 2020-08-16 08:30:58
top:
---

---

* Python高阶语法系列CP2——协程
* CP2主要分享了
  * 协程(Coroutine)的基本概念和作用
  * 协程的创建：greenlet、gevent
  * 协程实战：多协程文件下载器
  * 协程实战：多协程Web服务器

<!-- more -->

# Python高阶语法系列CP2——协程

### 4. 协程(Coroutine)

* 时间：2020.08.16

#### 4.1 协程的概念和作用

* 协程概念

  > 协程，⼜称微线程，纤程（Coroutine），协程就是你可以暂停执行的函数，协程是一个特殊的生成器。
  >
  > 通俗的理解：在⼀个线程中的某个函数，可以在任何地方保存当前函数的⼀些临时变量等信息，然后切换到另外⼀个函数中执行，注意不是通过调用函数的方式做到的，并且切换的次数以及什么时候再切换到原来的函数都由开发者自己确定

* 协程特点：

* 在不开辟新的线程的基础上，实现多个任务

* 协程作用

  * 对于多线程应用，CPU通过切片的方式来切换线程间的执行，线程切换时需要耗时(存状态，下次继续)。
  * 协程，则只使⽤⼀个线程(单线程)，在⼀个线程中规定某个代码块执行顺序

* 适用场景

  > 当程序中存在大量不需要CPU的操作时（IO），适用于协程；

#### 4.2 协程的创建

* yield 方式

  > ```python
  > #  yield 方式
  > import time
  > 
  > # 创建生成器    work1
  > def work1():
  >  while True:
  >      print("正在执⾏work1------------")
  >      yield
  >      time.sleep(0.5)
  > 
  > 
  > # 创建生成器    work2
  > def work2():
  >  while True:
  >      print("正在执⾏work2----")
  >      yield
  >      time.sleep(0.5)
  > 
  > 
  > if __name__ == '__main__':
  >  # 获取生成器
  >  w1 = work1()
  >  w2 = work2()
  >  while True:
  >      # 通过生成器获取下⼀个值
  >      next(w1)
  >      next(w2)
  > ```

* Greenlet 模块

  * Greenlet是python的⼀个C扩展，来源于Stackless python，旨在提供可自行调度的‘微线程’，即协程。
  * generator实现的协程在yield value时只能将value返回给调用者(caller)。
  * 而在greenlet中，target.switch(value)可以切换到指定的协程(target)，然后yield value。
  * greenlet⽤switch来表示协程的切换，从⼀个协程切换到另⼀个协程需要显式指定。
  * 语法格式：
    * 创建：`g1 = greenlet.greenlet(func)`
    * 切换：`g1.switch(args)`

  > ```python
  > # 1. 导入模块
  > import greenlet
  > import time
  > 
  > 
  > # 2. 创建任务
  > def work1(n):
  >  for i in range(n):
  >      print("[%d]正在执⾏work1------------" % i)
  >      # yield
  >      time.sleep(0.5)
  >      # 5 遇到阻塞时切换任务
  >      g2.switch()
  > 
  > 
  > def work2():
  >  while True:
  >      print("正在执⾏work2----")
  >      # yield
  >      time.sleep(0.5)
  >      # 5. 遇到阻塞时切换任务
  >      # g1.switch(args)
  >      g1.switch()
  > 
  > 
  > if __name__ == '__main__':
  >  # 3. 创建greenlet对象
  >  # greenlet.greenlet(函数名/任务名)
  >  g1 = greenlet.greenlet(work1)
  >  print(g1) # <greenlet.greenlet object at 0x000002C8D162C5A0>
  >  g2 = greenlet.greenlet(work2)
  > 
  >  # 4. 启动第一个任务
  >  g1.switch(5)
  > 
  > ```

* Gevent模块

  * gevent 也是第三方库，自动调度协程，自动识别程序中的耗时操作
  * 其原理是当⼀个greenlet遇到IO(指的是input、output输⼊输出，比如网络、⽂件操作等)操作时，
  * 如访问网络，就自动切换到其他的greenlet，等到IO操作完成，再在适当的时候切换回来继续执⾏。
  * 由于IO操作非常耗时，经常使程序处于等待状态，有了gevent为我们自动切换协程，就保证总有greenlet在运行，而不是等待IO
  * 语法格式：
    * 创建并指派任务：`g1 = gevent.spawn(func, args)`
    * 阻塞：` gevent.sleep(0.5)`

  > ```python
  > # 5.2 导入monkey补丁并破解---放在开头，避免影响其他模块
  > from gevent import monkey
  > # 破解
  > monkey.patch_all()
  > # 猴子补丁monkey用途
  > # 1. 在运⾏时替换⽅法、属性等
  > # 2. 在不修改第三⽅代码的情况下增加原来不⽀持的功能
  > # 3. 在运⾏时为内存中的对象增加patch⽽不是在磁盘的源代码中增加
  > 
  > # 1. 导入模块
  > import gevent
  > import time
  > 
  > 
  > # 2. 创建任务
  > def work1(n):
  >  for i in range(n):
  >      print("正在执⾏work1------------")
  >      # yield
  >      # time.sleep(0.5)
  >      # 5. 因为time.sleep()没有被识别为耗时操作---阻塞，可以通过两种方法解决
  >      # 5.1 将time.sleep()改为其自带的gevent.sleep()
  >      # gevent.sleep(0.5)
  >      # 5.2 导入monkey补丁并破解 from gevent import monkey 、monkey.patch_all()
  >      # 导入破解后就可以识别time.sleep()了
  >      time.sleep(0.5)
  >      # 6. 查看当前正在运行的协程
  >      g_now = gevent.getcurrent()
  >      print(g_now)
  > 
  > 
  > def work2():
  >  while True:
  >      print("正在执⾏work2----")
  >      # yield
  >      # time.sleep(0.5)
  >      # 5. 因为time.sleep()没有被识别为耗时操作---阻塞，所以改为其自带的gevent.sleep()
  >      gevent.sleep(0.5)
  > 
  > 
  > if __name__ == '__main__':
  >  # 3. 创建gevent对象---指派任务
  >  # gevent.spawn(函数名，参数1，参数2，...)
  >  g1 = gevent.spawn(work1, 5)
  >  print(g1)  # <Greenlet at 0x1e8832dce88: work1(5)>
  >  g2 = gevent.spawn(work2)
  >  # 4. 让主线程在协程结束后再结束
  >  g1.join()
  >  g2.join()
  >  # gevent.sleep(5)
  > ```

* 注意：

  * 因为`time.sleep()`没有被gevent识别为耗时、阻塞操作，所以没有自动切换协程。可以通过两种方法解决

    * 将`time.sleep()`改为其自带的`gevent.sleep()`

    * 导入monkey补丁并破解 `from gevent import monkey `、`monkey.patch_all()`，导入破解后就可以识别time.sleep()了

      > ```python
      > #导入monkey补丁并破解---放在开头，避免影响其他模块
      > from gevent import monkey
      > # 破解
      > monkey.patch_all()
      > ```

  * 猴子补丁monkey用途

    * 在运行时替换方法、属性等
    * 在不修改第三方代码的情况下增加原来不⽀持的功能
    * 在运行时为内存中的对象增加patch而不是在磁盘的源代码中增加

#### 4.3 协程实战——并发版多协程文件下载器

* 下载请求：urllib.request模块

  > 使用urllib.request模块请求网络资源
  >
  > urllib.request.urlopen(url地址)  返回的是二进制数据

* 基础版：使用循环来下载，无并发、无多协程

  > ```python
  > # 文件下载器(图片——不使用多任务，只用循环
  > """
  > 主函数 main()
  > 1、定义要下载的图片路径
  > 2、调用文件下载的函数，专门下载文件
  > 
  > 文件下载函数   image_download(img_url, name):
  > 1、循环---根据url地址请求网络资源
  > 2、在本地创建文件，准备保存
  > 3、读取网络资源数据 （循环）
  > 4、把读取的网络资源写入到本地文件中
  > 5、做异常捕获
  > 
  > """
  > # 导入猴子补丁
  > from gevent import monkey
  > 
  > monkey.patch_all()
  > 
  > # 导⼊urllib模块
  > import urllib.request
  > import gevent
  > 
  > 
  > # 文件下载函数
  > def image_download(img_url, name):
  >  # 1、根据url地址请求网络资源
  >  # 使用urllib.request模块请求网络资源
  >  # urllib.request.urlopen(url地址)  返回的是二进制数据
  >  for i in range(len(img_url)):
  >      url = img_url[i]
  >      file_name = name[i]
  >      try:
  >          response = urllib.request.urlopen(url)
  >          # response : <http.client.HTTPResponse object at 0x000001C8E2D618D0>
  >          print(response)
  >      # 5、做异常捕获
  >      except Exception as e:
  >          # <class 'urllib.error.HTTPError'> ---type(e)
  >          print('文件%s下载失败！ %s' % (file_name, e))
  >      else:
  >          # 2、在本地创建文件，准备保存
  >          with open(file_name, 'wb') as img_file:
  >              # 3、读取网络资源数据 （循环）
  >              while True:
  >                  img_data = response.read(1024)
  >                  # 4、把读取的网络资源写入到本地文件中
  >                  if img_data:
  >                      img_file.write(img_data)
  >                  else:
  >                      break
  >          print('文件%s下载成功！' % file_name)
  > 
  > def main():
  >  # 1、定义要下载的图片路径
  >  img_url1 = 'http://qefz7x7v9.bkt.clouddn.com/Chtholly06.jpg'
  >  img_url2 = 'http://qefz7x7v9.bkt.clouddn.com/Chtholly10.jpg'
  >  img_url3 = 'http://qefz7x7v9.bkt.clouddn.com/Violet06.jpg'
  >      
  >  img_url = [img_url1, img_url2, img_url3]
  >  print(img_url)
  >  # 2、调用文件下载的函数，专门下载文件
  >  name = ['1.jpg', '2.jpg', '3.jpg']
  >  image_download(img_url, name)
  > 
  > 
  > if __name__ == '__main__':
  >  main()
  > 
  > ```

* 进阶版：多协程，高并发

  > ```python
  > # 文件下载器(图片)----多任务并发下载器----协程
  > """
  > 主函数 main()
  > 1、定义要下载的图片路径
  > 2、通过协程调用文件下载的函数，专门下载文件
  > 
  > 文件下载函数   image_download(img_url, name):
  > 1、根据url地址请求网络资源
  > 2、在本地创建文件，准备保存
  > 3、读取网络资源数据 （循环）
  > 4、把读取的网络资源写入到本地文件中
  > 5、做异常捕获
  > 
  > """
  > # 导入猴子补丁
  > from gevent import monkey
  > 
  > monkey.patch_all()
  > 
  > # 导⼊urllib模块
  > import urllib.request
  > import gevent
  > 
  > 
  > # 文件下载函数
  > def image_download(img_url, img_name):
  >  # 1、根据url地址请求网络资源
  >  # 使用urllib.request模块请求网络资源
  >  # urllib.request.urlopen(url地址)  返回的是二进制数据
  >  try:
  >      response = urllib.request.urlopen(img_url)
  >      # response : <http.client.HTTPResponse object at 0x000001C8E2D618D0> 是二进制数据
  >      print(response)
  >  # 5、做异常捕获
  >  except Exception as e:
  >      # <urlopen error [Errno 11001] getaddrinfo failed>   --- e
  >      # HTTP Error 404: Not Found --- e
  >      # <class 'urllib.error.HTTPError'> ---type(e)
  >      print('文件%s下载失败！ %s' % (img_name, e))
  >  else:
  >      # 2、在本地创建文件，准备保存
  >      with open('./picture/' + img_name, 'wb') as img_file:
  >          # type(img_file)  ---  <class '_io.BufferedWriter'>
  >          # 3、读取网络资源数据 （循环）
  >          while True:
  >              img_data = response.read(1024)
  >              if img_data:
  >                  # 4、把读取的网络资源写入到本地文件中
  >                  img_file.write(img_data)
  >              else:
  >                  break
  >      print('文件%s下载成功！' % img_name)
  > 
  > 
  > def main():
  >  # 1、定义要下载的图片路径
  >  # 定义空list来存储图片路径及保存的文件名
  >  file_name = []
  >  # 定义空list来存储协程，用于gevent.joinall([协程组成的list])
  >  gevent_list = []
  > 
  >  file_url1 = 'http://qefz7x7v9.bkt.clouddn.com/Chtholly06.jpg'
  >  file_url2 = 'http://qefz7x7v9.bkt.clouddn.com/Chtholly10.jpg'
  >  file_url3 = 'http://qefz7x7v9.bkt.clouddn.com/Violet06.jpg'
  >  file_url4 = "http://image.uczzd.cn/11867042470350090334"
  > 
  >  file_url = [file_url1, file_url2, file_url3, file_url4]
  >  for i in range(len(file_url)):
  >      file_name.append(str(i + 1) + '.gif' )
  > 
  >  # 2、通过协程调用文件下载的函数，专门下载文件
  >  for i in range(len(file_url)):
  >      img_name = file_name[i]
  >      img_url = file_url[i]
  >      gevent_list.append(gevent.spawn(image_download, img_url, img_name))
  > 
  >  # gevent.joinall([协程组成的list])
  >  # 让主线程在所有(all)协程结束后再结束
  >  gevent.joinall(gevent_list)
  > 
  > 
  > # gevent.joinall([
  > #      gevent.spawn(download_img, img_url1, "1.gif"),
  > #      gevent.spawn(download_img, img_url2, "2.gif"),
  > #      gevent.spawn(download_img, img_url3, "3.gif")
  > # ])
  > 
  > if __name__ == '__main__':
  >  main()
  > 
  > ```

#### 4.4 协程实战——多协程版Web服务器

* Web网游服务器：多协程版

* 协程部分：

  > ```python
  > # 创建协程
  > gevent_request_handler = gevent.spawn(self.request_handler, new_client_socket, ip_port)
  > # 设置主线程在协程完成后再结束
  > gevent_request_handler.join()
  > # gevent_request_handler.join()可以省略，
  > # 因为此处主线程一直处在while True循环中，协程一定比主线程先结束
  > ```

* 整体：

  > ```python
  > # 多协程网游服务器
  > # 利用多协程同时浏览多个资源
  > """
  > 1、⽤户使⽤浏览器打开指定地址
  > 2、游戏运⾏⽅启动服务器，选择要发布的游戏
  > 3、客户端刷新浏览器即可畅玩新的游戏
  > ----------------------------------------------------------------------------
  > Setup:
  > 1、在 webserver类的初始化方法__init__中:
  > 1.1 定义一个实例属性projects_dict --- 字典类型，用于设置：游戏项目---路径；
  >  字典格式：key=项⽬名称，value=路径；
  > 1.2 定义一个实例属性current_dir --- 字符串类型，用于保存项⽬的路径，初始设为空 ''；
  > 2、定义初始化项⽬的⽅法 init_projects():
  > 2.1 循环获取字典中的所有key，并转化为list，方便读取， list(dict.keys())；
  > 2.2 通过enumerate()将key_list转化为index和key的元组---(index,game_name)；
  > 2.3 根据index获取游戏的key(名称)，再根据key获取其⽬录；
  > 2.4 将游戏的⽬录保存到实例属性current_dir中；
  > 3、修改Web服务器的要打开的⽬录为实例属性保存的游戏⽬录
  > 4、修改utils()函数中的响应头response_header，设置浏览器优先的解析方式
  > 添加一个 Content-Type:text/html
  > 
  > """
  > # 1、导入模块
  > # 1.1 导入猴子补丁
  > from gevent import monkey
  > monkey.patch_all()
  > 
  > import socket
  > # 1.2 导入gevent---协程
  > import gevent
  > import sys
  > from application import app
  > 
  > 
  > class WebServer(object):
  >  def __init__(self, port):
  >      # 2、创建套接字
  >      tcp_webserver_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  > 
  >      # 3、设置地址重用
  >      tcp_webserver_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
  > 
  >      # 4、绑定端口
  >      self.port = port
  >      addr = ('', self.port)
  >      tcp_webserver_socket.bind(addr)
  > 
  >      # 5、设置监听，让套接字由主动变为被动接收
  >      tcp_webserver_socket.listen(128)
  >      # 定义实例属性，保存套接字
  >      self.tcp_webserver_socket = tcp_webserver_socket
  > 
  >      # 定义实例属性，初始化项目配置----字典dict
  >      self.projects_dict = {}
  >      # self.projects_dict = dict()
  > 
  >      # 定义实例属性，保存要发布的游戏的存储路径current_dir
  >      self.current_dir = ''
  > 
  >      # 2.4 调用初始化游戏项目配置的方法
  >      self.init_projects()
  > 
  >  # 2、给类增加一个初始化项目配置的方法 init_projects()
  >  def init_projects(self):
  >      # 初始化字典的内容---游戏项目配置
  >      # key---游戏名称；value---游戏的路径
  >      self.projects_dict['植物大战僵尸-普通版'] = "game/zwdzjs-v1"
  >      self.projects_dict['植物大战僵尸-破解版'] = "game/zwdzjs-v2"
  >      self.projects_dict['保卫萝卜'] = "game/tafang"
  >      self.projects_dict['2048'] = "game/2048"
  >      self.projects_dict['读心术'] = "game/dxs"
  >      # 2.1  显示所有可以发布的游戏菜单
  >      key_list = list(self.projects_dict.keys())
  >      # enumerate(key_list) 是个元组---（序号，值)
  >      for index, game_name in enumerate(key_list):
  >          print("%d.%s" % (index, game_name))
  > 
  >      # 2.2  接收用户的选择
  >      select_num = int(input("请输入要发布的游戏项目:\n"))
  > 
  >      # 2.3  根据用户的选择发布指定的项目 （保存用户选择的游戏对应的本地目录）
  >      # 根据游戏序号返回游戏名称
  >      game_key = key_list[select_num]
  >      # 根据key返回游戏的存放路径
  >      self.current_dir = self.projects_dict[game_key]
  >      print(self.current_dir)
  > 
  >  def start(self):
  >      # 启动Web服务器
  >      # 6、接受客户端连接 定义函数 request_handler()
  >      # 循环接收客户端的连接
  >      print('服务器已经启动成功！等待客户端连接······')
  >      while True:
  >          new_client_socket, ip_port = self.tcp_webserver_socket.accept()
  >          print('客户端[%s]已连接！' % str(ip_port))
  > 
  >          # 调用函数来进行请求报文的接收，以及响应报文的发送
  >          # self.request_handler(new_client_socket, ip_port)
  > 
  >          # 创建协程
  >          gevent_request_handler = gevent.spawn(self.request_handler, new_client_socket, ip_port)
  >          # 设置主线程在协程完成后再结束
  >          gevent_request_handler.join()
  >          # gevent_request_handler.join()可以省略，
  >          # 因为此处主线程一直处在while True循环中，协程一定比主线程先结束
  > 
  >      # 13、关闭操作
  >      self.tcp_webserver_socket.close()  # 不执行
  > 
  >  def request_handler(self, new_client_socket, ip_port):
  >      # 7、接收客户端浏览器发送的请求协议
  >      request_data = new_client_socket.recv(1024)
  > 
  >      # 8、判断协议是否为空
  >      if not request_data.decode():
  >          print('客户端%s已下线！' % str(ip_port))
  >          new_client_socket.close()
  >          return
  > 
  >      # 修改request_handler()函数中的文件路径---self.current_dir
  >      response_data = app.application(self.current_dir, request_data, ip_port)
  > 
  >      # 12、发送响应报文
  >      new_client_socket.send(response_data)
  > 
  >      # 13、关闭操作
  >      # del ip_port
  >      new_client_socket.close()
  > 
  > 
  > def main():
  >  """
  >  1、导⼊sys模块
  >  2、通过sys.argv获取参数列表
  >  3、判断列表⻓度是否为	2，如果不等于2，要给出提示，Web服务器启动失败
  >  4、取出第⼆个参数，判断是否是⼀个数字，如果不是⼀个数组，则给出提示，Web服务器启动失败
  >  5、接收启动参数，端⼝号
  >  6、修改类构造⽅法，使⽤提供的端⼝号启动Web服务器
  >  :return:
  >  """
  >  # 2、通过sys.argv获取参数列表
  >  # params_list = sys.argv
  >  # print(params_list)
  > 
  >  # 3、判断列表⻓度是否为2，如果不等于2，要给出提示，Web服务器启动失败
  >  if len(sys.argv) != 2:
  >      print("启动失败，参数格式错误!正确格式:python3 xxx.py 端口号")
  >      return
  > 
  >  # 4、取出第⼆个参数，判断是否是⼀个数字，如果不是⼀个数字，则给出提示，Web服务器启动失败
  >  # isdight()会自动把字符串转化为int类型
  >  if not sys.argv[1].isdigit():
  >      print("启动失败，端口号不是一个纯数字!")
  >      return
  > 
  >  # 5、接收启动参数，端⼝号
  >  port = int(sys.argv[1])
  > 
  >  # 6、修改类构造⽅法，使⽤提供的端⼝号启动Web服务器
  >  # 创建class WebServer 的实例对象
  >  ws = WebServer(port)
  >  # 启动web服务器
  >  ws.start()
  > 
  > 
  > if __name__ == '__main__':
  >  main()
  > ```