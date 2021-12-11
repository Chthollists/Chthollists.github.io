---
title: Hexo博客搭建系列Chapter6——Hexo博客功能扩展(2/5)
toc: true
reward: true
declare: true
gitalkenable: true
categories: 
- others
- Hexo
tags:
  - Hexo
  - busuanzi
  - hexo-wordcount
  - hexo-blog-encrypt
abbrlink: f9e02555
date: 2020-07-31 20:10:40
top:
---

---

* 在Windows系统上利用Hexo搭建博客系列——Chapter6
* Chapter6：
* 扩展了不蒜子统计功能、字数统计功能、阅读时长功能
* 扩展了网易云音乐功能、文章加密功能

<!-- more -->

# Hexo博客搭建系列——Chapter 6

---

## 五、高级功能实现

### 7. 不蒜子统计功能

#### 7.1 安装不蒜子统计脚本

* [不蒜子官网](http://busuanzi.ibruce.info/)

* 不蒜子用来统计博客的访问量在`themes\yilia\layout\_partial\after-footer.ejs`文件最后添加

  > ```html
  > <script  async  src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
  > ```

#### 7.2 博客访问量统计

* 在`themes\yilia\layout\_partial\footer.ejs`中

  > 在`<div class="footer-right">`标签内添加：
  >
  > ```html
  > <!-- # PV方式，单个用户连续点击 n 篇，记录 n 次记录值 -->
  > <span id="busuanzi_container_site_pv"> 总访问量: <span id="busuanzi_value_site_pv"></span> /</span>
  > <!-- # UV方式，单个用户连续点击 n 篇，记录 1 次记录值 -->
  > <span id="busuanzi_container_site_uv">  访客数:<span id="busuanzi_value_site_uv"></span></span>
  > ```

* 美化显示

  > ```html
  > <font color="red" face="Microsoft YaHei" size= 3><b> 内容：<%=content%></b></font>
  > ```
  >
  > 内容：<%=content%>根据需求改变

#### 7.3 单篇文章点击量统计方法一

* 在`themes/yilia/layout/_partial/article.ejs`中

  > ```html
  > <%- partial('post/title', {class_name: 'article-title'}) %>
  > ```
  >
  > 后面添加如下代码：
  >
  > ```html
  > <!--显示阅读次数-->
  > <% if (!index && post.comments){ %>
  > <br/>
  > <a class="cloud-tie-join-count" href="javascript:void(0);" style="color:gray;font-size:14px;">
  > <span class="icon-sort"></span>
  > <span id="busuanzi_container_page_pv" style="color:#ef7522;font-size:14px;">
  >          阅读数: <span id="busuanzi_value_page_pv"></span>次 &nbsp;&nbsp;
  > </span>
  > </a>
  > <% } %>
  > <!--显示阅读次数完毕-->
  > ```

#### 7.4 单篇文章点击量统计方法二

* 找到`themes\yilia\layout\_partial\post\nav.ejs`，注释掉以下两行：大概在27行左右(无此代码请忽略)

  > ```html
  > <span class="post-meta-item-text">本文阅读量 </span>
  > <span class="leancloud-visitors-count">8848</span>
  > ```
  >
  > 注释掉之后直接在这两行下面加上：
  >
  > ```html
  > <span id="busuanzi_container_page_pv">
  >      本文阅读量: <span id="busuanzi_value_page_pv"></span>次
  > </span>
  > ```

#### 7.5 不蒜子统计与Live2D冲突问题

* 参考：[不蒜子功能失效](https://boyinthesun.cn/post/error-live2d-busuanzi/)

* 问题：当配置了Live2D功能后，不蒜子统计功能会与其发生冲突从而失效

* 分析：查看网页源码Ctrl+u，可以发现：

  * 不蒜子功能正常时

    > ```html
    > <span id="busuanzi_container_site_pv">
    >  |&nbsp;<i class="far fa-eye"></i>&nbsp;总访问量:&nbsp;<span id="busuanzi_value_site_pv"
    >      class="white-color"></span>&nbsp;次
    >  </span>
    > <span id="busuanzi_container_site_uv">
    >  |&nbsp;<i class="fas fa-users"></i>&nbsp;总访问人数:&nbsp;<span id="busuanzi_value_site_uv"
    >      class="white-color"></span>&nbsp;人
    >  </span>
    > </span>
    > ```

  * 不蒜子功能失效时

    > ```html
    > <span id="busuanzi_container_site_pv" style="display: none;">
    >  |&nbsp;<i class="far fa-eye"></i>&nbsp;总访问量:&nbsp;<span id="busuanzi_value_site_pv" 
    >      class="white-color">2303950</span>&nbsp;次
    > </span>
    > <span id="busuanzi_container_site_uv" style="display: none;">
    >  |&nbsp;<i class="fas fa-users"></i>&nbsp;总访问人数:&nbsp;<span id="busuanzi_value_site_uv" 
    >      class="white-color">160644</span>&nbsp;人
    > </span>
    > ```

  * 所以导致失效的原因是多了一个display的属性值变成了none，从而不显示不蒜子统计

    > ```html
    > style="display: none;"
    > ```

* 解决办法：

  * 打开不蒜子官网上的[busuanzi.pure.mini.js](https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js)文件，将其下载到本地，放到yilia主题下的路径中

    > 如：`theme/yilia/source`路径

  * 修改busuanzi.pure.mini.js文件

    > ```yml
    > # 找到
    > b.style.display="none"
    > 
    > # 改为
    > b.style.display=''
    > ```

  * 修改不蒜子统计的调用语句

    > 原来是从不蒜子统计的服务器上调用busuanzi.pure.mini.js
    >
    > 现在直接从本地调用，还可以避免不蒜子官网域名改变导致的功能失效问题

    > 路径：`themes\yilia\layout\_partial\after-footer.ejs`
    >
    > ```html
    > # 原始
    > <script  async  src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
    > 
    > # 修改为本地路径：/busuanzi.pure.mini.js
    > <script  async  src="/busuanzi.pure.mini.js"></script>
    > ```

---

### 8. 字数统计和阅读时长功能

#### 8.1 字数统计

* 安装插件 hexo-wordcount

  > ```bash
  > npm i -save hexo-wordcount
  > ```

* 博客根目录`_config.yml`添加配置

  > ```yml
  > word_count: true
  > ```

* 修改`themes\yilia\layout\_partial\article.ejs`

  > 查找
  >
  > ```html
  > <%- partial('post/date', {class_name: 'archive-article-date', date_format: null}) %>
  > ```
  >
  > 后面添加
  >
  > ```html
  > <% if(theme.word_count && !post.no_word_count){%>
  >        <%- partial('post/word') %>
  >        <% } %>
  > ```

* 最后在`themes\yilia\layout\_partial\post`目录下新建`word.ejs`,内容如下

  > ```html
  > <div style="margin-top:10px;">
  >  <span class="post-time">
  >    <span class="post-meta-item-icon">
  >      <i class="fa fa-keyboard-o"></i>
  >      <span class="post-meta-item-text" style="font-size: 16px;color: grey">  字数统计: </span>
  >      <!--这里样式可以自己定制-->
  >      <span class="post-count" style="font-size: 16px;color: grey"><%= wordcount(post.content) %>字</span>
  >    </span>
  >  </span>
  > 
  >  <span class="post-time">
  >    &nbsp; | &nbsp;
  >    <span class="post-meta-item-icon">
  >      <i class="fa fa-hourglass-half"></i>
  >      <span class="post-meta-item-text" style="font-size: 16px;color: grey">  阅读时长: </span>
  >      <span class="post-count" style="font-size: 16px;color: grey"><%= min2read(post.content) %>分</span>
  >    </span>
  >  </span>
  > </div>
  > ```

#### 8.2 阅读时长统计

> 步骤与7.1相同

* 安装插件 hexo-wordcount
* 博客根目录`_config.yml`添加配置
* 修改`themes\yilia\layout\_partial\article.ejs`
* 最后在`themes\yilia\layout\_partial\post`目录下新建`word.ejs`

---

### 9. 背景音乐功能

> 使用网易云音乐

#### 9.1 文章内音乐

* 从网易云音乐获取音乐外链

  > ```html
  > <iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=468490592&auto=0&height=66"></iframe>
  > ```

* 将外链输入到文章中即可

  ```html
  <iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=110 src="//music.163.com/outchain/player?type=4&id=1217042&auto=0&height=90"></iframe>
  ```

#### 9.2 主页背景音乐

* 获取外链

  > 注意：要获取多个音乐的外链可以将其添加到歌单、电台中，获取外链
  >
  > auto 表示是否自动播放
  >
  > ```html
  > <iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=4993317&auto=1&height=66"></iframe>
  > ```

* 引入播放器代码

  在`themes/yilia/layout/_partial/left-col.ejs`文件`nav`标签中添加代码

  >  注：放在`<nav>`和`</nav>`之内
  >
  >  ```html
  >  <!-- 网易云音乐插件 -->
  >  <% if (theme.music && theme.music.enable){ %>
  >   <div style="position:absolute; bottom:120px left:auto; width:85%">
  >      <iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=228 height=52 src="//music.163.com/outchain/player?type=4&id=349623778&auto=0&height=32"></iframe>
  >   </div>
  >  <% } %>
  >  ```
  >
  >  通过设置marginheight="0" width=330 height=110 来改变大小，美化界面

* 在yilia主题配置文件`_config.yml`中添加属性

  > ```yml
  > # 网易云音乐插件
  > music:
  > enable: true
  > #id: 1332647902  # 网易云分享的ID
  > autoplay: false  # 是否开启自动播放
  > ```

#### 9.3 遗留问题(未解决)

* **歌单外链错误 : music.163.com上的嵌入式页面显示资源加载失败**

  > 单首音乐对象直接生成外链、电台对象生成外链均不会出现资源加载失败问题；
  >
  > 但是，使用歌单对象(即使歌单只有一首音乐)，会出现资源加载失败错问题；
  >
  > 推测可能是歌单后台有bug，已反馈给网易云客服。

* [解决办法](https://blog.csdn.net/hubaoquanu/article/details/79084193)

  * 原因：是由于开启了IPv6后使用了google的IPv6 DNS，音乐资源网站ip解析会直接错误解析到本地127.0.0.1

  * 方法(均无效)：

    * 添加以下内容到hosts文件C:\Windows\System32\drivers\etc\hosts

      > ```bash
      > # 解决ipv6开启后网易云音乐无法播放的问题
      > 111.19.136.246 m10.music.126.net
      > 111.20.255.169 p1.music.126.net
      > 223.252.199.66 music.163.com
      > ```

      直接停用IPv6功能，在网络和共享中心---网络状态---属性里面，不勾选TCP/IPv6。

    * 使用国内的IPv6DNS: 240c::6666，此时无法访问Google和Youtube。

---

### 10. 文章加密功能

> 文章加密一般有两种方式：
>
> 1.Nginx授权访问；
>
> 2.Hexo-Blog-Encrypt；
>
> 由于Nginx授权访问的前置条件是：Hexo博客搭在Nginx服务器；这里用Encrypt插件实现加密

#### 10.1 引入加密插件

* 安装加密插件Encrypt

  > 找到博客根目录下的`package.json`,最下面添加
  >
  > ```yml
  > "hexo-blog-encrypt": "2.0.*"
  > ```
  >
  > 注意：添加时要记得给前一项加上  **,**

* 进入博客根目录，Git bash，安装

  > ```bash
  > npm install
  > # 或者
  > cnpm
  > ```

#### 10.2 修改配置

* 找到根目录下的`_config.yml`文件，添加

  > ```yml
  > # Security
  > #
  > encrypt:
  >  enable: true
  > ```

#### 10.3 修改MD文章开头属性

* 在需要加密的文章.md文件开头写入

  > password: 这里填密码
  > abstract: 这里是博客简述(可见)
  > message: 输入密码提示语句
  >
  > ```yml
  > ---
  > title: 加密文章
  > date: 2019-05-30 22:02:07
  > password: 这里填密码
  > abstract: 这里是博客简述（能被访客看见）
  > message: 输入密码提示语句（例如：请输入密码）
  > ---
  > ```

---

