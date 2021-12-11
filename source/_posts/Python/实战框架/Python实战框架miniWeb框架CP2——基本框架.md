---
title: Python实战框架MiniWeb框架CP2——基本框架
toc: true
reward: true
declare: true
gitalkenable: true
categories:
  - Python
  - Frame
  - MiniWeb
tags:
  - Python
  - MiniWeb
  - listdir
abbrlink: 9dd4a6e5
date: 2020-08-31 21:30:18
top:
---

---

* Python实战框架MiniWeb框架CP2——基本框架
* CP2主要分享了
* miniWeb框架的Web服务器(OOP+多进程)
  * `miniWebServer.py`
* miniWeb框架的Web框架应用程序(接口)
  * `app.py`
  * `utils.py`
* 模板文件的加载与数据库内容替换

<!-- more -->

# Python实战框架MiniWeb框架CP2——基本框架

### 2. miniWeb框架基本实现

* 时间：2020.08.31

#### 2.1 Web服务器

* `miniWebServer.py`

  > 客户端与服务器的通信的建立、请求报文的接收与解析；
  >
  > 通过命令行启动，输入web框架的模块名和入口函数。
  >
  > ```python
  > # 4.2 取出第三个参数，进行正则匹配，得到Web框架的模块名和函数名，失败给出提示
  > web_frame = sys.argv[2]
  > result = re.match(r"([^:]*):(.*)", web_frame)
  > web_frame_module_name = result.group(1)
  > web_frame_app_name = result.group(2)
  > # 导入web框架的主模块
  > web_frame_module = __import__(web_frame_module_name)
  > # 获取那个可以直接调用的函数(对象)
  > app_name = getattr(web_frame_module, web_frame_app_name)
  > ```
  >
  > ```python
  > # miniWeb框架  命令行终端启动
  > """
  > 在终端中启动Web服务器
  > 在main()里实现。
  > 
  > """
  > # 1、导入模块
  > import re
  > import sys
  > import socket
  > import multiprocessing
  > 
  > class WebServer(object):
  >  def __init__(self, port, document_root, app_name):
  >      # 设定资源文件的路径
  >      self.document_root = document_root
  >      # 设定web框架可以调用的函数(对象)
  >      self.app_name = app_name
  > 
  >      # 2、创建套接字
  >      tcp_web_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  > 
  >      # 3、设置地址重用
  >      tcp_web_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
  > 
  >      # 4、绑定端口
  >      self.port = port
  >      address = ('', self.port)
  >      tcp_web_server_socket.bind(address)
  > 
  >      # 5、设置监听，让套接字由主动变为被动接收
  >      tcp_web_server_socket.listen(128)
  >      # 定义实例属性，保存套接字
  >      self.tcp_web_server_socket = tcp_web_server_socket
  > 
  >  def start(self):
  >      # 启动Web服务器
  >      # 6、接受客户端连接 定义函数 request_handler()
  >      # 循环接收客户端的连接
  >      print('服务器已经启动成功！等待客户端连接······')
  >      while True:
  >          new_client_socket, ip_port = self.tcp_web_server_socket.accept()
  >          # 设置socket操作的超时时长，默认为无限
  >          # new_client_socket.settimeout(5)
  > 
  >          # 多进程执行请求报文的接接收与响应报文的生成
  >          process_request_handler = multiprocessing.Process(target=self.request_handler,
  >                                                            args=(new_client_socket, ip_port))
  >          process_request_handler.start()
  >          # 子进程拷贝了一份资源，所以需要多close()一次socket
  >          new_client_socket.close()
  > 
  >          # 调用函数来进行请求报文的接收，以及响应报文的生成
  >          # self.request_handler(new_client_socket, ip_port)
  > 
  >      # 13、关闭操作
  >      # self.tcp_web_server_socket.close()  # 不执行
  > 
  >  def request_handler(self, new_client_socket, ip_port):
  >      # 7、接收客户端浏览器发送的请求协议
  >      try:
  >          request_text = new_client_socket.recv(1024).decode("utf-8")
  >      except Exception as ret:
  >          print("请求报文接收或解码错误!  Error:", ret)
  >          new_client_socket.close()
  >          return
  > 
  >      # request_data = new_client_socket.recv(1024)
  > 
  >      # 8、判断协议是否为空
  >      if not request_text:
  >          print('客户端%s已下线，浏览器已关闭！' % str(ip_port))
  >          new_client_socket.close()
  >          return
  > 
  >      resource_path = self.parse_request(request_text)
  >      response_data = self.app_name(resource_path, ip_port)
  > 
  >      # 12、发送响应报文
  >      new_client_socket.send(response_data)
  > 
  >      # 13、关闭操作
  >      new_client_socket.close()
  > 
  >  def parse_request(self, request_text):
  >      """
  >      解析请求报文，返回资源路径
  >      :param request_text : 请求报文(decode)
  >      :return: file_path : 资源路径
  > 
  >      """
  >      # 方法二：正则表达式方式解析请求报文
  >      request_lines = request_text.splitlines()
  >      # 打印请求报文
  >      for i, line in enumerate(request_lines):
  >          print(i, line)
  >      # 获取资源路径
  >      try:
  >          result = re.match(r"([^/]*)([^ ]+)", request_lines[0])
  >      except Exception as ret:
  >          print("请求报文的资源路径解析错误!  Error:", ret)
  >          return 'Path Not Parse'
  >      else:
  >          if result:
  >              print("请求方式:", result.group(1))
  >              # print("请求资源路径:", result.group(2))
  >              file_path = result.group(2)
  >          else:
  >              file_path = ''
  >              # 9.2.3 不加路径时的默认路径---默认显示index.html页面
  >          if file_path == "/":
  >              file_path = "/index.html"
  > 
  >          resource_path = self.document_root + file_path
  >      return resource_path
  >      # -----------------------------------------------------------------
  > 
  > 
  > def main():
  >  # 1. 常量参数定义
  >  static_document_root = './static'
  >  dynamic_document_root = './application'
  > 
  >  # 2、通过sys.argv获取参数列表
  >  # params_list = sys.argv
  >  # print(params_list)
  > 
  >  # 3、判断列表⻓度是否为3，如果不等于3，要给出提示，Web服务器启动失败
  >  if len(sys.argv) != 3:
  >      print("启动失败，参数格式错误!正确格式:python xxx.py 端口号 web框架应用程序模块名:入口函数名")
  >      return
  > 
  >  # 4.1 取出第⼆个参数，判断是否是⼀个数字，如果不是⼀个数字，则给出提示，Web服务器启动失败
  >  # isdight()会自动把字符串转化为int类型
  >  if not sys.argv[1].isdigit():
  >      print("启动失败，端口号不是一个纯数字!")
  >      return
  >  port = int(sys.argv[1])
  > 
  >  # 4.2 取出第三个参数，进行正则匹配，得到Web框架的模块名和函数名，失败给出提示
  >  web_frame = sys.argv[2]
  >  try:
  >      result = re.match(r"([^:]*):(.*)", web_frame)
  >  except Exception as ret:
  >      print("启动失败！正确格式:python xxx.py 端口号 框架模块名:入口函数名")
  >      return
  >  else:
  >      if result:
  >          web_frame_module_name = result.group(1)
  >          web_frame_app_name = result.group(2)
  >      else:
  >          print('Error')
  >          return
  > 
  >  # 4.3 临时将动态资源相关的web框架应用程序所在的路径添加到sys.path中
  >  sys.path.append(dynamic_document_root)
  > 
  >  # 5、导入框架模块__import__()
  >  # 导入web框架的主模块
  >  web_frame_module = __import__(web_frame_module_name)
  >  # 获取那个可以直接调用的函数(对象)
  >  app_name = getattr(web_frame_module, web_frame_app_name)
  > 
  >  # 6、修改类构造⽅法，使⽤提供的端⼝号启动Web服务器
  >  # 创建class WebServer 的实例对象
  >  ws = WebServer(port, static_document_root, app_name)
  >  # 启动web服务器
  >  ws.start()
  > 
  > 
  > if __name__ == '__main__':
  >  main()
  > ```

