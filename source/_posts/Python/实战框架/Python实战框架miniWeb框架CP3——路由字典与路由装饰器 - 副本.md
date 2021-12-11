---
title: Python实战框架MiniWeb框架CP3——路由字典与路由装饰器
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
  - route
abbrlink: 7db5d861
date: 2020-09-01 10:18:26
top:
---

---

* Python实战框架MiniWeb框架CP3——路由字典与路由装饰器
* CP3主要分享了
* 路由字典：路径与函数的映射
  * `urls.py`
* 路由装饰器：自动添加路径与函数的映射
  * `templates.py`

<!-- more -->

# Python实战框架MiniWeb框架CP3——路由字典与路由装饰器

### 4. 路由字典与路由装饰器

* 时间：2020.09.02

#### 4.1 路由器：路由字典  Django

* 路由概念

  > 路由就是指请求路径与功能函数的映射，是Web框架应用程序中非常重要的⼀个概念，路由根据用户的请求，选择对应的函数来处理相应的请求，一般用于获取不同的模板文件，并加载数据库的数据。

* 框架结构

  * `miniWebServer.py`
  * `static`

  * `application`
    * `app.py`
    * `utils.py`
    * `urls.py`
    * `templates.py`
  * `tempaltes`

* 新增：路由字典模块：`application\urls.py`

  > 定义路由字典，通过字典存储可以访问的页面及其对应的函数。
  >
  > ```python
  > from application import templates
  > 
  > # 定义路由字典(手动)
  > route_dict = {
  >  '/index.py': templates.index,
  >  '/center.py': templates.center,
  >  '/update.py': templates.update
  > }
  > ```

* 新增：页面函数模块：`application\templates.py`

  > 定义页面函数，函数功能：读取模板文件、与数据库交互等。
  >
  > ```python
  > import re
  > import time
  > 
  > 
  > def index(template_path):
  >  """返回index.py需要的页面内容"""
  >  try:
  >      with open(template_path, 'rb') as file:
  >          print("模板资源:%s请求成功！" % template_path)
  >          response_body = file.read()
  >      # 模板替换操作，与数据库交互
  >      mysql_data = time.ctime()
  >      response_body = re.sub(r"\{%.+%\}", mysql_data, response_body.decode()).encode()
  >      # 模板存在时要添加到响应报文的响应头中的内容
  >      response_headers = [("Data", time.ctime())]
  >  except Exception as e:
  >      print("模板资源路径:%s无法访问! Error:%s" % (template_path, e))
  >      response_headers = []
  >      response_body = "Error!! %s" % str(e)
  >      response_body = response_body.encode()
  >  return response_headers, response_body
  > 
  > 
  > def center(template_path):
  >  """返回center.py需要的页面内容"""
  >  try:
  >      with open(template_path, 'rb') as file:
  >          print("模板资源:%s请求成功！" % template_path)
  >          response_body = file.read()
  >      # 模板替换操作，与数据库交互
  >      mysql_data = time.ctime()
  >      response_body = re.sub(r"\{%.+%\}", mysql_data, response_body.decode()).encode()
  >      # 模板存在时要添加到响应报文的响应头中的内容
  >      response_headers = [("Data", time.ctime())]
  >  except Exception as e:
  >      print("模板资源路径:%s无法访问! Error:%s" % (template_path, e))
  >      response_headers = []
  >      response_body = "Error!! %s" % str(e)
  >      response_body = response_body.encode()
  >  return response_headers, response_body
  > 
  > 
  > def update(template_path):
  >  """返回update.py需要的页面内容"""
  >  try:
  >      with open(template_path, 'rb') as file:
  >          print("模板资源:%s请求成功！" % template_path)
  >          response_body = file.read()
  >      # 模板替换操作，与数据库交互
  >      mysql_data = time.ctime()
  >      response_body = re.sub(r"\{%.+%\}", mysql_data, response_body.decode()).encode()
  >      response_headers = [("Data", time.ctime())]
  >  except Exception as e:
  >      print("模板资源路径:%s无法访问! Error:%s" % (template_path, e))
  >      response_headers = []
  >      response_body = "Error!! %s" % str(e)
  >      response_body = response_body.encode()
  >  return response_headers, response_body
  > 
  > 
  > def nor_found(template_path):
  >  """返回路径错误时的页面内容"""
  >  try:
  >      with open(template_path, 'rb') as file:
  >          pass
  >  except Exception as e:
  >      print("模板资源路径:%s不存在! Error:%s" % (template_path, e))
  >      response_body = "Error!! %s" % str(e)
  >      response_body = response_body.encode()
  >      response_headers = []
  >  return response_headers, response_body
  > ```

