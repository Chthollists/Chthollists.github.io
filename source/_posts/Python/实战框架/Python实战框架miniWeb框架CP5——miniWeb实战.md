---
title: Python实战框架MiniWeb框架CP5——miniWeb实战
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
  - logging
abbrlink: 17c86d33
date: 2020-09-03 15:22:33
top:
---

---

* Python实战框架MiniWeb框架CP5——miniWeb实战
* CP5主要分享了
* miniWeb实战：选股系统
* 框架：`app.py`、`utils.py`、`urls.py`、`templates.py`
* 系统功能
  * 首页加载：查询
  * 个人中心页加载：查询
  * 首页将股票添加收藏：增
  * 个人中心页删除收藏：删
  * 更新页修改备注：改
* 路由装饰器正则匹配路径：多对一映射
* 数据接口函数：只从数据库获取数据，不渲染，前后端分离
* log日志：监控+调试

<!-- more -->

# Python实战框架MiniWeb框架CP5——miniWeb实战

### 6. miniWeb实战：选股系统

* 时间：2020.09.04

#### 6.1 数据库导入

* 创建数据库

  > ```sql
  > create database stock_db charset = utf8
  > ```

* 导入sql文件

  > ```bash
  > $ mysql -u root -p stock_db < F:\CodeTools\MySQL\sql\stock_db.sql
  > ```

#### 6.2 基础页面显示：首页、个人中心

##### 6.2.1 服务器与框架结构

* Web服务器：`miniWeb.py`

  > 系统sys输入、建立TCPsocket、请求报文的解析、多进程执行服务器与客户端、框架的数据通信。

* Web框架

  * application：app.py、utils.py、urls.py、templates.py
  * static：css、js、png、jpg
  * templates：index.html、center.html、update.html

##### 6.2.2 首页数据加载功能

* 修改模板页面加载的模块：与数据库交互，select查询

* `templates_mysql.py ` 中的`index`函数

  > ```python
  > # 首页
  > @route("/index.py")
  > def index(template_path):
  >  """
  >  返回index.py需要的页面内容
  >  首页信息加载
  >  """
  >  try:
  >      # 1. 加载模板
  >      with open(template_path, 'rb') as file:
  >          print("模板资源:%s请求成功！" % template_path)
  >          response_body = file.read()
  > 
  >      # 模板替换操作，与数据库交互
  >      # 2. 连接数据库获取数据
  >      conn = pymysql.connect(host='localhost', user='root', password='******', database='stock_db')
  >      cur = conn.cursor()
  >      sql = "select * from info;"
  >      cur.execute(sql)
  >      # conn.commit()
  >      result_list = cur.fetchall()
  >      # mysql_data = str(result_list)
  >      cur.close()
  >      conn.close()
  > 
  >      # 3. 数据显示优化：html标签
  >      html_template = """
  >          <tr>
  >              <td>%s</td>
  >              <td>%s</td>
  >              <td>%s</td>
  >              <td>%s</td>
  >              <td>%s</td>
  >              <td>%s</td>
  >              <td>%s</td>
  >              <td>%s</td>
  >              <td>
  >                  <input type="button" value="添加收藏" id="toAdd" name="toAdd" systemidvaule="%s">
  >              </td>
  >          </tr>
  >          """
  >      mysql_data = ""
  >      for info in result_list:
  >          # mysql_data += html_template % (info[0], info[1], info[2], info[3], info[4], info[5], info[6], info[7], 
  >          # info[1]) 
  >          info = list(info)
  >          info.append(info[1])
  >          mysql_data += html_template % tuple(info)
  >          
  >      # 4. 数据替换
  >      response_body = re.sub(r"\{%.+%\}", mysql_data, response_body.decode()).encode()
  >      # 模板存在时要添加到响应报文的响应头中的内容
  >      response_headers = [("Data", time.ctime())]
  >  except Exception as e:
  >      print("模板资源路径:%s无法访问! Error:%s" % (template_path, e))
  >      response_headers = []
  >      response_body = "Error!! %s" % str(e)
  >      response_body = response_body.encode()
  >  return response_headers, response_body
  > ```

##### 6.2.3 个人主页数据加载功能