#### 2.2 Web框架应用程序

* `application\app.py`

  > 判断静态、动态资源，生成响应报文；
  >
  > 通过资源路径的结尾判断：`.py`---动态，`.html`---静态；
  >
  > `environ`字典，传递状态值、模板路径等。
  >
  > ```python
  > from application import utils
  > import time
  > 
  > def set_response_headers(status, headers):
  >  """这个方法，会在 web框架中被默认调用"""
  >  response_header_default = [("Server", "MiniWeb Server WSGI/2.1"), ]
  >  # 将状态码/相应头信息存储起来
  >  # [字符串, [xxxxx, xxx2]]
  > 
  >  headers = [status, response_header_default + headers]
  >  return headers
  > 
  > 
  > def application(resource_path, ip_port):
  >  """
  >  生成响应报文，返回响应报文
  >  :param resource_path: 文件路径
  >  :param ip_port: 客户端地址
  >  :return: response_data : 响应报文
  > 
  >  """
  >  # 一、静态资源获取，资源路径非.py结尾。
  >  if not resource_path.endswith('.py'):
  >      print('客户端:[%s]正在请求路径:%s的资源!' % (str(ip_port), resource_path))
  > 
  >      # 10.5 异常捕获，如果没有该路径文件，显示404网页
  >      try:
  >          with open(resource_path, 'rb') as file:
  >              # 响应报文：静态资源 200
  >              status = '200 OK'
  >              response_body = file.read()
  >              response_data = utils.create_response(status, response_body)
  >      except Exception as e:
  >          # 重新设置响应报文 404
  >          print("资源路径:%s不存在! Error:%s" % (resource_path, e))
  >          status = '404 Not Found'
  >          response_body = "Error!! %s" % str(e)
  >          response_body = response_body.encode()
  >          response_data = utils.create_response(status, response_body)
  > 
  >  # 二、动态资源获取，资源路径以.py结尾。
  >  else:
  >      # 请求信息字典，初始为空
  >      # env = dict()
  >      env = {'status': '200 OK', 'PATH_INFO': './index.html'}
  >      # 响应报文：动态资源 200
  >      headers, response_body = utils.get_response(env, set_response_headers)
  >      response_line = "HTTP/1.1 {status}\r\n".format(status=headers[0])
  >      response_header = "Content-Type: text/html; charset=utf-8\r\n"
  >      for temp_head in headers[1]:
  >          response_header += "{0}:{1}\r\n".format(*temp_head)
  >      response_header += "Content-Length: %d\r\n" % len(response_body)
  >      response_blank = "\r\n"
  >      response_data = (response_line + response_header + response_blank + response_body).encode()
  > 
  >      # status = '200 OK'
  >      # # response_body 应该与数据库、模板文件交互
  >      # response_body = ('Dynamic Web Page ! at %s' % time.ctime()).encode()
  >      # response_data = utils.create_response(status, response_body)
  > 
  >  return response_data
  > ```

