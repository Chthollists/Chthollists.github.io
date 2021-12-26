---
title: Hexo博客搭建系列Chapter10——Hexo博客遗留问题(完结)
toc: true
reward: true
declare: true
gitalkenable: true
categories: 
- others
- Hexo
top: 2
tags:
  - Hexo
  - busuanzi
  - Live2D
  - Coding Pages
  - Gitalk
abbrlink: a8cc0871
date: 2020-07-31 20:11:20
---

---

* 在Windows系统上利用Hexo搭建博客系列——Chapter10

* Chapter10：

  * 分析并解决了不蒜子统计与Live2D冲突导致的不蒜子统计功能失效问题
  * 分析并解决了网易云音乐外链资源加载失败的问题，但歌单外链仍会失败
  * 分析并解决了博客网站无法被百度收录的问题
  * 分析了Gitalk评论出现`Error : Network Error`的原因及解决办法，但仍未解决
* Tip：
  * 如果有人解决了Gitalk的`Error : Network Error`问题，请在评论区分享或私信博主
  * 后面会介绍如何在多台设备上一起更新博客

---

<!-- more -->

# Hexo博客搭建系列——Chapter 10

---

## Hexo博客搭建中的遗留问题与解决方法

### 一、不蒜子统计与Live2D冲突

#### 1. 不蒜子统计失效

* 问题：当配置了Live2D功能后，不蒜子统计功能会与其发生冲突从而失效
* 参考：[不蒜子功能失效](https://boyinthesun.cn/post/error-live2d-busuanzi/)

#### 2. 问题分析

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

    > ```yml
    > style="display: none;"
    > ```

#### 3. 解决办法：

* 打开不蒜子官网上的[busuanzi.pure.mini.js](https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js)文件，将其下载到本地，放到yilia主题下的路径中

  > 如：`theme/yilia/source`路径

* 修改`busuanzi.pure.mini.js`文件

  > ```js
  > // 找到
  > b.style.display="none"
  > 
  > // 改为
  > b.style.display=''
  > ```

* 修改不蒜子统计的调用语句：路径：`themes\yilia\layout\_partial\after-footer.ejs`

  * 原来是从不蒜子统计的服务器上调用busuanzi.pure.mini.js
  * 现在直接从本地调用，还可以避免不蒜子官网域名改变导致的功能失效问题
  
  > ```html
  ># 原始
  > <script  async  src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
  > 
  > # 修改为本地路径：/busuanzi.pure.mini.js
  > <script  async  src="/busuanzi.pure.mini.js"></script>
  > ```

#### 4. 结果：已解决

---

### 二、网易云外链资源加载失败

#### 1. 网易云音乐加载失败

* 问题：**歌单外链错误 : music.163.com上的嵌入式页面显示资源加载失败**


#### 2. 调研与分析

* 网上的解决办法参见：[解决办法](https://blog.csdn.net/hubaoquanu/article/details/79084193)；均无法解决该问题

* 原因：是由于开启了IPv6后使用了google的IPv6 DNS，音乐资源网站ip解析会直接错误解析到本地`127.0.0.1`

* 解决方法

  * 添加以下内容到hosts文件`C:\Windows\System32\drivers\etc\hosts`

    > ```bash
    > # 解决ipv6开启后网易云音乐无法播放的问题
    > 111.19.136.246 m10.music.126.net
    > 111.20.255.169 p1.music.126.net
    > 223.252.199.66 music.163.com
    > ```

  * 直接停用IPv6功能，在网络和共享中心---网络状态---属性里面，不勾选TCP/IPv6。

  * 使用国内的IPv6DNS: 240c::6666，此时无法访问Google和Youtube。

#### 3. 解决办法

* 单首音乐对象直接生成外链、电台对象生成外链均不会出现资源加载失败问题

* 但是，使用歌单对象(即使歌单只有一首音乐)，会出现资源加载失败错问题

* 推测可能是歌单后台有bug，已反馈给网易云客服

#### 4. 结果：已解决

---

### 三、百度推送收录

#### 1. 博客无法被百度收录

* 原因：由于Github Pages上禁止百度爬虫，所以无法实现百度收录。

* 解决方法：将Hexo博客双线部署到Coding Pages上，再通过主动推送、自动推送、SiteMap来实现百度收录。

#### 2. Hexo博客双线部署

* 详细教程：具体方法与部署到Github上类似
  * [Hexo双线部署CodingPages](https://blog.csdn.net/qq_36759224/article/details/100879609)、
  * [Hexo博客+Coding Pages](http://mfln8n.coding-pages.com/)
* 注册Coding Pages
* 新建项目--repo库
* 绑定SSH公钥
* Hexo部署到Coding Pages上
* Coding Pages生成静态网站

#### 3. 百度收录实现：主动推送+SiteMap

* 主动推送原理

  * 新链接的产生， hexo generate 会产生一个文本文件，里面包含最新的链接；

  * 新链接的提交， hexo deploy 会从上述文件中读取链接，提交至百度搜索引擎。

* 安装插件：`hexo-baidu-url-submit`

  > ```bash
  > npm install hexo-baidu-url-submit --save
  > ```

* 修改博客根目录配置文件`_config.yml`，添加

  > ```yml
  >baidu_url_submit:
  > count: 20 ## 提交最新的20个链接
  > host: http://mfln8n.coding-pages.com/ ## 百度站长平台中注册的域名
  > token:  ## 16位准入秘钥
  > path: baidu_urls.txt ## 文本文档的地址， 新链接会保存在此文本文档里
  > ```
  
* 获取准入秘钥

  > 在[百度搜索资源平台](https://ziyuan.baidu.com/)的平台收录中获取准入秘钥token ---16位

* 查看根目录下的`_config.yml`文件中url的值， 必须包含是百度站长平台注册的域名

  > ```yml
  ># URL
  > url: http://mfln8n.coding-pages.com/
  > root: /
  > ```
  
* 加入新的deployer

  > ```yml
  > deploy:
  >  - type: git
  >    repo: git@github.com:xxxxx
  >    branch: master
  >    # 加入新的type
  >  - type: baidu_url_submitter ## 添加这里内容即可
  > ```

#### 4. 结果：已解决

---

### 四、Gitalk评论系统 Error：Network Error （未解决）

#### 1. Error：Network Error

* 评论区显示Error : Network Error，用户无法登陆
* 登录时会自动跳转回博客主页

#### 2. 分析

* 参考

  > [Gitalk评论初始化无法登陆](https://www.cnblogs.com/liuurick/p/10713693.html)
  >
  > [Gitalk评论初始化登录时跳转回首页](https://blog.csdn.net/w47_csdn/article/details/88858343)

* OAuth APP的`callback URL`设置导致的

#### 3. 解决办法

* 通过查看登录时的网页路径请求，来重新设置callback URL

* 解决方法：在Github创建OAuth APP时：

  * 如果博客只部署在一个云端，无论是Github Pages、Gitee Pages、Coding Pages还是个人搭建的云服务器，回调地址callback URL都选择博客主页

  * 当博客双线部署在多个云端时，会出现Error : Network Error 错误，此时回调地址callback URL要与_config.yml配置文件中的 config.url 一致，不然会无法登陆。

    > ```yml
    > callback URL: http://mfln8n.coding-pages.com
    > ```

#### 4. 结果：未解决

* 后续会继续跟进