* 主页显示收藏的股票，通过info和focus两个表内连接inner join来实现

* `templates_mysql.py ` 中的`center`函数

  > ```python
  > # 个人中心页
  > @route("/center.py")
  > def center(template_path):
  >  """
  >  返回center.py需要的页面内容
  >  加载个人主页，主页显示收藏的股票，通过info和focus两个表内连接来实现
  >  """
  >  # 1. 加载模板
  >  try:
  >      with open(template_path, 'rb') as file:
  >          print("模板资源:%s请求成功！" % template_path)
  >          response_body = file.read()
  > 
  >      # 模板替换操作，与数据库交互
  >      # 2. 连接数据库获取数据
  >      conn = pymysql.connect(host='localhost', user='root', password='******', database='stock_db')
  >      cur = conn.cursor()
  >      sql = "select i.code, i.short, i.chg, i.turnover, i.price, i.highs, f.note_info from info as i inner join " \
  >            "focus as f where i.id = f.id; "
  >      cur.execute(sql)
  >      # conn.commit()
  >      result_list = cur.fetchall()
  >      # mysql_data = str(result_list)
  >      cur.close()
  >      conn.close()
  > 
  >      html_template = """
  >          <tr>
  >              <td>%s</td>
  >              <td>%s</td>
  >              <td>%s</td>
  >              <td>%s</td>
  >              <td>%s</td>
  >              <td>%s</td>
  >              <td>%s</td>
  >              <td>
  >                  <a type="button" class="btn btn-default btn-xs" href="/update/%s.html"> <span class="glyphicon glyphicon-star" aria-hidden="true"></span> 修改 </a>
  >              </td>
  >              <td>
  >                  <input type="button" value="删除" id="toDel" name="toDel" systemidvaule="%s">
  >              </td>
  >          </tr>
  >          """
  >      mysql_data = ""
  >      for info in result_list:
  >          # mysql_data += html_template % (info[0], info[1], info[2], info[3], info[4], info[5], info[6], info[7],
  >          # info[0])
  >          info = list(info)
  >          info.extend((info[0], info[0]))
  >          mysql_data += html_template % tuple(info)
  > 
  >      response_body = re.sub(r"\{%.+%\}", mysql_data, response_body.decode()).encode()
  >      # 模板存在时要添加到响应报文的响应头中的内容
  >      response_headers = [("Data", time.ctime())]
  >  except Exception as e:
  >      print("模板资源路径:%s无法访问! Error:%s" % (template_path, e))
  >      response_headers = []
  >      response_body = "Error!! %s" % str(e)
  >      response_body = response_body.encode()
  >  return response_headers, response_body
  > ```

---

#### 6.3 选股系统高阶操作

##### 6.3.1 路由装饰器正则形式

* 作用

  > 当路径与函数的映射关系为多对一时，为了简化函数的个数，可以使用正则匹配来向路由器中动态添加可变的模板文件路径，：`re.match(url, path)`。

* 前提：多个路径对应的页面的功能一致，可由一个函数完成

