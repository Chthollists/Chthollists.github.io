---
title: Python实战框架MiniWeb框架CP1——WSGI
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
  - WSGI
abbrlink: b0c11c34
date: 2020-08-30 13:14:58
top:
---

---

* Python实战框架miniWeb框架CP1——WSGI
* CP1主要分享了
* Web框架的基本概念
* 静态资源与动态资源
* 模板文件与替换
* WSGI协议
* 基本框架结构

<!-- more -->

# Python实战框架MiniWeb框架CP1——WSGI

## 六、miniWeb框架

* 时间：2020.08.30~2020.09.04

### 1. 框架基础

* 时间：2020.08.30

#### 1.1 基础概念

* 服务器资源

  * 静态资源

    > 概念：不需要经常变化的资源，由于该资源不需要经常变化，所以可以提前准备，如 png、jpg、css、js等⽂件。

  * 动态资源

    > 概念：会经常变化的资源，由于经常变化，所以无法提前准备，需要根据用户的选择来提供不同的资源。

* 服务器与框架的关系

  * Web服务器接收从用户客户端(浏览器)发来的请求，根据不同的请求返回不同的资源；
  * 请求为静态资源时，直接返回服务器上预先准备好的文件；
  * 请求为动态资源时，需要根据动态资源请求协议(如，WSGI协议)来调用Web框架应用程序，通过模板文件和数据库返回动态资源；
  * 服务器作用
    * 用户浏览器进行交互，接收客户端请求，发送对应的响应；
    * 调用框架，在web框架和web浏览器之间进行数据的中转。

* 模板文件

  * 概念

    > 模板文件就是网页中通用的结构构成的⼀个页面；
    >
    > 该页面中不含有任何用户需要查看的数据,，当用户查询数据的时候会将最终的结果放到模板中形成用户真正需要的页面。

  * 模板替换

    > 把模板文件转化为用户真正看到的网页的过程。

* 通信规范

  * 服务器与客户端(浏览器)：HTTP协议
  * Web服务器与Web框架
    * 服务器在接收到浏览器的动态资源请求时，会将请求信息切割拆分为键值对放到⼀个字典中；
    * 服务器调用框架提供的方法执行框架的功能，并且传给框架⼀个字典；
    * 框架通过字典取出用户请求，并根据请求产生处理状态、响应头、响应体；
    * 框架通过返回值将响应报文发送给服务器，完成⼀次动态资源请求的过程。

* WSGI(Web Server Gateway Interface)：Web服务器网关接口

  * 概念

    > WSGI 是Python语言中Web服务器与Web框架间的一个通信协议，规定Web服务器将动态资源的请求信息传递给Web框架，Web框架将处理好的完整网页信息传回给服务器。

  * 特点

    > WSGI 允许开发者将web框架和web服务器分开，可以混合匹配web服务器和web框架，选择一个适合的配对；
    >
    > Web服务器必须具备WSGI接口，所有的现代Python Web框架都已具备WSGI接口，使得不对代码作修改的前提下就能使服务器和特定的web框架协同工作；
    >
    > 可以使底层web服务器对HTTP的解析部分与应用程序逻辑部分分离开实现；
    >
    > 对应于Java中的Servlet API，或Ruby中的Rack接口协议。

  * 定义WSGI接口

    > ```python
    > def application(environ, start_response):
    >  start_response('200 OK', [('Content-Type', 'text/html')])
    >  return 'Hello World!'
    > ```
    >
    > `application()`函数就是符合WSGI标准的一个HTTP处理函数，也称作Web框架应用程序；
    >
    > `environ`参数表示一个包含所有HTTP请求信息(请求报文)的dict对象；
    >
    > `start_response`参数表示一个发送HTTP响应的函数。

  * `environ`字典

    > 该字典中存放动态资源请求的相关信息，如模板路径`'PATH_INFO': '/index.html'`等；
    >
    > ```python
    > {
    >  'HTTP_ACCEPT_LANGUAGE': 'zh-cn',
    >  'wsgi.file_wrapper': <built-infunctionuwsgi_sendfile>,
    >  'HTTP_UPGRADE_INSECURE_REQUESTS': '1',
    >  'uwsgi.version': b'2.0.15',
    >  'REMOTE_ADDR': '172.16.7.1',
    >  'wsgi.errors': <_io.TextIOWrappername=2mode='w'encoding='UTF-8'>,
    >  'wsgi.version': (1,0),
    >  'REMOTE_PORT': '40432',
    >  'REQUEST_URI': '/',
    >  'SERVER_PORT': '8000',
    >  'wsgi.multithread': False,
    >  'HTTP_ACCEPT': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
    >  'HTTP_HOST': '172.16.7.152: 8000',
    >  'wsgi.run_once': False,
    >  'wsgi.input': <uwsgi._Inputobjectat0x7f7faecdc9c0>,
    >  'SERVER_PROTOCOL': 'HTTP/1.1',
    >  'REQUEST_METHOD': 'GET',
    >  'HTTP_ACCEPT_ENCODING': 'gzip,deflate',
    >  'HTTP_CONNECTION': 'keep-alive',
    >  'uwsgi.node': b'ubuntu',
    >  'HTTP_DNT': '1',
    >  'UWSGI_ROUTER': 'http',
    >  'SCRIPT_NAME': '',
    >  'wsgi.multiprocess': False,
    >  'QUERY_STRING': '',
    >  'PATH_INFO': '/index.html',
    >  'wsgi.url_scheme': 'http',
    >  'HTTP_USER_AGENT': 'Mozilla/5.0(Macintosh;IntelMacOSX10_12_5)AppleWebKit/603.2.4(KHTML,likeGecko)Version/10.1.1Safari/603.2.4',
    >  'SERVER_NAME': 'ubuntu'
    > }
    > ```

#### 1.2 miniWeb基本框架结构

* `miniWebServer.py`

  > 用于实现Web服务器

* `static`

  > 用于存放静态资源

  * `index.html`
  * `images`
  * `css`
  * `js`

* `application`

  > 用于实现动态资源，存放Web框架应用程序

  * `app.py`
  * `utils.py`

* `templates`

  > 用于存放模板文件

  * `center.html`
  * `update.html`