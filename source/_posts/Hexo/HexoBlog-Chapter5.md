---
title: Hexo博客搭建系列Chapter5——Hexo博客功能扩展(1/5)
toc: true
reward: true
declare: true
gitalkenable: true
abbrlink: 95d7a133
date: 2020-07-31 16:27:15
categories: 
- others
- Hexo
top:
tags: 
- Hexo
---

---

* 在Windows系统上利用Hexo搭建博客系列——Chapter5
* Chapter5：
  * 具备Yilia主题自带原始功能
  * 扩展了微信分享功能、文章置顶功能、文章分类功能
  * 扩展了404默认公益网页、头像旋转、文章默认格式功能

<!-- more -->

# Hexo博客搭建系列——Chapter 5

---

## 五、高级功能实现

* 各功能教程超链接汇总
  * [微信分享](http://dongshuyan.com/2019/05/24/hexo%E5%8D%9A%E5%AE%A2%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9/)
  * [版权信息](https://blog.csdn.net/qq_37939980/article/details/104390332)
  * [不蒜子统计](https://blog.csdn.net/qq_40922859/article/details/97035497?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param)
  * [爱心功能、评论系统](https://blog.csdn.net/qq_40922859/article/details/97035497?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param)
  * [音乐功能、404页面、头像旋转](https://blog.csdn.net/weixin_41287260/article/details/97693850)
  * [分类、随笔、标签](https://github.com/iissnan/hexo-theme-next/wiki/%E5%88%9B%E5%BB%BA%E5%88%86%E7%B1%BB%E9%A1%B5%E9%9D%A2)
  * [字数统计、阅读时长](https://blog.csdn.net/weixin_41287260/article/details/97693850)
  * [live2D、转载说明](http://dongshuyan.com/2019/05/24/hexo%E5%8D%9A%E5%AE%A2%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9/)
  * [文章加密](https://blog.csdn.net/qq_40922859/article/details/97035497?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param)
  * [更多功能](http://dongshuyan.com/2019/05/24/hexo%E5%8D%9A%E5%AE%A2%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9/)

### 1. 微信分享功能

* Yilia主题的默认微信分享
  * 初始时微信分享功能失效
  * 解决方法：修改`themes\yilia\layout\_partial\post\share.ejs`文件

* 由于百度云已经不支持二维码生成功能，修改`share.ejs`的度盘链接，改为`qrserver.com`的api

  > ```html
  > <!--查找-->
  > //pan.baidu.com/share/qrcode?url=
  > 
  > <!-- 修改为-->
  > //api.qrserver.com/v1/create-qr-code/?size=150x150&data=
  > ```

* 查找`share.ejs`中的

  > ```html
  > <!--查找-->
  > var sUrl = url.replace(/index\.html$/, '');
  > sUrl = /^(http:|https:)\/\//.test(sUrl) ? sUrl : 'http:' + sUrl;
  > 
  > <!--添加-->
  > test=sUrl.substring(29);
  > <!-- 注意：substring截取的位数与你的主页网址长度相等>
  >   <!--注意：如果博客只部署在一个服务端，可以省略这一步，微信二维码分享的地方，直接使用sUrl>
  >   <!--注意：如果博客只部署在多个服务端，不可省略，使得二维码为主博客的网址>
  >   ```
  
* 再将`share.ejs`中的

  > ```html
  > <!--查找-->
  > '//api.qrserver.com/v1/create-qr-code/?size=150x150&data=' + sUrl
  > 
  > <!-- 修改为-->
  > '//api.qrserver.com/v1/create-qr-code/?size=150x150&data=你的主页网址' + test
  > ```

* 最终结果

  > ```html
  > <!-- 最终修改结果-->
  > <div class="page-modal wx-share js-wx-box">
  >  <a class="close js-modal-close" href="javascript:;"><i class="icon icon-close"></i></a>
  >  <p>扫一扫，分享到微信</p>
  >  <div class="wx-qrcode">
  >    <img src="<%- 'qrcode' in locals ? qrcode(sUrl) : '//api.qrserver.com/v1/create-qr-code/?size=150x150&data=https://githubName.github.io' + test  %>" alt="微信分享二维码">
  >  </div>
  > </div>
  > ```

---

### 2. 文章置顶功能

#### 2.1 安装插件

* 安装`hexo-generator-index-pin-top`模块

  > ```bash
  > npm uninstall hexo-generator-index --save
  > npm install hexo-generator-index-pin-top --save
  > ```

#### 2.2 配置置顶标准

* 打开`/themes/yilia/layout/post.ejs`在最前面加入代码

  > ```ejs
  > <% if (page.top) { %>
  > <i class="fa fa-thumb-tack"></i>
  > <font color=7D26CD>置顶</font>
  > <span class="post-meta-divider">|</span>
  > <% } %>
  > ```

#### 2.3 配置文章

* 在需要置顶的文章的`Front-matter`中加上`top`属性

  > top后面的数字越大，优先级越高
  >
  > ```yml
  > ---
  > title: 博客简介
  > date: xxx
  > top: 5
  > ---
  > ```

#### 2.4 优先级配置

* 修改根目录配置文件`_config.yml`，`top`值-1标示根据top值倒序（正序设置为1即可），同样date也是根据创建日期倒序。

  > ```yml
  > # 查找
  > index_generator:
  > path: ''
  > per_page: 10
  > order_by: -date
  > 
  > # 改为
  > index_generator:
  > path: ''
  > per_page: 10
  > order_by:
  >  top: -1
  >  date: -1
  > ```

---

### 3. 文章标签分类功能

#### 3.1 文章分类功能

* 通过hexo新建一个页面，命名为`categories`

  > ```bash
  > $ hexo new page categoties
  > ```

* 编辑刚新建的页面，将页面的类型`type`设置为`categories`，主题将自动为这个页面显示所有分类。

  > 通过设置categoties，并在menu中添加对应链接来实现博文分类，categoties是不同级的，适合纵向文件分类    
  >
  > ```yml
  >title: 分类
  > date: 2020-07-29 10:36:40
  > type: "categories"
  > comments: false
  > ```
  
* 菜单中添加链接。编辑主题的`_config.yml` ，将categories添加到menu中

  > ```yml
  > menu:
  > home: /
  > categories: /categories
  > archives: /archives
  > tags: /tags
  > ```

#### 3.2 文章标签分类功能

* MarkDown 各种标签设置

  > 通过设置tags，并在menu中添加对应链接来实现博文分类，tags标签是同级的，适合横向文件分类 
  >
  > ```yml
  >---
  > title: 				 #文章标题
  > subtitle:            #子标题
  > categories:          #文章分類目錄 可以省略    
  > toc: true            #目录 需要安装插件或者主题自带
  > reward: true         #打赏 需要安装插件或者主题自带
  > declare: true        #版权 需要安装插件或者主题自带
  > date:                #文章日期
  > header-img:          #顶部背景图片
  > tags:                #标签
  > ---
  > ```


---

### 4. 添加404网页

* 在`/source/`目录下新建`404.md`在头部添加属性`permalink: /404.html`，页面内容可以自定义。

  * 推荐使用[腾讯公益404页面](https://www.qq.com/404/)（注意要把首页链接换成自己的）
  * 参考：[自定义Github Pages](https://docs.github.com/cn/github/working-with-github-pages/creating-a-custom-404-page-for-your-github-pages-site)
  
  * `404.md`文件内容
  
    > ```html
    > ---
    > # example 404.md 
    > title: 404 Not Found：该页无法显示 
    > date: 2019-07-26 21:51:29 
    > permalink: /404.html
    > ---
    > 
    > <!DOCTYPE html>
    > <html>
    > <head>
    >   <meta charset="UTF-8" />
    >   <title>404</title>                              
    > </head>    
    > <body>
    >   <script type="text/javascript" src="//qzonestyle.gtimg.cn/qzone/hybrid/app/404/search_children.js" homePageName="返回首页" homePageUrl="https://chthollists.github.io/"></script> 
    > </body>
    > </html>
    > ```

---

### 5. 头像旋转功能

* 第一步：在`themes\yilia\source\`文件夹下增加一个css文件`avatarrotation.css`用来旋转360度

  > ```css
  > /* avatarrotation.css文件的内容 */
  > 
  > .left-col #header .profilepic img {
  > 	/* 控制旋转速度时间*/
  > -webkit-transition: -webkit-transform 1.0s ease-out;
  > -moz-transition: -moz-transform 1.0s ease-out;
  > transition: transform 1.0s ease-out;
  > }
  > .left-col #header .profilepic img:hover {
  > 	/* 鼠标经过360% */
  > -webkit-transform: rotateZ(360deg);
  > -moz-transform: rotateZ(360deg);
  > transform: rotateZ(360deg);
  > }
  > ```

* 第二步：在`themes\yilia\layout\_partial\head.ejs`文件中添加进去创建的`css`文件：
  
  * 找到`<%- partial('css') %>`，在它的下面添加代码，把刚才写的文件添加进去
  
  * 注意：是在它的下面添加，不然旋转速度将不会生效
  
    > ```html
    > <!-- 示例 -->
    > <% if (theme.avatarrotation){ %>
    > 	<link rel="stylesheet" type="text/css" href="/./avatarrotation.css">
    > <% } %>
    > ```
  
* 第三步：在`theme\_config.yml`中添加

  > ```yml
  > #头像是否旋转(如果不要旋转取false)
  > avatarrotation: true
  > ```

---

### 6. 修改默认文章格式

* 修改根目录下的`scaffolds`文件夹的`post.md`，或修改hexo new "article" 的Front-matter的属性

  > ```yml
  > title: {{ title }}
  > date: {{ date }}
  > subtitle:                         #子标题
  > categories:                       #文章分类 可以省略    
  > toc: true                         #目录 需要安装插件或者主题自带
  > reward: true                      #打赏 需要安装插件或者主题自带
  > declare: true                     #版权 需要安装插件或者主题自带
  > header-img:                       #顶部背景图片
  > top: 
  > password: 
  > abstract: 
  > message: 
  > tags:                             #标签
  > ```

---