* 代码修改

  * Web框架：`app.py`中的application()函数

    > `app_3_mysql.py`，通过正则匹配来确定请求路径的有无，并将路由器中被匹配到的路径添加到字典env中，传入utils模块
    >
    > ```python
    >  # 动态资源请求
    >  else:
    >      template_root = "./templates"
    >      # template_path = template_root + file_path.replace('.py', '.html')
    >      template_path = template_root + file_path
    >      # template_path = match_template(template_root, file_path)
    > 
    >      #  方法三：多对一，路由装饰器方法，通过正则匹配在路由字典中查找是否有满足格式的请求路径，适用于路径和函数多对一。
    >      for url, func in urls_route_deco.route_dict.items():
    >          if re.match(url, file_path):
    >              route_url = url
    >              template_func = func
    >              status = '200 OK'
    >              break
    >      else:
    >          route_url = None
    >          template_func = templates_mysql.not_found
    >          status = '404 Not Found'
    > 
    >      print('客户端:[%s]正在请求路径:%s的资源!' % (str(ip_port), template_path))
    > 
    >      # 请求信息字典，初始为空
    >      # env = dict()
    >      env = {
    >          'status': status,
    >          'PATH_INFO': template_path,
    >          'FUNC': template_func,
    >          'FILE_NAME': file_path,
    >          'URL': route_url
    >      }
    >      # tmp, response_body = template_func(template_path)
    >      headers, response_body = utils_1_route.get_response(env, set_response_headers)
    > ```

  * `utils.py`中的get_response()函数

    > `utils_1_route_deco.py`，调用templates模块时，多传入一个url参数，表示匹配到的模板路径。
    >
    > ```python
    > def get_response(environ, start_response):
    >  status = environ['status']
    >  template_path = environ['PATH_INFO']
    >  template_func = environ['FUNC']
    >  file_name = environ['FILE_NAME']
    >  route_url = environ['URL']
    > 
    >  # 获取响应主体
    >  response_headers, response_body = template_func(file_name, route_url)
    >  # response_data = create_response(status, response_body)
    >  # 设置响应行、响应头
    >  headers = start_response(status, response_headers)
    >  
    >  return headers, response_body
    > ```

  * `templates.py`

    > `templates_mysql.py`
    >
    > ```python
    > import re
    > import time
    > import pymysql
    > from application import urls_route_deco
    > 
    > template_root = "./templates"
    > 
    > 
    > # 装饰器工厂：传递路径参数
    > def route(template_name):
    >  def decorator(template_func):
    >      # 添加路径到route_dict字典中
    >      urls_route_deco.route_dict[template_name] = template_func
    > 
    >      def wrapped(*args, **kwargs):
    >          return template_func((*args, **kwargs)
    > 
    >      return wrapped
    > 
    >  return decorator
    > 
    > # 首页
    > @route("/index.html")
    > def index(file_name, route_url=None):
    >  """
    >  返回index.html需要的页面内容
    >  首页信息加载
    >  """
    >  template_path = template_root + file_name
    >  try:
    >      # 1. 加载模板
    >      with open(template_path, 'rb') as file:
    >          print("模板资源:%s请求成功！" % template_path)
    >          response_body = file.read()
    > 
    >      # 模板替换操作，与数据库交互
    >      # 2. 连接数据库获取数据
    >      conn = pymysql.connect(host='localhost', user='root', password='******', database='stock_db')
    >      cur = conn.cursor()
    >      sql = "select * from info;"
    >      cur.execute(sql)
    >      # conn.commit()
    >      result_list = cur.fetchall()
    >      # mysql_data = str(result_list)
    >      cur.close()
    >      conn.close()
    > 
    >      # 3. 数据显示优化：html标签
    >      html_template = """
    >          <tr>
    >              <td>%d</td>
    >              <td>%s</td>
    >              <td>%s</td>
    >              <td>%s</td>
    >              <td>%s</td>
    >              <td>%s</td>
    >              <td>%s</td>
    >              <td>%s</td>
    >              <td>
    >                  <input type="button" value="添加收藏" id="toAdd" name="toAdd" systemidvaule="%s">
    >              </td>
    >          </tr>
    >          """
    >      mysql_data = ""
    >      for info in result_list:
    >          # mysql_data += html_template % (info[0], info[1], info[2], info[3], info[4], info[5], info[6], info[7],
    >          # info[0])
    >          info = list(info)
    >          info.append(info[1])
    >          mysql_data += html_template % tuple(info)
    > 
    >      # 4. 数据替换
    >      # mysql_data = time.ctime()
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
    > # 个人中心页
    > @route("/center.html")
    > def center(file_name, route_url=None):
    >  """
    >  返回center.html需要的页面内容
    >  加载个人主页，主页显示收藏的股票，通过info和focus两个表内连接来实现
    >  """
    >  # 1. 加载模板
    >  template_path = template_root + file_name
    >  try:
    >      with open(template_path, 'rb') as file:
    >          print("模板资源:%s请求成功！" % template_path)
    >          response_body = file.read()
    > 
    >      # 模板替换操作，与数据库交互
    >      # 2. 连接数据库获取数据
    >      conn = pymysql.connect(host='localhost', user='root', password='******', database='stock_db')
    >      cur = conn.cursor()
    >      sql = "select i.code, i.short, i.chg, i.turnover, i.price, i.highs, f.note_info from info as i inner join " \
    >            "focus as f where i.id = f.id; "
    >      cur.execute(sql)
    >      # conn.commit()
    >      result_list = cur.fetchall()
    >      # mysql_data = str(result_list)
    >      cur.close()
    >      conn.close()
    > 
    >      html_template = """
    >          <tr>
    >              <td>%s</td>
    >              <td>%s</td>
    >              <td>%s</td>
    >              <td>%s</td>
    >              <td>%s</td>
    >              <td>%s</td>
    >              <td>%s</td>
    >              <td>
    >                  <a type="button" class="btn btn-default btn-xs" href="/update/%s.html"> <span class="glyphicon glyphicon-star" aria-hidden="true"></span> 修改 </a>
    >              </td>
    >              <td>
    >                  <input type="button" value="删除" id="toDel" name="toDel" systemidvaule="%s">
    >              </td>
    >          </tr>
    >          """
    >      mysql_data = ""
    >      for info in result_list:
    >          # mysql_data += html_template % (info[0], info[1], info[2], info[3], info[4], info[5], info[6], info[7],
    >          # info[0])
    >          info = list(info)
    >          info.extend((info[0], info[0]))
    >          mysql_data += html_template % tuple(info)
    > 
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
    > # 更新页面
    > # @route("/update.py")
    > @route(r"/update/(\d*).html")
    > def update(file_name, route_url):
    >  """返回update.html需要的页面内容"""
    >  template_path = template_root + '/update.html'
    >  try:
    >      with open(template_path, 'rb') as file:
    >          print("模板资源:%s请求成功！" % template_path)
    >          response_body = file.read()
    >          
    >      # 模板替换操作，与数据库交互
    >      ret = re.match(route_url, file_name)
    >      if ret:
    >          stock_code = ret.group(1)
    >      else:
    >          stock_code = "股票代码格式错误！正确格式为：", route_url
    > 
    >      # 将提取到的股票编码返回到浏览器中,以检查是否能够正确的提取url的数据
    >      response_body = re.sub(r"\{%code+%\}", stock_code, response_body.decode()).encode()
    >      response_headers = [("Data", time.ctime())]
    >  except Exception as e:
    >      print("模板资源路径:%s无法访问! Error:%s" % (template_path, e))
    >      response_headers = []
    >      response_body = "Error!! %s" % str(e)
    >      response_body = response_body.encode()
    >  return response_headers, response_body
    > 
    > 
    > def not_found(file_name, route_url=None):
    >  """返回路径错误时的页面内容"""
    >  template_path = template_root + file_name
    >  try:
    >      with open(template_path, 'rb') as file:
    >          response_body = file.read()
    >      response_headers = []
    >  except Exception as e:
    >      print("模板资源路径:%s不存在! Error:%s" % (template_path, e))
    >      response_body = "Error!! %s" % str(e)
    >      response_body = response_body.encode()
    >      response_headers = []
    >  return response_headers, response_body
    > ```