* `application\utils.py`

  > 拼接响应报文。
  >
  > ```python
  > import time
  > 
  > def create_response(status, response_body):
  >  # 10、根据请求路径生成响应报文
  >  # 10.1 响应行
  >  response_line = 'HTTP/1.1 %s\r\n' % status
  >  # 10.2 响应头
  >  response_header = "Server:MiniWeb Server WSGI/2.1\r\n"
  >  # 响应头加一个"Content-Type:text/html\r\n"，使得浏览器可以正确响应数据
  >  # response_header += "Content-Type: text/html; charset=utf-8\r\n"
  >  response_header += "charset=utf-8\r\n"
  >  response_header += "Content-Length: %d\r\n" % (len(response_body))
  > 
  >  # 10.3 响应空行
  >  response_blank = "\r\n"
  > 
  >  # 10.4 响应主体---打开并读取资源路径对应的html文件
  >  # response_body = "Hello World!".encode()
  >  response_body = response_body
  > 
  >  # 11、拼接响应报文
  >  response_data = (response_line + response_header + response_blank).encode() + response_body
  >  return response_data
  > 
  > # application(environ, start_response)
  > def get_response(environ, start_response):
  >  status = environ['status']
  >  # 要添加到响应报文的响应头中的内容
  >  response_headers = [("Data", time.ctime())]
  >  headers = start_response(status, response_headers)
  >  response_body = 'WSGI Dynamic Web Page ! at %s' % time.ctime()
  >  return headers, response_body
  > ```

---

### 3. 加载与替换模板文件

* 时间：2020.09.01

#### 3.1 加载模板页面

> 使用模板文件只需要对Web框架应用程序进行一点修改，服务器不需要改动。

