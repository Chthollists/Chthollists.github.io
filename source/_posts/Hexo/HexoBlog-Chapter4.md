---
title: Hexo博客搭建系列Chapter4——Hexo博客美化
toc: true
reward: true
declare: true
gitalkenable: true
abbrlink: 139f76f2
date: 2020-07-31 15:49:00
categories: 
- others
- Hexo
top:
tags: 
- Hexo
- Yilia
- toc
---

---

* 在Windows系统上利用Hexo搭建博客系列——Chapter4
* Chapter4：
  * 更换Yilia主题
  * 配置图片和链接资源
  * 增加摘要、目录、归档
  * 美化目录、字体、背景
  * 增加文章总数(底部)、用户信息、个性签名

  <!-- more -->

# Hexo博客搭建系列——Chapter 4

---

## 四、博客设置美化

* 各功能教程超链接汇总
  * [MarkDown语法简介](http://dongshuyan.com/2016/04/07/%E6%80%8E%E4%B9%88%E5%86%99%E5%8D%9A%E5%AE%A2/)
  * [主题更换]([http://dongshuyan.com/2016/04/07/%E6%80%8E%E4%B9%88%E5%86%99%E5%8D%9A%E5%AE%A2/](http://dongshuyan.com/2016/04/07/怎么写博客/))
  * [配置图片和链接](https://cloudy-liu.github.io/2018/04/07/Hexo_yilia_%E4%B8%BB%E9%A2%98%E4%B8%80%E6%8F%BD%E5%AD%90%E4%BC%98%E5%8C%96%E6%96%B9%E6%A1%88/#%E7%99%BE%E5%BA%A6-Google%E7%BB%9F%E8%AE%A1-SEO)
  * [文章摘要、目录、归档、字体](https://blog.csdn.net/qq_37939980/article/details/104390332)
  * [美化目录](https://blog.csdn.net/qq_40922859/article/details/97035497?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param)
  * [文章总数统计、主页美化](https://blog.csdn.net/qq_37939980/article/details/104390332)

### 1. 主题更换 ：Yilia

* 下载开源的主题，存放在`HexoBlog/themes`目录下

  > 选择Github上很多人Star的[Yilia主题](https://github.com/litten/hexo-theme-yilia.git themes/yilia)，此外，还有[Yilia-plus主题](https://zhousiwei.gitee.io/2019/07/25/hexo%E4%B8%BB%E9%A2%98%E4%B9%8Bhexo-theme-yilia-plus/)、Next主题等。
  >
  > ```bash
  ># 在HexoBlog目录下Git bash
  > git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
  > ```
  
* 修改`_config.yml`文件

  > ```yml
  > theme : landscape
  > # 改为
  > theme : yilia
  > ```

* 清理hexo并重新生成页面，部署到Github上

  > ```bash
  > # 清理
  > hexo clean
  > 
  > # 生成
  > hexo g
  > hexo generate
  > 
  > # 本地预览
  > hexo s
  > 
  > # 部署
  > hexo d
  > ```

---

### 2. 配置图片资源和链接资源

* 添加图片资源文件夹

  > 路径为 `themes/yilia/source/`下，可添加一个 `assets` 文件夹，里面存放图片资源即可。

* 配置文件中直接引用

  > 路径为 `themes/yilia/_config.yml`，添加WeChat、Email、Github等作为博主的联系方式，添加个人头像、网页favicon。
  >
  > ```yml
  > # 微信二维码图片
  > weixin:  /assets/wechat.jpg
  > # 头像图片
  > avatar:  /assets/avatar.jpg
  > # 网页图标
  > favicon:  /assets/favicon.png
  > ```

* 链接资源——配置文件中直接引用链接地址

  > 路径为 `themes/yilia/_config.yml`，如：Github、Facebook、Twitter、Bilibili等。
  >
  > ```yml
  >github: "https://github.com/Chthollists"
  > bilibili: "https://space.bilibili.com/2030799"
  > ```
  
* 注意：`_config.yml`的语句对空格、缩进要求严格

---

### 3. 博客文章美化设置

#### 3.1 显示文章摘要

* 在MD格式文章正文，一般在标题后插入 `<!-- more -->`

  > 同时需要注释掉 `themes/yilia/_config.yml`内的`excerpt_link: more`项。
  >
  > ```yml
  > # 文章太长，截断按钮文字
  > # excerpt_link: more
  > ```

#### 3.2 显示文章目录TOC

* TOC(table of content) 在 `themes/yilia/_config.yml`中进行配置

  > ```yml
  > # 目录设定：
  > # 0-不显示目录； 
  > # 1-文章对应的md文件里有toc:true属性，才有目录； 
  > # 2-所有文章均显示目录
  > toc: 2
  > ```

#### 3.3 增加归档菜单

* 修改 `themes/yilia/_config.yml`配置

  > ```yml
  > menu:
  >  主页:  /
  >  归档:  /archives/index.html
  > ```

#### 3.4 美化目录

* 原始目录

  > 通过`toc = 2`生成的蓝底目录不够美观，通过修改css和ejs文件来自制滑动白色目录，注意：此时yilia主题的原目录无法显示。

* 修改css文件

  > `themes\yilia\source\`路径下找到`main.xxxxx.css`，在最后添加代码
  >
  > ```css
  > #container .show-toc-btn,
  > #container .toc-article{display:block}
  > 
  > .toc-article{
  >     z-index:100;
  >     background:#fff;
  >     border:1px solid #ccc;
  >     max-width:250px;
  >     min-width:150px;
  >     max-height:500px;
  >     overflow-y:auto;
  >     -webkit-box-shadow:5px 5px 2px #ccc;
  >     box-shadow:5px 5px 2px #ccc;
  >     font-size:12px;
  >     padding:10px;
  >     position:fixed;
  >     right:35px;
  >     top:129px
  > }
  > 
  > .toc-article .toc-close{
  >     font-weight:700;
  >     font-size:20px;
  >     cursor:pointer;
  >     float:right;color:#ccc
  > }
  > 
  > .toc-article .toc-close:hover{
  >     color:#000
  > }
  > 
  > .toc-article .toc{
  >     font-size:12px;
  >     padding:0;
  >     line-height:20px
  > }
  > 
  > .toc-article .toc .toc-number{
  >     color:#333
  > }
  > 
  > .toc-article .toc .toc-text:hover{
  >     text-decoration:underline;
  >     color:#2a6496
  > }
  > 
  > .toc-article li{
  >     list-style-type:none
  > }
  > 
  > .toc-article .toc-level-1{
  >     margin:4px 0
  > }
  > 
  > .toc-article .toc-child{}
  > 
  > @-moz-keyframes cd-bounce-1{
  >     0%{
  >         opacity:0;
  >         -o-transform:scale(1);
  >         -webkit-transform:scale(1);
  >         -moz-transform:scale(1);
  >         -ms-transform:scale(1);
  >         transform:scale(1)
  >     }
  >     60%{
  >         opacity:1;
  >         -o-transform:scale(1.01);
  >         -webkit-transform:scale(1.01);
  >         -moz-transform:scale(1.01);
  >         -ms-transform:scale(1.01);
  >         transform:scale(1.01)
  >     }
  >     100%{
  >         -o-transform:scale(1);
  >         -webkit-transform:scale(1);
  >         -moz-transform:scale(1);
  >         -ms-transform:scale(1);
  >         transform:scale(1)}
  > }
  > 
  > @-webkit-keyframes cd-bounce-1{
  >     0%{
  >         opacity:0;
  >         -o-transform:scale(1);
  >         -webkit-transform:scale(1);
  >         -moz-transform:scale(1);
  >         -ms-transform:scale(1);
  >         transform:scale(1)
  >     }
  >     60%{
  >         opacity:1;
  >         -o-transform:scale(1.01);
  >         -webkit-transform:scale(1.01);
  >         -moz-transform:scale(1.01);
  >         -ms-transform:scale(1.01);
  >         transform:scale(1.01)
  >     }
  >     100%{
  >         -o-transform:scale(1);
  >         -webkit-transform:scale(1);
  >         -moz-transform:scale(1);
  >         -ms-transform:scale(1);
  >         transform:scale(1)}
  > }
  > 
  > @-o-keyframes cd-bounce-1{
  >     0%{
  >         opacity:0;
  >         -o-transform:scale(1);
  >         -webkit-transform:scale(1);
  >         -moz-transform:scale(1);
  >         -ms-transform:scale(1);
  >         transform:scale(1)
  >     }
  >     60%{
  >         opacity:1;
  >         -o-transform:scale(1.01);
  >         -webkit-transform:scale(1.01);
  >         -moz-transform:scale(1.01);
  >         -ms-transform:scale(1.01);
  >         transform:scale(1.01)
  >     }
  >     100%{
  >         -o-transform:scale(1);
  >         -webkit-transform:scale(1);
  >         -moz-transform:scale(1);
  >         -ms-transform:scale(1);
  >         transform:scale(1)}
  > }
  > 
  > @keyframes cd-bounce-1{
  >     0%{
  >         opacity:0;
  >         -o-transform:scale(1);
  >         -webkit-transform:scale(1);
  >         -moz-transform:scale(1);
  >         -ms-transform:scale(1);
  >         transform:scale(1)
  >     }
  >     60%{
  >         opacity:1;
  >         -o-transform:scale(1.01);
  >         -webkit-transform:scale(1.01);
  >         -moz-transform:scale(1.01);
  >         -ms-transform:scale(1.01);
  >         transform:scale(1.01)
  >     }
  >     100%{
  >         -o-transform:scale(1);
  >         -webkit-transform:scale(1);
  >         -moz-transform:scale(1);
  >         -ms-transform:scale(1);
  >         transform:scale(1)}
  > }
  > 
  > .show-toc-btn{
  >     display:none;
  >     z-index:10;
  >     width:30px;
  >     min-height:14px;
  >     overflow:hidden;
  >     padding:4px 6px 8px 5px;
  >     border:1px solid #ddd;
  >     border-right:none;
  >     position:fixed;
  >     right:40px;
  >     text-align:center;
  >     background-color:#f9f9f9
  > }
  > 
  > .show-toc-btn .btn-bg{
  >     margintop:2px;
  >     display:block;
  >     width:16px;
  >     height:14px;
  >     background:url(https://7xtawy.com1.z0.glb.clouddn.com/show.png) no-repeat;
  >     -webkit-background-size:100%;
  >     -moz-background-size:100%;
  >     background-size:100%
  > }
  > 
  > .show-toc-btn .btn-text{
  >     color:#999;
  >     font-size:12px
  > }
  > 
  > .show-toc-btn:hover{
  >     cursor:pointer
  > }
  > 
  > .show-toc-btn:hover .btn-bg{
  >     background-position:0 -16px
  > }
  > 
  > .show-toc-btn:hover .btn-text{
  >     font-size:12px;
  >     color:#ea8010
  > }
  > 
  > .toc-article li ol, .toc-article li ul {
  >     margin-left: 30px;
  > }
  > .toc-article ol, .toc-article ul {
  >     margin: 10px 0;
  > }
  > ```

* 修改ejs文件：

  > 找到`themes\yilia\layout\_partial\article.ejs`，在 `</header> <% } %>`下面加入
  >
  > ```html
  > <!-- 目录内容 -->
  > <% if (!index && post.toc){ %>
  >  <p class="show-toc-btn" id="show-toc-btn" onclick="showToc();" style="display:none">
  >        <span class="btn-bg"></span>
  >        <span class="btn-text">文章导航</span>
  >        </p>
  > 	<div id="toc-article" class="toc-article">
  > 	    <span id="toc-close" class="toc-close" title="隐藏导航" onclick="showBtn();">×</span>
  > 		<strong class="toc-title">文章目录</strong>
  >         <%- toc(post.content) %>
  >       </div>
  > <script type="text/javascript">
  > 	function showToc(){
  > 		var toc_article = document.getElementById("toc-article");
  > 		var show_toc_btn = document.getElementById("show-toc-btn");
  > 		toc_article.setAttribute("style","display:block");
  > 		show_toc_btn.setAttribute("style","display:none");
  > 		};
  > 	function showBtn(){
  > 		var toc_article = document.getElementById("toc-article");
  > 		var show_toc_btn = document.getElementById("show-toc-btn");
  > 		toc_article.setAttribute("style","display:none");
  > 		show_toc_btn.setAttribute("style","display:block");
  > 		};
  > </script>
  >    <% } %>
  > <!-- 目录内容结束 -->
  > ```

* 注意：此时MD文件，即文章开头需要设置toc属性——`toc: true `

  > ```yml
  > ---
  > title: 				 #文章标题
  > categories:          #文章分类路径，可以省略    
  > toc: true            #是否显示目录，需要安装插件或者主题自带
  > date:                #文章日期
  > tags:                #标签
  > ---
  > ```

#### 3.5 字体美化

* 正文字体美化

  > ```html
  > <font color="purple" face="Microsoft YaHei" size= 5><b>content内容 </b></font>
  > ```
  >
  > 修改文件路径为`theme\yilia\source\main.0cf68a.css`
  >
  > ```css
  > # font-family:字体
  > font-family : Times New Roman
  > 
  > # .article-entry .highlight .line(color:#FFF----默认白色
  > .article-entry .highlight .line(color : 十六进制颜色
  > ```

---

### 4. 博客主页美化

#### 4.1 修改博客标题、子标题、用户

* 修改文件路径为博客根路径下的`_config.yml`

  > ```yml
  > # Site
  > title: Chthollists的平凡之路
  > subtitle: '今日物语'
  > description: ''
  > keywords: xxx
  > author: 'xxx'
  > # 设置为英文 en
  > language: Zh-cn
  > timezone: ''
  > ```

#### 4.2 主页左侧显示文章总数

* 在`themes\yilia\layout\_partial\left-col.ejs`文件

  > ```html
  > # 查找
  > <nav class="header-menu">
  >  <ul>
  >  <% for (var i in theme.menu){ %>
  >      <li><a href="<%- url_for(theme.menu[i]) %>"><%= i %></a></li>
  >  <%}%>
  >  </ul>
  > </nav>
  > 
  > # 后面添加
  > <nav>
  >  总文章数 <%=site.posts.length%>
  > </nav>
  > ```

#### 4.3 设置个性签名

* 在 `themes/yilia/_config.yml`文件中，修改aboutme项

  > ```html
  > aboutme: <font color="purple" face="Microsoft YaHei" size= 5><b>content内容<br> </b></font>
  > ```

---