##### 6.3.2 更新页面内容加载：查询

* 更新页面：`update.html`显示个人中心选择修改备注时对应的备注更新页面

* 更新页面会显示所选股票代码及其备注(备注可以修改)

* 修改`templates.py`的`update_info()`函数，结合路由器正则表示

  * 打开模板文件update.html，加载模板页面

  * 通过正则匹配，获取股票代码

  * 如果股票代码不为空，即是通过个人主页的修改按钮跳转至更新页面，连接数据库，查询其备注，并通过`re.sub()`替换模板文件中的body部分，将股票信息显示在页面中

  * 如果股票代码为空，即直接访问的`/update.html`页面，同样替换模板信息，显示有关股票代码和备注的提示信息

    > `templates_mysql.py`
    >
    > ```python
    > @route(r"/update.html|/update/(\d*)\.html")
    > def update_info(file_name, route_url):
    >  """返回update.html需要的页面内容"""
    >  # 1. 打开模板文件，加载模板
    >  template_path = template_root + '/update.html'
    >  try:
    >      with open(template_path, 'rb') as file:
    >          print("模板资源:%s请求成功！" % template_path)
    >          response_body = file.read()
    >      print('--' * 30)
    > 
    >      # 2. 正则匹配，获取股票代码stock_code
    >      ret = re.match(route_url, file_name)
    >      if ret:
    >          stock_code = ret.group(1)
    >      else:
    >          stock_code = 'Error'
    > 
    >      response_headers = [("Data", time.ctime())]
    > 
    >      if stock_code:
    >          # 3. 模板替换操作，与数据库交互
    >          conn = pymysql.connect(host='localhost', user='root', password='ly666996', database='stock_db')
    >          cur = conn.cursor()
    >          # 3.1 SQL防注入，参数化
    >          sql = "select f.note_info from focus as f inner join info as i on f.id = i.id where i.code = %s;"
    >          cur.execute(sql, stock_code)
    >          # conn.commit()
    >          result_list = cur.fetchone()
    >          note_info = result_list[0]
    >          cur.close()
    >          conn.close()
    > 
    >          # 4. 将提取到的股票编码返回到浏览器中,以检查是否能够正确的提取url的数据
    >          response_body = re.sub(r"\{%code+%\}", stock_code, response_body.decode()).encode()
    >          response_body = re.sub(r"\{%note_info+%\}", note_info, response_body.decode()).encode()
    >      else:
    >          response_body = re.sub(r"\{%code+%\}", "Stock Code", response_body.decode()).encode()
    >          response_body = re.sub(r"\{%note_info+%\}", "Stock Note Information", response_body.decode()).encode()
    > 
    >  except Exception as e:
    >      print("模板资源路径:%s无法访问! Error:%s" % (template_path, e))
    >      response_headers = []
    >      response_body = "Error!! %s" % str(e)
    >      response_body = response_body.encode()
    >  return response_headers, response_body
    > ```