* `application\app.py`

  * 增加函数：利用os模块的listdir函数获取文件名

    > `get_template(path, url_list, file_list)`，用于获取项目模板文件路径下的模板文件名(html)；
    >
    > ```python
    > def get_template(path, url_list, file_list):  # 传入存储的list
    >  for file in os.listdir(path):
    >      file_path = os.path.join(path, file)
    >      if os.path.isdir(file_path):
    >          get_template(file_path, url_list, file_list)
    >      else:
    >          url_list.append(file_path)
    >          file_list.append(file)
    >  return file_list
    > ```

  * 增加函数：

    > `match_template(template_root, file_path)`，用于判断请求的动态资源路径与模板文件名是否匹配；
    >
    > ```python
    > def match_template(template_root, file_path):
    >  # 初始化模板文件所在路径
    >  template_root = template_root
    >  # url_list = []
    >  file_list = []
    >  # 模板文件名列表
    >  template_list = get_template(template_root, [], file_list)
    >  file_name = file_path.replace('.py', '.html')
    >  # file_name = resource_path.split('/')[-1].replace('py', 'html')
    >  if file_name in list(map(lambda x: '/' + x, file_list)):
    >      return template_root + file_name
    >  else:
    >      # print("请求的模板文件:%s不存在!" % file_name)
    >      return
    > ```

  * 修改`application(file_path, static_root, ip_port):`函数动态资源响应部分`

    > `env = {'status': status, 'PATH_INFO': template_path}`，通过`env`字典传递请求信息。
    >
    > ```python
    > def application(file_path, static_root, ip_port):
    >  # 一、静态资源获取，资源路径非.py结尾。
    >  if not file_path.endswith('.py'):
    >      resource_path = static_root + file_path
    >      print('客户端:[%s]正在请求路径:%s的资源!' % (str(ip_port), resource_path))
    > 
    >      # 10.5 异常捕获，如果没有该路径文件，显示404网页
    >      try:
    >          with open(resource_path, 'rb') as file:
    >              # 响应报文：静态资源 200
    >              print("静态资源:%s请求成功!" % resource_path)
    >              status = '200 OK'
    >              response_body = file.read()
    >              response_data = utils.create_response(status, response_body)
    >      except Exception as e:
    >          # 重新设置响应报文 404
    >          print("静态资源路径:%s不存在! Error:%s" % (resource_path, e))
    >          status = '404 Not Found'
    >          response_body = "Error!! %s" % str(e)
    >          response_body = response_body.encode()
    >          response_data = utils.create_response(status, response_body)
    > 
    >  # 二、动态资源获取，资源路径以.py结尾。
    >  else:
    >      template_root = "./templates"
    >      template_path = match_template(template_root, file_path)
    > 
    >      if template_path:
    >          status = '200 OK'
    >      else:
    >          status = '404 Not Found'
    >          template_path = template_root + file_path.replace('.py', '.html')
    >      print('客户端:[%s]正在请求路径:%s的资源!' % (str(ip_port), template_path))
    > 
    >      # 请求信息字典，初始为空
    >      # env = dict()
    >      env = {'status': status, 'PATH_INFO': template_path}
    >      # 响应报文：动态资源 200
    >      headers, response_body = utils.get_response(env, set_response_headers)
    >      response_line = "HTTP/1.1 {status}\r\n".format(status=headers[0])
    >      response_header = "Content-Type: text/html; charset=utf-8\r\n"
    >      for temp_head in headers[1]:
    >          response_header += "{0}:{1}\r\n".format(*temp_head)
    >      response_header += "Content-Length: %d\r\n" % len(response_body)
    >      response_blank = "\r\n"
    >      response_data = (response_line + response_header + response_blank).encode() + response_body
    >  return response_data
    > 
    > ```

* `application\utils.py`

  > 修改`get_response(environ, start_response)`函数，针对不同的模板文件、不存在的模板文件返回不同的响应信息。
  >
  > ```python
  > def get_response(environ, start_response):
  >  status = environ['status']
  >  template_path = environ['PATH_INFO']
  >  try:
  >      with open(template_path, 'rb') as file:
  >          print("模板资源:%s请求成功！" % template_path)
  >          response_body = file.read()
  >      # 模板存在时要添加到响应报文的响应头中的内容
  >      response_headers = [("Data", time.ctime())]
  >      headers = start_response(status, response_headers)
  >      # response_body = 'WSGI Dynamic Web Page ! at %s' % time.ctime()
  >  except Exception as e:
  >      print("资源路径:%s不存在! Error:%s" % (template_path, e))
  >      # 模板不存在时要添加到响应报文的响应头中的内容
  >      response_headers = []
  >      headers = start_response(status, response_headers)
  >      status = '404 Not Found'
  >      response_body = "Error!! %s" % str(e)
  >      response_body = response_body.encode()
  >  return headers, response_body
  > ```

#### 3.2 替换模板文件

* 将模板文件中的部分数据(body)修改为数据库中的数据；

* 即通过正则匹配修改从模板文件中读取的数据(html)；

* 只需修改web框架中的`utils.py`。

  > ```python
  >      with open(template_path, 'rb') as file:
  >          print("模板资源:%s请求成功！" % template_path)
  >          response_body = file.read()
  > 
  >      # 替换模板文件
  >      mysql_data = "与数据库交互:~~~~(>_<)~~~~"
  >      response_body = re.sub(r"\{%content%\}", mysql_data, response_body.decode()).encode()
  > ```

* 注意

  * 打开文件要通过`rb`二进制方式打开，避免乱码；
  * 或者使用`with open(path, 'r', encoding='utf-8') as file`方式，指定编码格式；
  * 通过`re.sub(pattern, newstr, str)`进行正则替换，如果是`rb`读取的二进制文件，需要decode()；
  * 在``create_response()`函数中，生成的响应报文的响应头中不能有` response_header += "Content-Type: text/html\r\n"`，会导致页面格式显示不全。

---

### 