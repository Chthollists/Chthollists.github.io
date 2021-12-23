---
title: Hexo博客搭建系列Chapter8——Hexo博客功能扩展(4/5)
toc: true
reward: true
declare: true
gitalkenable: true
categories: 
- others
- Hexo
top: 1
tags:
  - Hexo
  - Coding Pages
  - SEO
  - SiteMap
abbrlink: f8e513cc
date: 2020-07-31 20:11:06
---

---

* 在Windows系统上利用Hexo搭建博客系列——Chapter8

* Chapter8：

  * 扩展了SEO优化功能：[百度搜索资源平台](https://ziyuan.baidu.com/)

    * 文章URL加密
    * 站点地图SiteMap生成与提交
    * 蜘蛛协议Robots

  * 扩展了百度推送、收录功能

    * 由于Github禁止百度爬虫，所以需要把Hexo博客双线部署到`CodingPages`上，从而实现百度收录功能

    * 主动推送：`hexo-baidu-url-submit --save`
    * 自动推送

  * 扩展了百度统计功能：[百度统计官网](https://tongji.baidu.com)

    * 准入秘钥token
    * `baidu-analytics`

<!-- more -->

# Hexo博客搭建系列——Chapter 8

---

## 五、高级功能实现

### 13. SEO优化

* SEO(Search Engine Optimization)搜索引擎优化

  > 搜索引擎优化是一种利用搜索引擎的搜索规则来提高目的网站在有关搜索引擎内的排名的方式。

  * 针对个人网站的页面排版进行SEO的优化
  * 针对搜索引擎进行主动的优化

#### 13.1 加密文章URL

* 文章加密
  * 博客默认的地址 `网址/年/月/日/文章名`
  * 默认格式不便于搜索引擎抓取，需要进行优化
  * 注意：修改之后旧文章的统计数据(浏览量)会重新计数。

* 第一步：安装插件 `hexo-abbrlink `

  > ```bash
  > npm install hexo-abbrlink --save
  > ```

* 第二步：修改根目录配置文件`_config.yml`

  > ```yml
  > # 查找
  > permalink: :year/:month/:day/:title/ 
  > # 修改
  > permalink: post/:abbrlink.html
  > # 并在下面添加加密算法
  > abbrlink:
  > alg: crc32  # 算法
  > rep: hex    # 进制
  > ```
  >

#### 13.2 添加站点地图SiteMap

* 第一步：安装插件：`hexo-generator-sitemap `

* 第二步：安装插件：`hexo-generator-baidu-sitemap `

  > ```bash
  > npm install hexo-generator-sitemap --save          # 默认的
  > npm install hexo-generator-baidu-sitemap --save    # 百度专属
  > ```

* 重新清理、渲染，`pulic`文件夹下会生成一个`sitmap.xml`文件

  > ```bash
  >$ hexo clean
  > $ hexo g
  > ```
  
* 注意：sitemap 的初衷是给搜索引擎看的，为了提高搜索引擎对自己站点的收录效果，可以：

  * 手动到 Google 和百度等搜索引擎提交 `sitemap.xml`
  * 添加蜘蛛协议 `robots`，新建`robots.txt`文件，添加以下文件内容，把`robots.txt`放在hexo站点的source文件下

#### 13.3 注册百度站长平台

* 在百度站长平台添加网站

  > 在[百度搜索资源平台](https://ziyuan.baidu.com/)→用户中心→站点管理→添加网站，添加需要管理的网站地址。
  
* 验证网站：三种方法——本文选用html标签验证

  * 文件验证

    * 将百度验证文件放在网站首页下，即：博客根目录`/source`下
    * 但是hexo在渲染生成时，会修改`html`文件，导致验证失败
    
* `html`标签验证
  
  * 将`html`验证代码(如：`RdypQbGwZW`)添加到网站首页HTML代码的`<head>`标签之间
  
    * 在根目录`/themes/yourtheme/layout/_partial/head.ejs `中添加如下代码
  
      > ```html
      > <meta name="baidu-site-verification" content="RdypQbGwZW" />
      > ```
  
* `CNAME`方式
  
  > 将`RdypQbGwZW.yoursite`使用CNAME解析到`ziyuan.baidu.com`。

#### 13.4 针对百度搜索引擎的优化

* 提交SiteMap站点地图文件

  * 转到普通收录，选择自己的站点网址。

  * 自动提交，选择sitemap。按照提示的格式添加自己的sitemap文件

    > ```http
    > https://chthollists.github.io/sitemap.xml 
    > https://chthollists.github.io/baidusitemap.xml
    > ```

* 添加**蜘蛛协议**：`robots`

  > 新建`robots.txt`文件，添加以下文件内容，把`robots.txt`放在hexo站点的`source`文件下，可以在[百度搜索资源平台](https://ziyuan.baidu.com/)验证文件是否生效。
  >
  > ```yml
  > # hexo robots.txt
  > User-agent: * Allow: /
  > Allow: /archives/
  > Disallow: /vendors/
  > Disallow: /js/
  > Disallow: /css/
  > Disallow: /fonts/
  > Disallow: /vendors/
  > Disallow: /fancybox/
  > 
  > Sitemap: https://chthollists.github.io/sitemap.xml
  > Sitemap: https://chthollists.github.io/baidusitemap.xml
  > ```
  >

#### 13.5 网站结构自身优化

* keywords 和 description

  > 在`\scaffolds\post.md`中添加如下代码，用于生成的文章中添加关键字和描述。
  >
  > ```yml
  > keywords: #文章关键词，多个关键词用英文逗号隔开
  > description: #文章描述，便于搜索引擎用关键词检索
  > ```

* 文章地址优化：参见13.1 加密URL


---

### 14. 百度推送收录功能

* 除了通过提交SiteMap文件推送网站的地址外，还有自动提交：主动推送、自动推送，手动提交方式。详情参考[Hexo实现百度收录](https://zhuanlan.zhihu.com/p/100922816)
* 由于Github Pages上禁止百度爬虫，所以需要把Hexo博客部署到双线服务器上，选择Coding Pages，详情参考[Hexo双线部署CodingPages](https://blog.csdn.net/qq_36759224/article/details/100879609)
* [Hexo博客+Coding Pages](http://mfln8n.coding-pages.com/)

#### 14.1 新增博客文章页面自动推送

* 修改文件：`<博客根目录>/themes/yilia/layout/_partial/article.ejs`

  > ```html
  > <!-- 查找 -->
  > <%
  >   var sUrl = url.replace(/index\.html$/, '');
  >   sUrl = /^(http:|https:)\/\//.test(sUrl) ? sUrl : 'https:' + sUrl;
  > %>
  >     
  > <!-- 在前面添加 -->
  > <!-- 百度自动推送方式提交 -->
  > <% if (1){ %>
  > <script>
  > (function(){
  > var bp = document.createElement('script');
  > var curProtocol = window.location.protocol.split(':')[0];
  > if (curProtocol === 'https') {
  >     bp.src = 'https://zz.bdstatic.com/linksubmit/push.js';
  > }
  > else {
  >     bp.src = 'http://push.zhanzhang.baidu.com/push.js';
  > }
  > var s = document.getElementsByTagName("script")[0];
  > s.parentNode.insertBefore(bp, s);
  > })();
  > </script>
  > <% } %>
  > <!-- 百度自动推送方式提交结束 -->
  > ```
  >

* 注意：只会主自动推送新增博客文章页面，不包括其他页面。

#### 14.2 主动推送

* 主动推送原理
  * 主动推送是最为快速的提交方式，是被百度收录最快的推送方式
  * 新链接的产生， hexo generate 会产生一个文本文件，里面包含最新的链接
  * 新链接的提交， hexo deploy 会从上述文件中读取链接，提交至百度搜索引擎

* 第一步：安装插件：`hexo-baidu-url-submit`

  > ```bash
  > npm install hexo-baidu-url-submit --save
  > ```

* 第二步：修改博客根目录配置文件`_config.yml`，添加配置

  > ```yml
  > # 添加
  > baidu_url_submit:
  > count: 20 ## 提交最新的20个链接
  > host: http://mfln8n.coding-pages.com/ ## 百度站长平台中注册的域名
  > token:  ## 16位准入秘钥
  > path: baidu_urls.txt ## 文本文档的地址， 新链接会保存在此文本文档里
  > ```

* 第三步：获取准入秘钥

  > 在[百度搜索资源平台](https://ziyuan.baidu.com/)的平台收录中获取准入秘钥token ---16位

* 第四步：查看根目录下的`_config.yml`文件中url的值， 必须包含是百度站长平台注册的域名，如：

  > ```yml
  ># URL
  > url: http://mfln8n.coding-pages.com/
  > root: /
  > ```
  
* 第五步：加入新的`deployer`

  > ```yml
  > deploy:
  >  - type: git
  >    repo: git@github.com:xxxxx
  >    branch: master
  >    # 加入新的type
  >  - type: baidu_url_submitter ## 添加这里内容即可
  > ```

#### 14.3 自动推送

* 安装自动推送JS代码的网页，在页面被访问时，页面URL将立即被推送给百度

* 修改主题目录下的`layout/post.ejs`文件

  > 末尾添加自动推送代码：
  >
  > ```html
  > <script>
  > (function(){
  >  var bp = document.createElement('script');
  >  var curProtocol = window.location.protocol.split(':')[0];
  >  if (curProtocol === 'https') {
  >      bp.src = 'https://zz.bdstatic.com/linksubmit/push.js';
  >  }
  >  else {
  >      bp.src = 'http://push.zhanzhang.baidu.com/push.js';
  >  }
  >  var s = document.getElementsByTagName("script")[0];
  >  s.parentNode.insertBefore(bp, s);
  > })();
  > </script>
  > ```

---

### 15. 百度统计功能

* 第一步：在[百度统计官网](https://tongji.baidu.com)上添加网站，获取统计代码

  > ```html
  > <script>
  > var _hmt = _hmt || [];
  > (function() {
  > var hm = document.createElement("script");
  > hm.src = "https://hm.baidu.com/hm.js?0113964bae57c4e8dcc0a90d835680c4";
  > var s = document.getElementsByTagName("script")[0]; 
  > s.parentNode.insertBefore(hm, s);
  > })();
  > </script>
  > ```

* 第二步：添加统计代码到Hexo站点

  * 方式一：在文件`themes/yilia/layout/_partial/baidu-analytics.ejs`，中添加统计代码

  * 方式二：新建文件`themes/yilia/layout/baidu-tongji.ejs`

    > ```html
    > <% if (theme.baidu_tongji) { %>
    > //这里添加统计代码
    > <% } %>
    > ```

* 第三步：修改主题的配置文件`themes/yilia/_config.yml`

  > ```yml
  > baidu_analytics: ''
  > # 改为
  > baidu_analytics: true
  > 
  > # 或者
  > # 新建属性
  > baidu_tongji: true
  > ```

* 注意：如果是新建的`baidu-tongji.ejs`文件

  > 还需要在`themes\yilia\layout\_partial\head.ejs`，在`</head>`上面添加：
  >
  > ```html
  > <%- partial("baidu_tongji") %>
  > ```

* 第四步：可以通过百度统计的[代码安装检查](https://tongji.baidu.com/sc-web/10000247748/home/js/check?siteId=15660763)功能来验证百度统计功能是否添加成功。

---