##### 6.3.3 更新页面：修改

* 修改更新页面中显示的所选股票代码的备注，点击update页面的修改按钮时触发

* 在`templates.py`中新增函数`update_change()`并用路由装饰器进行装饰

  * 通过正则匹配，获取股票代码和新的备注，需要用`url.unquote()`解码来使备注正确储存

  * 连接数据库，通过update+内连接修改focus表中的原始备注信息

  * 对于修改成功、股票代码出错两种情况，分别返回对应的响应主体信息

    > ```python
    > @route(r"/update/(\d*)/(.*)\.html")
    > def update_change(file_name, route_url):
    >  # 获取股票代码stock_code和新的信息
    >  ret = re.match(route_url, file_name)
    >  if ret:
    >      stock_code = ret.group(1)
    >      new_note_info = ret.group(2)
    >      # 当修改的信息中含有中文时，url编码与解码可能会出现问题，所以需要unquote函数
    >      new_note_info = unquote(new_note_info)
    > 
    >      # 与数据库交互，将修改后的数据存入数据库
    >      conn = pymysql.connect(host='localhost', user='root', password='ly666996', database='stock_db')
    >      cur = conn.cursor()
    >      # SQL防注入，参数化
    >      sql = "update focus f inner join info i on f.id = i.id set f.note_info = %s where i.code = %s;"
    >      cur.execute(sql, (new_note_info, stock_code))
    >      conn.commit()
    >      cur.close()
    >      conn.close()
    > 
    >      response_body = "Stock's notes has updated!"
    > 
    >  else:
    >      stock_code = 'Stock code is Error!'
    >      response_body = "Updated failed:", stock_code
    > 
    >  response_headers = [("Data", time.ctime())]
    >  return response_headers, response_body.encode()
    > ```

##### 6.3.4 首页：添加

* 通过首页的添加收藏按钮，将对应股票添加到个人收藏中，可在个人中心页面中显示