* 修改：`application/app.py`中的application()函数

  > `app_1_route.py`
  >
  > 将原本的通过列表和match_template()函数实现的模板请求路径是否存在的判断，修改为通过`urls.py`中的路由字典进行判断，若存在，通过`env`字典将对应的函数引用传递给`utils.py`中，在调用对应函数实现功能。
  >
  > ```python
  > def application(file_path, static_root, ip_port):
  >  # 二、动态资源获取，资源路径以.py结尾。
  >  else:
  >      template_root = "./templates"
  >      template_path = template_root + file_path.replace('.py', '.html')
  >      # template_path = match_template(template_root, file_path)
  > 
  >      # 判断请求的模板文件是否存在
  >      # 存在就保存对应的函数名，之后传递给utils模块中的get_response()函数中，再执行模板文件对应的函数
  >      if file_path in urls.route_dict:
  >          template_func = urls.route_dict[file_path]
  >          status = '200 OK'
  >      # 不存在就保存默认的404页面显示函数，之后再执行
  >      else:
  >          template_func = templates.nor_found
  >          status = '404 Not Found'
  >      # if template_path:
  >      #     status = '200 OK'
  >      # else:
  >      #     status = '404 Not Found'
  >      #     template_path = template_root + file_path.replace('.py', '.html')
  >      print('客户端:[%s]正在请求路径:%s的资源!' % (str(ip_port), template_path))
  > 
  >      # 定义请求信息字典，初始为空
  >      # env = dict()
  >      env = {'status': status, 'PATH_INFO': template_path, 'FUNC': template_func}
  > 
  >      # 调用utils模块中的WSGI接口函数，get_response()
  >      # 生成并拼接响应报文，返回给Web服务器
  >      headers, response_body = utils_1_route.get_response(env, set_response_headers)
  > ```

* 修改：`application/utils.py`中的get_response()函数

  > `utlis_1_route.py`
  >
  > 将之前在该函数中执行的模板文件读取，数据库交互部分分离出去，放入`templates.py`中实现，同时可以对不同的模板实现不同的数据库交互功能。
  >
  > ```python
  > def get_response(environ, start_response):
  >  status = environ['status']
  >  template_path = environ['PATH_INFO']
  >  template_func = environ['FUNC']
  > 
  >  response_headers, response_body = template_func(template_path)
  >  headers = start_response(status, response_headers)
  >  return headers, response_body
  > ```

#### 4.2 路由装饰器  Flask

* 路由装饰器概念

  > 使用装饰器工厂函数接收路径参数，进而实现装饰器路由，即利用装饰器装饰页面的函数。

* 作用

  > 可以通过装饰器自动添加路径参数。

* 文件结构

  > 只需要修改Django框架中的`urls.py`和`templates.py`

* `urls.py`

  > `urls_route_deco.py`
  >
  > 字典定义为空字典，同时也不用导入`templates.py`模块；
  >
  > 可以在此处定义路由装饰器(装饰器工厂)，也可以定义在`templates.py`模块中；
  >
  > ```python
  > # 定义路由字典(空字典)
  > route_dict = {}
  > ```

* `templates.py`

  > `templates_route_deco.py`
  >
  > 定义路由装饰器，传递路径参数，自动导入到路由字典中；
  >
  > 需要在`templates.py`中导入`urls.py`模块；
  >
  > 在该模块中直接对函数进行装饰`@route("/index.py")`，在`app.py`模块导入该模块时，装饰执行，路径被导入路由字典。
  >
  > ```python
  > from application import urls_route_deco
  > 
  > 
  > # 装饰器工厂：传递路径参数
  > def route(template_name):
  >  def decorator(template_func):
  >      # 添加路径到route_dict字典中
  >      urls_route_deco.route_dict[template_name] = template_func
  > 
  >      def wrapped(template_path):
  >          return template_func(template_path)
  > 
  >      return wrapped
  > 
  >  return decorator
  > 
  > 
  > # 首页
  > @route("/index.py")
  > def index(template_path):
  >  pass
  > ```

---