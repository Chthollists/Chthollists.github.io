---
title: Hexo博客搭建系列Chapter9——Hexo博客功能扩展(5/5)
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
  - Valine
  - Leancloud
  - Gitalk
  - Gitment
  - Live2D
  - hexo-helper-live2d
abbrlink: 64db259f
date: 2020-07-31 20:11:33
---

---

* 在Windows系统上利用Hexo搭建博客系列——Chapter9

* Chapter9：

* 扩展了评论系统功能——均支持Markdown语法

  * 基于Valine和Leancloud的评论系统

    > 详细教程：[Valine评论系统](https://deserts.io/diy-a-comment-system/)
    >
    > 优点：不会出现各种奇怪的错误
    >
    > 缺点：没有实现真实登录功能，可以用户自己随意取昵称评论

  * 基于Gitment和Gitalk的评论系统

    > 详细教程：[Gitalk评论系统](https://www.jianshu.com/p/57afa4844aaa)
    >
    > 优点：基于Github登录
    >
    > 缺点：Error和Bug较多，Error：Network Error 问题没能解决

* 扩展了Live2D功能

  * 更多模型参见：[Live2D 模型](https://github.com/dongshuyan/live2dDemo)
  * 说明文档：[Live2D-widget.js.org](https://l2dwidget.js.org/docs/class/src/index.js~L2Dwidget.html#instance-method-init)
  * [高级Live2D](https://github.com/stevenjoezhang/live2d-widget)：主要在Next主题下，支持变装、小游戏、更换角色等功能

* 更多功能展望(莫须有)

<!-- more -->

# Hexo博客搭建系列——Chapter 9

---

## 五、高级功能实现

### 16. 评论系统

#### 16.1 注册Leancloud

* [官网](https://www.leancloud.cn/)

* [注册页面](https://leancloud.cn/dashboard/login.html#/signup)

* [创建应用](https://leancloud.cn/dashboard/applist.html#/newapp)：Hexo Blog Comment

* 获取Key

  > 选择刚刚创建的应用>设置>选择应用 Keys
  >
  > 获取APP ID和APP KEY了
  >
  > ```yml
  > APP ID : IbB069kzjoJ6yrtHz3F47kAh-gzGzoHsz
  > APP Key : seteWK1qk6nSC5zBcB2TFfWi
  > ```

* 设置Web安全域名

  > 设置的安全中心中设置
  >
  > ```yml
  > http://mfln8n.coding-pages.com
  > https://chthollists.github.io
  > ```

#### 16.2 修改配置文件

* 修改`theme/yilia/layout/_partial/article.ejs`

  > 在最末尾加上
  >
  > ```html
  > <% if ((theme.valine_type === 2 || (theme.valine_type === 1 && post.valineenbale)) && !index){ %>
  > <% if (theme.valine && theme.valine.appid && theme.valine.appkey){ %>
  >    <section id="comments" class="comments">
  >      <style>
  >        .comments{margin:30px;padding:10px;background:#fff}
  >        @media screen and (max-width:800px){.comments{margin:auto;padding:10px;background:#fff}}
  >      </style>
  >      <%- partial('post/valine', {
  >        key: post.slug,
  >        title: post.title,
  >        url: config.url+url_for(post.path)
  >        }) %>
  >  </section>
  > <% } %>
  > <% } %>
  > ```

* 修改`theme/yilia/_config.yml`

  > 添加
  >
  > ```yml
  > #6、Valine https://valine.js.org
  > valine_type: 1 #0 关闭评论  2全开评论  1需要再文章md开头加入valineenbale:true
  > valine: 
  > enable: true
  > appid:  你在注册时候得到的id
  > appkey:  你在注册时候得到的key
  > #这里的appid和appkey如果配置失败可以试试改成app_id和app_key
  > verify: true #验证码
  > notify: true #评论回复提醒
  > avatar:  identicon   #评论列表头像样式：''/mm/identicon/monsterid/wavatar/retro/hide
  > avatar_cdn: '' #头像CDN
  > #评论框占位符
  > placeholder: ヾﾉ≧∀≦)o '友情提醒，留下正确的邮箱地址可以第一时间获取评论反馈'
  > guest_info: nick,mail,link # custom comment header
  > pageSize: 10 # pagination size
  > visitor: true
  > ```

* 修改CDN加速：新建`theme/yilia/layout/_partial/post/valine.ejs`

  > 官方的CDN是
  >
  > ```yml
  > //unpkg.com/valine/dist/Valine.min.js
  > ```

  > 新建`valine.ejs`,内容
  >
  > ```html
  > <div id="vcomment"></div>
  > <script src="//cdn.jsdelivr.net/npm/jquery/dist/jquery.min.js"></script>
  > <script src="//cdn.jsdelivr.net/npm/leancloud-storage/dist/av-min.js"></script>
  > <script src='//cdn.jsdelivr.net/npm/valine/dist/Valine.min.js'></script>
  > <script>
  > var notify = '<%= theme.valine.notify %>' == true ? true : false;
  > var verify = '<%= theme.valine.verify %>' == true ? true : false;
  > new Valine({
  >          av: AV,
  >          el: '#vcomment',
  >          notify: notify,
  >          verify: verify,
  >          app_id: "<%= theme.valine.appid %>",
  >          app_key: "<%= theme.valine.appkey %>",
  >          placeholder: "<%= theme.valine.placeholder %>",
  >          avatar: "<%= theme.valine.avatar %>",
  >          avatar_cdn: "<%= theme.valine.avatar_cdn %>",
  >          pageSize: "<%= theme.valine.pageSize %>"
  >  });
  > </script>
  > ```

* 如果你的valine_type: 也是1，那么然后就需要在你文章抬头加上

  > ```yml
  > valineenbale:true
  > ```

* 注意：由于不明原因没有登录功能，所以后续准备改用Gitment/Gitalk评论系统。

#### 16.3 Gitalk评论系统

* 注册(已有Github可以跳过注册)

* Gitment

* [创建APP应用](https://github.com/settings/applications/new)

  > ```yml
  > # 创建APP的相关设置
  > # APP名称---无限制
  > Application name : HexoBlogComment
  > # APP主页URL---无限制
  > Homepage URL : github.com/chthollists
  > # APP 描述介绍---无限制
  > description : Gitalk Comment APP 
  > # 回调地址---重要：取博客主页，要与_config.yml配置文件中的 config.url 一致
  > callback URL : http://mfln8n.coding-pages.com
  > ```

* 获取Client ID和Client Secret

  > ```yml
  > Client ID : ef41cd3f6bb938f5b4c6
  > Client Secret : 0a387b3802665d8a71757a618f93dfd8cb5ec7d8
  > ```
  
* 新建Gitalk文件

  > 新建ejs文件`layout/_partial/post/gitalk.ejs`
  >
  > ```html
  > <div id="gitalk-container" style="padding: 0px 30px 0px 30px;"></div> 
  > 
  > <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.css">
  > <script src="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.min.js"></script>
  > <script type="text/javascript">
  > 
  > if(<%=theme.gitalk.enable%>){
  > var gitalk = new Gitalk({
  > clientID: '<%=theme.gitalk.ClientID%>',
  > clientSecret: '<%=theme.gitalk.ClientSecret%>',
  > repo: '<%=theme.gitalk.repo%>',
  > owner: '<%=theme.gitalk.githubID%>',
  > admin: ['<%=theme.gitalk.adminUser%>'],
  > id: '<%= page.date %>',
  > distractionFreeMode: '<%=theme.gitalk.distractionFreeMode%>'
  > pageSize: "<%= theme.gitalk.pageSize %>"
  > })
  > gitalk.render('gitalk-container') 
  > }
  > </script>
  > ```

* 修改文件comment.scss

  > 完全替换`source-src/css/comment.scss`文件 
  >
  > ```css
  > disqus_thread, .duoshuo, .cloud-tie-wrapper, #SOHUCS, #gitment-ctn, #gitalk-container {
  > padding: 0 30px !important;
  > min-height: 20px;
  > }
  > 
  > #SOHUCS {
  > #SOHU_MAIN .module-cmt-list .block-cont-gw {
  >  border-bottom: 1px dashed #c8c8c8 !important;
  > }
  > }
  > ```

* 修改`layout/_partial/article.ejs`

  > 文件内新增gitalk相关的配置代码：
  >
  > ```html
  > <% if ((theme.gitalk_type === 2 || (theme.gitalk_type === 1 && post.gitalkenbale)) && !index){ %>
  > <% if(theme.gitalk.enable){ %>
  > <%- partial('post/gitalk', {
  > key: post.slug,
  > title: post.title,
  > url: config.url+url_for(post.path)
  > }) %>
  > <% } %>
  > <% } %>
  > ```

* 最后修改`theme/yilia/_config.yml`

  > ```yml
  > gitalk_type:  1 #0 关闭评论  2全开评论  1需要再文章md开头加入gitalkenbale: true
  > gitalk:
  > enable: true
  > githubID: github帐号     
  > repo: 仓库名称 ( 用来存放评论的仓库名 )   例如：blog-comments
  > ClientID: Client ID
  > ClientSecret: Client Secret
  > adminUser: github帐号   #指定可初始化评论账户
  > distractionFreeMode: true
  > ```
  >
  > ```yml
  > gitalk:
  > enable:  true
  > githubID:  'Chthollists'   
  > repo:  'chthollists.github.io' #这里可以另开一个也可以就用你博客的那个repo
  > ClientID:  'xxxxxx'
  > ClientSecret:  'xxxxxxxxxxxxxxxxxxxxxxxxx'
  > adminUser:  'Chthollists' 
  > distractionFreeMode: true
  > ```

* 注意：在Github创建OAuth APP时：

  * 如果博客只部署在一个云端，无论是Github Pages、Gitee Pages、Coding Pages还是个人搭建的云服务器，回调地址callback URL都选择博客主页

  * 而当博客双线部署在多个云端时，会出现Error : Network Error 错误，此时回调地址callback URL要与_config.yml配置文件中的 config.url 一致，不然会无法登陆。

    > ```yml
    > callback URL: http://mfln8n.coding-pages.com
    > ```

  * 参考：

    > [Gitalk评论初始化无法登陆](https://www.cnblogs.com/liuurick/p/10713693.html)
    >
    > [Gitalk评论初始化登录时跳转回首页](https://blog.csdn.net/w47_csdn/article/details/88858343)

---

### 17. Live 2D功能

* Github 上的Live2D资源：[Live2D 模型](https://github.com/dongshuyan/live2dDemo)
* [参考链接1](https://blog.csdn.net/Aoman_Hao/article/details/82049821)、[参考链接2](https://qianlei6148.github.io/2018/10/11/hexo模版yilia添加可爱的看板娘)、[参考链接3](https://zhousiwei.gitee.io/2019/05/05/Hexo%E5%8D%9A%E5%AE%A2%E6%B7%BB%E5%8A%A0helper-live2d%E5%8A%A8%E6%80%81%E6%A8%A1%E5%9E%8B%E6%8F%92%E4%BB%B6/)

#### 17.1  下载插件和模型

* 安装插件 [hexo-helper-live2d](https://github.com/EYHN/hexo-helper-live2d)

  > ```bash
  > npm install --save hexo-helper-live2d
  > ```

* 安装2D模型：[Live2D 模型](https://github.com/dongshuyan/live2dDemo)

  > ```bash
  > npm install live2d-widget-model-模型名称
  > ```

#### 17.2 更改模型路径

* 在博客根目录下新建目录 live2d_models
* 从目录node_modules中拷贝模型文件，放到live2d_models中

#### 17.3 添加配置

* 说明文档：[Live2D-widget.js.org](https://l2dwidget.js.org/docs/class/src/index.js~L2Dwidget.html#instance-method-init)

* 修改根目录下的`_config.yml`配置文件

  > 配置文件示例
  >
  > ```yml
  > # Live2D
  > ## https://github.com/EYHN/hexo-helper-live2d
  > ## https://l2dwidget.js.org/docs/class/src/index.js~L2Dwidget.html#instance-method-init
  > live2d:
  > enable: true
  > #enable: false
  > scriptFrom: local # 默认
  > pluginRootPath: live2dw/ # 插件在站点上的根目录(相对路径)
  > pluginJsPath: lib/ # 脚本文件相对与插件根目录路径
  > pluginModelPath: assets/ # 模型文件相对与插件根目录路径
  > # scriptFrom: jsdelivr # jsdelivr CDN
  > # scriptFrom: unpkg # unpkg CDN
  > # scriptFrom: https://cdn.jsdelivr.net/npm/live2d-widget@3.x/lib/L2Dwidget.min.js # 你的自定义 url
  > tagMode: false # 标签模式, 是否仅替换 live2d tag标签而非插入到所有页面中
  > debug: false # 调试, 是否在控制台输出日志
  > model:
  >  use:  # 模型名
  >  # use: live2d-widget-model-wanko # npm-module package name
  >  # use: wanko # 博客根目录/live2d_models/ 下的目录名
  >  # use: ./wives/wanko # 相对于博客根目录的路径
  >  # use: https://cdn.jsdelivr.net/npm/live2d-widget-model-wanko@1.0.5/assets/wanko.model.json # 你的自定义 url
  > display:
  >  position: right
  >  width: 145
  >  height: 315
  > mobile:
  >  show: true # 是否在移动设备上显示
  >  scale: 0.5 # 移动设备上的缩放       
  > react:
  >  opacityDefault: 0.7
  >  opacityOnHover: 0.8
  > ```

---

### 18. 其他功能

> 后续逐步完善

#### 18.1 图床

[七牛云图床](https://cloudy-liu.github.io/2018/04/07/Hexo_yilia_%E4%B8%BB%E9%A2%98%E4%B8%80%E6%8F%BD%E5%AD%90%E4%BC%98%E5%8C%96%E6%96%B9%E6%A1%88/)

---

#### 18.2 RSS 

[RSS](https://blog.csdn.net/qq_40922859/article/details/97035497?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param)

---

#### 18.3 一言API个性签名自动刷新

[一言API个性签名自动刷新](https://blog.csdn.net/qq_40922859/article/details/97035497?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param)

---

#### 18.4 Google收录

[站点地图](http://dongshuyan.com/2019/05/24/hexo%E5%8D%9A%E5%AE%A2%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9/#4-%E6%80%8E%E4%B9%88%E7%BD%AE%E9%A1%B6%E6%96%87%E7%AB%A0)

---

#### 18.5 Google广告投放 

[Google广告](http://dongshuyan.com/2019/05/24/hexo%E5%8D%9A%E5%AE%A2%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9/#4-%E6%80%8E%E4%B9%88%E7%BD%AE%E9%A1%B6%E6%96%87%E7%AB%A0)

---

---

## 附录：参考资料

* 参考资料：

  > https://cloudy-liu.github.io/2018/04/07/Hexo_yilia
  >
  > https://blog.csdn.net/weixin_41287260/article/details/97693850
  >
  > https://blog.csdn.net/qq_37939980/article/details/104390332
  >
  > http://dongshuyan.com/2019/05/24/hexo
  >
  > https://github.com/dongshuyan/live2dDemo

* 视频教程

  > https://www.bilibili.com/video/BV1pt411K7o9?p=8
  >
  > https://www.bilibili.com/video/BV1Yb411a7ty/?spm_id_from=333.788.videocard.2

  