* 在`templates.py`中新增函数`add()`函数，并装饰

  * 通过正则匹配，获取股票代码

  * 连接数据库，可以通过select获取股票名称等其他信息

  * 判断该股票是否已经被收藏，即通过内连接查询该股票的序号id在focus表中是否存在

  * 若存在，无需重复添加，若不存在，通过insert+子查询将该股票添加到收藏focus表中

  * 对于存在和不存在、以及股票代码出错三种情况，分别返回对应的响应主体信息

    > ```python
    > # // alert("/add/" + code + ".html");
    > @route(r"/add/(\d*)\.html")
    > def add(file_name, route_url):
    >  """添加收藏"""
    >  # 1. 正则匹配，获取股票代码
    >  ret = re.match(route_url, file_name)
    > 
    >  if ret:
    >      stock_code = ret.group(1)
    > 
    >      # 2. 连接数据库
    >      conn = pymysql.connect(host='localhost', user='root', password='ly666996', database='stock_db')
    >      cur = conn.cursor()
    > 
    >      cur.execute("select short from info where code = %s;", stock_code)
    >      stock_name = cur.fetchone()[0]
    > 
    >      # 2.1 判断该股票是否已收藏，即是否在focus表中
    >      # SQL防注入，参数化
    >      sql_judge = "select i.code from info i inner join focus f on i.id = f.id where i.code = %s;"
    >      row = cur.execute(sql_judge, stock_code)
    >      # 2.2 股票数据已存在于focus
    >      if row:
    >          # 无需再次添加
    >          response_body = "The stock:%s has already focused!" % stock_code
    >      # 2.3 股票数据不存在于focus
    >      else:
    >          # SQL防注入，参数化
    >          sql = "insert into focus(id) (select id from info where code = %s);"
    >          # 不存在，添加进数据库
    >          cur.execute(sql, stock_code)
    >          conn.commit()
    > 
    >          response_body = "The stock : %s is successfully focused!\r\n" % stock_name
    >          response_body += "The stock' code is %s." % stock_code
    >      cur.close()
    >      conn.close()
    >  else:
    >      stock_code = 'Stock code is Error!'
    >      response_body = "Focused failed:" % stock_code
    > 
    >  # 3. 返回收藏成功、失败相关信息
    >  response_headers = [("Data", time.ctime())]
    >  return response_headers, response_body.encode()
    > ```

##### 6.3.5 个人中心：删除

* 通过个人中心的删除按钮，将对应的股票从收藏中移除

* 在`templates.py`中新增函数`delete()`函数，并装饰

  * 通过正则匹配，获取股票代码

  * 连接数据库，可以通过select获取股票名称等其他信息

  * 判断该股票是否已被收藏，即通过内连接查询该股票的序号id在focus表中是否存在

  * 若存在，通过delete+子查询将其删除，若不存在，无需再次删除

  * 对于存在和不存在、以及股票代码出错三种情况，分别返回对应的响应主体信息

    > ```python
    > # // alert("/del/" + code + ".html");
    > @route(r"/del/(\d*)\.html")
    > def delete(file_name, route_url):
    >  """取消收藏"""
    >  # 1. 正则匹配，获取股票代码
    >  ret = re.match(route_url, file_name)
    > 
    >  if ret:
    >      stock_code = ret.group(1)
    > 
    >      # 2. 连接数据库
    >      conn = pymysql.connect(host='localhost', user='root', password='ly666996', database='stock_db')
    >      cur = conn.cursor()
    > 
    >      cur.execute("select short from info where code = %s;", stock_code)
    >      stock_name = cur.fetchone()[0]
    > 
    >      # 2.1 判断该股票是否已收藏，即是否在focus表中
    >      # SQL防注入，参数化
    >      sql_judge = "select i.code from info i inner join focus f on i.id = f.id where i.code = %s;"
    >      row = cur.execute(sql_judge, stock_code)
    >      # 2.2 股票数据已存在于focus表中
    >      if row:
    >          # 将其从数据库的focus表中删除
    >          # SQL防注入，参数化
    >          sql = "delete from focus where id = (select id from info where code = %s);"
    >          cur.execute(sql, stock_code)
    >          conn.commit()
    >          response_body = "The stock : %s is successfully deleted!\r\n" % stock_name
    >          response_body += "The stock' code is %s." % stock_code
    >      # 2.3 股票数据不存在于focus表中
    >      else:
    >          # 股票不存在，没有收藏即无需删除
    >          response_body = "The stock:%s hasn't focused!" % stock_code
    >      cur.close()
    >      conn.close()
    >  else:
    >      stock_code = 'Stock code is Error!'
    >      response_body = "Delete failed:" % stock_code
    > 
    >  # 3. 返回删除成功、失败相关信息
    >  response_headers = [("Data", time.ctime())]
    >  return response_headers, response_body.encode()
    > ```

---

#### 6.4 log日志模块

* 日志级别

  * DEBUG：详细的信息，通常只出现在诊断问题上；
  * INFO：确认一切按预期运行；
  * WARNING：一个迹象表明，一些意想不到的事情发生了，或表明一些问题在不久的将来(如，磁盘空间不足)发生，这个软件还能按预期工作；
  * ERROR：更严重的问题，软件没能执行一些功能；
  * CRITICAL：一个严重的错误，这表明程序本身可能无法继续运行。

  > 日志用于调试程序(debug)，这五个等级，分别对应5种输出日志的方法： debug 、info 、warning 、error 、critical。默认的是WARNING，当在WARNING或更严重的错误时才被跟踪。

* 日志输出

  > 两种方式记录跟踪，一种输出控制台，另一种是记录到文件中，如日志文件。

* 日志输出到控制台

  * 方法

    > 使用logging模块，logging.basicConfig函数，默认输出warning及以上的对应信息
    >
    > ```python
    > import logging
    > 
    > # 默认：level=logging.warning
    > logging.basicConfig(level=logging.DEBUG,
    >                  format='%(asctime)s - %(filename)s[line:%(lineno)d] - %(levelname)s: %(message)s')
    > 
    > # 开始使用log功能，在程序执行出现对应问题的地方报出下面设置的信息
    > logging.info('这是 loggging info message')
    > logging.debug('这是 loggging debug message')
    > logging.warning('这是 loggging a warning message')
    > logging.error('这是 an loggging error message')
    > logging.critical('这是 loggging critical message')
    > ```

  * 注意

    * 设置日志level时要将级别大写
    * 只要用过一次log功能再次设置格式时将失效，实际开发中格式肯定不会经常变化，所以刚开始时需要设定好格式

* 日志输出到日志文件

  * 方法

    > 使用logging模块，logging.basicConfig函数，传入参数不仅是level和格式format，还要传入文件名和写入模式
    >
    > ```python
    > import logging
    > 
    >  # 默认：level=logging.warning
    >  logging.basicConfig(level=logging.DEBUG,
    >                      filename='./log.txt',
    >                      filemode='w',
    >                      format='%(asctime)s - %(filename)s[line:%(lineno)d] - %(levelname)s: %(message)s',
    >                      datefmt='%a, %d %b %Y %H:%M:%S')
    > 
    >  # 开始使用log功能，在程序执行出现对应问题的地方报出下面设置的信息
    >  logging.info('这是 loggging info message')
    >  logging.debug('这是 loggging debug message')
    >  logging.warning('这是 loggging a warning message')
    >  logging.error('这是 an loggging error message')
    >  logging.critical('这是 loggging critical message')
    > ```

* 日志输出格式

  > logging.basicConfig函数中，可以指定日志的输出格式format，参数格式如下:

  - %(levelno)s: 打印日志级别的数值

  - %(levelname)s: 打印日志级别名称

  - %(pathname)s: 打印当前执行程序的路径，其实就是sys.argv[0]

  - %(filename)s: 打印当前执行程序名

  - %(funcName)s: 打印日志的当前函数

  - %(lineno)d: 打印日志的当前行号

  - %(asctime)s: 打印日志的时间

  - %(thread)d: 打印线程ID

  - %(threadName)s: 打印线程名称

  - %(process)d: 打印进程ID

  - %(message)s: 打印日志信息

    > ```python
    > format='%(asctime)s - %(filename)s[line:%(lineno)d] - %(levelname)s: %(message)s')
    > ```

* 日志同时输出到终端和文件，且设置编码

  * 创建logger对象，

  * 创建handle对象，然后分别设置级别、格式和文件(路径、编码、写入模式)

  * 将logger添加到handle中

    > ```python
    > import logging
    > 
    >  # 1. 创建logger对象
    >  logger = logging.getLogger()
    >  # 不传入name的话，logger的默认名字为root
    >  # print(logger.name)  # root
    >  # 设置总log等级
    >  logger.setLevel(logging.INFO)  # 如果下面的等级设置比总等级低，按照总等级来log
    > 
    >  file_log_flag = True
    >  terminal_log_flag = True
    >  file_path = r"./log.txt"
    > 
    >  # 2. 调用模块时，如果错误引用，比如多次调用，每次会添加Handler，造成重复日志，
    >  # 所以每次创建日志前都移除掉所有的handler，后面在重新添加，可以解决这类问题
    >  while logger.hasHandlers():
    >      for i in logger.handlers:
    >          logger.removeHandler(i)
    > 
    >  # 3. 创建handle对象并设置级别和格式——文件，添加logger到handle
    >  if file_log_flag:
    >      # 默认写入模式为 'a' 追加模式
    >      fh = logging.FileHandler(file_path, mode='a', encoding='utf8')
    >      formatter = logging.Formatter('%(asctime)s - %(filename)s[line:%(lineno)d] - %(levelname)s: %(message)s')
    >      fh.setLevel(logging.DEBUG)
    >      fh.setFormatter(formatter)
    >      logger.addHandler(fh)
    > 
    >  # 4. 创建handle对象并设置级别和格式——终端，添加logger到handle
    >  if terminal_log_flag:
    >      ch = logging.StreamHandler()
    >      formatter = logging.Formatter('%(asctime)s - %(filename)s[line:%(lineno)d] - %(levelname)s: %(message)s')
    >      ch.setLevel(logging.WARNING)
    >      ch.setFormatter(formatter)
    >      logger.addHandler(ch)
    > 
    >  # 开始使用log功能，在程序执行出现对应问题的地方报出下面设置的信息
    >  logging.info('这是 loggging info message')
    >  logging.debug('这是 loggging debug message')
    >  logging.warning('这是 loggging a warning message')
    >  logging.error('这是 an loggging error message')
    >  logging.critical('这是 loggging critical message')
    > ```

* 注意

  * 将日志功能添加到Web服务器的入口模块中，不要写在函数中；
  * 要针对每个可能的情况进行不同级别的log日志。

---

#### 6.5 小结

* 浏览器从请求到显示动态页面的过程简析

  * 浏览器发起动态资源请求，将请求报文发给web服务器；
  * web服务器判断资源类型，通过app接口函数将请求传给web应用程序；
  * web应用程序根据用户请求，将查询到的数据和模板⽂件拼接⼀个完整网页；
  * web应⽤程序将网页数据回传给web服务器；
  * web服务器将接收到的响应状态、响应头、响应体拼接为响应报文发送浏览器；
  * 浏览器接收响应报文并且解析、显示，如果浏览器在显示页面的时候还需要⼀些其他资源，会继续向服务器发起请求直到获取到所有需要的数据。

* 路由字典与路由装饰器

  * 装饰器要在外函数中就把路径添加到路由字典中；
  * 装饰器的内函数的参数要设置为`(*args, **kwargs)`；
  * 指定的模板文件路径，路径与函数一对一：`if path in dict:`；
  * 正则匹配的可变模板文件路径，路径与函数多对一：`re.match(url, path)`；

  * 对于网页登录、添加、修改、删除等功能，通过设置按钮跳转网页，再为新请求的网页地址设置对应的函数，实现其功能。

* 注意SQL注入问题，参数化解决；以及子查询、内连接的应用

* 伪静态

* 创建配置文件和shell脚本

  > 为了让服务器可以适用于不同的端口和框架，可以通过配置文件来设置这些传入程序的参数，如`myserver.conf`；
  >
  > 此外，还可以通过shell脚本来将运行的指令整合，简化使用，如`run.sh`。

* 元类与ORM

  * 自定义元类扩展原始类的功能，元类中重写`__new__(self)`方法，类似装饰器；
  * `class Goods(object, metaclass=user_type)`。

* 通过主从配置将不同类型的数据库操作分配到不同的服务器上实现，从而提高效率；

* 前后端分离

  > Django前后端不分离，Flask前后端分离；
  >
  > 可以通过在模板中添加ajax数据请求，来实现前后端分离，即使用tr标签封装数据库的数据这一过程在前端完成；
  >
  > 添加一个页面数据接口函数及其对应的路径，如`/center_data.html`与`center_data()`函数，在函数中获取数据库数据，并转成可以转换为json的格式，再通过json.dump()转换为json格式返回给浏览器。

---