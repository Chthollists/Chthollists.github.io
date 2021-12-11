---
title: Hexo博客搭建全家桶——不定期更新
categories: 
- others
- Hexo
declare: true
gitalkenable: true
toc: true
top: 5
tags:
  - Hexo
  - Git
  - Node.js
  - SEO
  - SiteMap
  - Gitalk
  - Live2D
abbrlink: 686a3de0
date: 2020-07-28 20:18:58
---

---

* 利用Hexo搭建的一个Yilia主题静态博客，并对博客进行了美化和功能扩展。
* 主要功能：文章加密、文章置顶、背景音乐、点击爱心、百度统计、不蒜子统计、SEO优化、百度收录、版权信息、Gitalk评论系统、Live2D

![Chtholly](http://qfpro7zhq.hd-bkt.clouddn.com/Chtholly06.jpg)

<!-- more -->

# Hexo博客搭建全家桶——不定期更新

---

## 一、博客分类及搭建工具

> 目前，主流的博客主要分静态博客和动态博客两类：
>
> 所谓动态，就是指有前端有后端，可以登录到后台进行管理。静态博客是纯前端的展示，没有后端。

### 1. 动态博客 Dynamic Blog

* 简书、CSDN、知乎、博客园、掘金等动态博客公共平台上搭建博客

  * 优点：简单
  * 缺点：受到平台的限制

* WordPress框架

  > 使用WordPress等成熟框架搭建动态博客 + 服务器部署
  >
  > [WordPress官网](https://wordpress.org/)

  * 基于PHP

  * 优点：相对简单
  * 缺点：笨重

* 前后端开发自行搭建

  > 使用 Java、PHP、Python等语言开发属于自己的动态博客 + 服务器部署

  * 优点：功能强大
  * 缺点：难度大

---

### 2. 静态博客 Static blog

#### 2.1 静态博客框架

> 轻量级、快速

* Hexo

  * [Hexo官网](https://hexo.io/)

  * 基于 JavaScript
  * Node.js
  
* Jekyllrb

  * [Jekyllrb官网](https://jekyllrb.com/)
  
  * 配置环境：Ruby、RubyGems、Node.js
* Hugo

  * [Hugo官网](https://www.gohugo.org/)
  
  * 基于Go
* VuePress

  * [VuePres官网](https://vuepress.vuejs.org/zh/guide/)
  
  * 基于Vue.js
  
  * 配置环境： [Vue](http://vuejs.org/)、[Vue Router](https://github.com/vuejs/vue-router) 、[webpack](http://webpack.js.org/)
* Solo

  * [Solo官网](https://solo.b3log.org/)
* 基于Java
  * 配置环境：JDK
  

#### 2.2 服务器

* 托管在GitHub、Coding、Gitee码云等公共平台

  * 优点：免费、简便
  * 缺点：访问速度慢、Github限制百度爬虫，不方便实现SEO优化

* 自己搭建云服务器

  > [Hexo+阿里云教程](https://mp.weixin.qq.com/s?sn=8009155566a3f5cd3e46b4052a047f73&idx=1&mid=2247483706&lang=zh_CN&__biz=MzUzMTA2Njk3Mw%3D%3D&chksm=fa497808cd3ef11ea8ba66a60bb131716b8278fcd1bf3105cf67c988b367d110089af453736c&token=55710950&bili_only=0#rd)

  * 优点：访问速度快、独立域名、自主可控
  * 缺点：域名和云服务器均付费

---

***

## 二、Git与Github

* 教程

  > [Git教程——廖雪峰](https://www.liaoxuefeng.com/)
  >
  > [Git教程](https://blog.csdn.net/qq_39387475/article/details/84996173)

### 1. Git安装与配置

* Git 分布式版本控制系统原理

  > 版本控制系统，其实只能跟踪文本文件的改动，比如TXT文件，网页，所有的程序代码等等，无法追踪图片、音频、视频等的改动。

  - 工作区(Working Directory)：就是电脑本地看到的目录。

  - 暂存区：英文叫stage, 或index。

    > 一般存放在 ".git目录下" 下的index文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。

  - 版本库(Repository)：工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。

* 安装包下载

  > [Git官网](https://git-scm.com/downloads)

* 在Git bash中配置本地用户名及邮箱

  > 如果用了 --global 选项，那么更改的配置文件就是位于你用户主目录下的那个，以后你所有的项目都会默认使用这里配置的用户信息。
  >
  > 如果要在某个特定的项目中使用其他名字或者电邮，只要去掉 --global 选项重新配置即可，新的设定保存在当前项目的 .git/config 文件里。

  > ```bash
  > # 查看配置
  > git config --list ：
  > # 设置全局用户名
  > git config --global user.name "your name"   
  > # 设置全局邮箱
  > git config --global user.email "your email" 
  > # 查看本目录下仓库的用户名
  >  git config user.name
  > # 修改本目录下仓库的用户名
  > git config user.name "用户名"
  > # 查看本目录下仓库的邮箱 ；
  > git config user.email 
  > # 修改本目录下仓库的邮箱 ； 
  > git config user.email "邮箱"   
  > ```

* 编辑、删除全局配置

  > ```bash
  > # 删除全局配置，终端执行命令
  > git config --global --unset user.name
  > 
  > # 编辑配置文件
  > git config --global --edit
  > ```

---

### 2. Git创建仓库及管理

> 所有命令均在Git bash 命令行中完成 $

* 创建仓库

  * 在路径下创建空目录

    > 可以修改新建路径，默认C/Users/you
    >
    > ```bash
    > mkdir newrepo
    > cd newrepo
    > ```

  * 在新建的目录下完成初始化

    > 在目录下会出现一个名为 .git 的目录，所有 Git 需要的数据和资源都存放在这个目录中
    >
    > ```bash
    > git init
    > # 或者直接把创建目录和初始化一步完成
    > git init newrepo
    > ```

* 本地仓库管理(文件版本管理)

  > 所有要管理的文件必须放在仓库中

  * 添加文件

    > ```bash
    > # 把文件从工作区Working Directory添加到缓存index
    > #  git add 文件名
    > # 文件名有空格时，需要写在引号中
    > git add CodeAlgorithm.md
    > # 把全部文件添加到缓存区
    > git add --all
    > 
    > # 把文件从缓存区index提交到Git仓库中master
    > # 可以一次提交多个文件，即多次add后再commit
    > # git commit -m "提交说明"
    > git commit -m "test"
    > # 跳过添加成缓存步骤，直接添加到仓库中  -a
    > git commit -a
    > ```
* 查看改动
  
    > ```bash
    > # 查看缓存区index文件
    > # A 表示缓存区
    > # AM 表示该文件添加进缓存区后被改动过，此时需要再次add
    > # ??表示不再缓存区
    > git status -s
    > 
    > # 查看具体改动
    > # 查看执行 git status 的结果的详细信息
    > # 尚未缓存的改动
    > git diff
    > # 查看已缓存的改动
    > git diff --cached
    > # 查看已缓存的与未缓存的所有改动
    > git diff HEAD
    > # 显示摘要而非整个 diff
    > git diff --stat
    > ```
  * 查看历史记录

    > ```bash
    > # 详细历史日志
    > git log
    > # 单行简易输出
    > git log --pretty=oneline
    > ```
  * 文件版本回溯
  
  > 在Git中，用`HEAD`表示当前版本，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，往上100个版本简写成`HEAD~100`。
    >
    > Git在内部有个指向当前版本的`HEAD`指针，回溯版本实际上是改变指针的对象。
    >
    > ```bash
    > # 版本回溯 --旧版
    > # git reset 版本(HEAD表示法、或者log下显示的版本号---输入前几位即可自动匹配)
    > git reset --hard HEAD^
    > git reset --hard xxxxxx
    > 
    > # 版本回溯 --新版
    > # 从master恢复暂存区
    > git resotre --worktree File
    > # 从暂存区恢复工作区
    > git restore --staged 
    > # 从master同时恢复工作区和暂存区
    > git restore --source=HEAD --staged --worktree 文件名
    > 
    > # 查看当前记录、版本号
    > git log
    > # 查看之前指令的相关记录---可以看到版本号
    > git reflog
    > ```
  * 文件撤销修改
  
    > 把文件在工作区的修改全部撤销，这里有两种情况：

    * 一种是文件自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
* 一种是文件已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
    * 总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。
    
    > ```bash
    > # 旧版
    > git checkout -- 文件名
    > 
    > # 新版
    > git restore 文件名
    > ```
  * 文件删除与恢复
  
    > 工作区的文件被删除后，在删除版本库中的文件
    >
  > ```bash
    > git rm  文件名
    > git commit -m "说明remove "
    > ```
    >
    > 工作区的文佳误删后，通过版本库恢复文件
    >
    > ```bash
    > rm '文件名'
    > git restore 文件名
    > ```
  
  * 创建、切换、删除分支
  
    > ```bash
    > # 默认主分支master
    > # 创建一个叫做"feature_x"的分支，并切换过去：-b
    > git checkout -b feature_x
    > 
    > # 切换回主分支：
    > git checkout master
    > 
    > # 再把新建的分支删掉：-d
    > git branch -d feature_x
    > 
    > # 除非你将分支推送到远端仓库，不然该分支就是 不为他人所见的：
    > git push origin <branch>
    > ```

---

### 3. Git远程仓库

> 将文件push到远程仓库的流程
>
> ```bash
> # 在目标路径下Git bash
> git init
> git add -all
> git commit -m 'readme'
> git remote add origin git@server-name:path/repo-name.git
> # git remote -v
> git push -u origin master
> ```

* 创建SSH Key

  > ```bash
  > # 会在用户主目录下，生成.ssh目录，里面有id_rsa私钥、id_ras.pub公钥文件
  > ssh-keygen -t rsa -C "youremail@example.com"
  > ```
  >
  > youremail@example.com使用注册Github、Gitee时的邮箱

* 登陆Github，Add SSH Key

  > 打开“Account settings”，“SSH Keys”页面，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的内容
  >
  > ```bash
  > # 验证是否成功
  > ssh -T git@github.com
  > ```

* 在Github上创建新的库并关联remote，将本地库推送到远程库push

  > ```bash
  > # 关联远程库
  > git remote add origin git@server-name:path/repo-name.git
  > git remote add origin(远程库名，默认origin) git@github.com:Github用户名/远程库名
  > git remote add origin git@github.com:Amaduess/newrepo.git
  > # 删除已有的关联origin
  > git remote rm origin
  > # 查看现有的关联
  >  git remote -v
  >  
  > # 推送push
  > # 第一次push -u 将本地master与远程master关联
  > git push -u origin master
  > # 之后push
  > git push origin master
  > ```
  >
  
* 从远程库克隆到本地库

  > ```bash
  > git clone git@server-name:path/repo-name.git
  > git clone git@github.com:Github用户名/远程库名
  > ```

* 克隆后可以更新本地库pull

  > ```bash
  > # 在当前工作目录中 获取（fetch） 并 合并（merge） 远端的改动
  > git pull
  > ```

* 其他

  > ```bash
  > # 内建的图形化 git：
  > gitk
  > 
  > # 创建标签
  > git tag 1.0.0  ID(前十位字符)
  > 
  > # 彩色的 git 输出：
  > git config color.ui true
  > 
  > # 显示历史记录时，每个提交的信息只显示一行：
  > git config format.pretty oneline
  > 
  > # 交互式添加文件到暂存区：
  > git add -i
  > ```

---

***

## 三、利用Hexo搭建博客

> [Hexo博客搭建教程](http://dongshuyan.com/2016/04/07/%E6%80%8E%E4%B9%88%E5%86%99%E5%8D%9A%E5%AE%A2/)

### 1. 准备工作 Nodejs & Git

* 配置Node.js

  > [Nodejs官网](https://nodejs.org/en/)

* 配置Git

  > [Git官网](https://git-scm.com/downloads)

* 检测安装结果

  > ```bash
  > # 查看node、npm、git版本
  > node -v
  > npm -v
  > git --version
  > ```

* 通过npm安装淘宝镜像源cnmp

  > ```bash
  > # 安装cnpm
  > npm install -g cnpm --registry=https://registry.npm.taobao.org
  > 
  > # 查看cnpm版本
  > cnpm --version
  > ```

---

### 2. Hexo安装及配置

* 安装Hexo

  > [Hexo官网](https://hexo.io/)
  >
  > ```bash
  > # 安装
  > cnpm install -g hexo-cli
  > # 查看版本
  > hexo -v
  > ```

* Hexo初始化

  > 创建目录，并在此目录下进行后续操作
  >
  > ```bash
  > # 创建目录
  > mkdir HexoBlog
  > cd HexoBlog
  > 
  > # 初始化hexo
  > hexo init
  > 
  > # 查看初始化的文件
  > ls -al
  > 
  > # ------------------或者--------------------------
  > hexo init HexoBlog
  > ```

* Hexo启动

  > ```bash
  > # 启动Hexo 搭建初始博客
  > hexo s
  > # 或者
  > hexo server
  > ```
  >
  >  此时博客已经初步搭建完成，本地访问地址：http://localhost:4000
  >
  > 当Git bash上停止服务器后，无法访问

* Hexo基本命令

  > ```bash
  > npm update hexo -g  #升级 
  > hexo init <folder>  #执行init命令初始化hexo到你指定的目录
  > hexo n "我的博客" == hexo new "我的博客" #新建文章
  > hexo generate       #自动根据当前目录下文件,生成静态网页 hexo g
  > hexo server         #运行本地服务 hexo s
  > hexo clean          #清理
  > hexo deploy         #部署 hexo d
  > 
  > hexo server         #Hexo会监视文件变动并自动更新，无须重启服务器
  > hexo server -s      #静态模式
  > hexo server -p 5000 #更改端口
  > hexo server -i 192.168.1.1 #自定义 IP
  > ```

---

### 3. 博客部署到远端服务器

* 使用Github作为远端服务器

  > 在Github上新建库，库名为：用户名.github.io
  >
  > ssh：git@github.com:Amaduess/Amaduess.github.io.git

* 利用cnpm安装一个hexo-deployer-git插件

  > ```bash
  > # 安装插件
  > cnpm install --save hexo-deployer-git
  > 
  > # 打开_config.yml文件，修改内容
  > vim _config.yml
  > # 原内容
  > deploy:
  > 	type : ' '
  > # 改为
  > deploy:
  > 	type : git
  > 	# 服务器地址(仓库地址)
  > 	repo : git@github.com:Amaduess/Amaduess.github.io.git 
  > 	# 分支，设置为管理员
  > 	branch : master
  > ```

* 将博客部署到Github上

  > ```bash
  > hexo d
  > hexo deploy
  > ```

---

***

## 四、博客设置美化

* 各功能教程超链接汇总
  * [MarkDown语法简介](http://dongshuyan.com/2016/04/07/%E6%80%8E%E4%B9%88%E5%86%99%E5%8D%9A%E5%AE%A2/)
  * [主题更换]([http://dongshuyan.com/2016/04/07/%E6%80%8E%E4%B9%88%E5%86%99%E5%8D%9A%E5%AE%A2/](http://dongshuyan.com/2016/04/07/怎么写博客/))
  * [配置图片和链接](https://cloudy-liu.github.io/2018/04/07/Hexo_yilia_%E4%B8%BB%E9%A2%98%E4%B8%80%E6%8F%BD%E5%AD%90%E4%BC%98%E5%8C%96%E6%96%B9%E6%A1%88/#%E7%99%BE%E5%BA%A6-Google%E7%BB%9F%E8%AE%A1-SEO)
  * [文章摘要、目录、归档、字体](https://blog.csdn.net/qq_37939980/article/details/104390332)
  * [美化目录](https://blog.csdn.net/qq_40922859/article/details/97035497?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param)
  * [文章总数统计、主页美化](https://blog.csdn.net/qq_37939980/article/details/104390332)

### 1. 主题更换 ：Yilia

* 下载开源的主题，存放在HexoBlog/theme目录下

  > 选择Github上很多人star的[Yilia主题](https://github.com/litten/hexo-theme-yilia.git themes/yilia)
  >
  > 此外，还有[Yilia-plus主题](https://zhousiwei.gitee.io/2019/07/25/hexo%E4%B8%BB%E9%A2%98%E4%B9%8Bhexo-theme-yilia-plus/)、Next主题等
  >
  > ```bash
  > # 在HexoBlog目录下Git bash
  > git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
  > ```

* 修改_config.yml文件

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

> 添加WeChat、Email、Github等作为博主的联系方式
>
> 添加个人头像、网页favicon

* 添加图片资源文件夹

  > 路径为 `themes/yilia/source/`下，可添加一个 `assets` 文件夹，里面存放图片资源即可

* 配置文件中直接引用

  > 路径为 `themes/yilia/_config.yml`
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

  > 路径为 `themes/yilia/_config.yml`
  >
  > 如：Github、Facebook、Twitter、Bilibili等
  >
  > ```yml
  > github: "https://github.com/Chthollists"
  > bilibili: "https://space.bilibili.com/2030799"
  > ```

* 注意：_config.yml的语句对空格、缩进要求严格

---

### 3. 博客文章美化设置

#### 3.1 显示文章摘要

* 在你MD 格式文章正文,一般在标题后插入 `<!-- more -->`

  > 同时注释掉 `themes/yilia/_config.yml`内的
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
  >     主页:  /
  >     归档:  /archives/index.html
  > ```

#### 3.4 美化目录

> 通过toc = 2生成的蓝底目录不够美观，通过修改css和ejs文件来自制滑动白色目录
>
> 注意：此时yilia主题的原目录无法显示。

* 修改css文件

  > themes\yilia\source\路径下找到`main.xxxxx.css`，在最后添加代码
  >
  > ```css
  > #container .show-toc-btn,
  > #container .toc-article{display:block}
  > 
  > .toc-article{
  >  z-index:100;
  >  background:#fff;
  >  border:1px solid #ccc;
  >  max-width:250px;
  >  min-width:150px;
  >  max-height:500px;
  >  overflow-y:auto;
  >  -webkit-box-shadow:5px 5px 2px #ccc;
  >  box-shadow:5px 5px 2px #ccc;
  >  font-size:12px;
  >  padding:10px;
  >  position:fixed;
  >  right:35px;
  >  top:129px
  > }
  > 
  > .toc-article .toc-close{
  >  font-weight:700;
  >  font-size:20px;
  >  cursor:pointer;
  >  float:right;color:#ccc
  > }
  > 
  > .toc-article .toc-close:hover{
  >  color:#000
  > }
  > 
  > .toc-article .toc{
  >  font-size:12px;
  >  padding:0;
  >  line-height:20px
  > }
  > 
  > .toc-article .toc .toc-number{
  >  color:#333
  > }
  > 
  > .toc-article .toc .toc-text:hover{
  >  text-decoration:underline;
  >  color:#2a6496
  > }
  > 
  > .toc-article li{
  >  list-style-type:none
  > }
  > 
  > .toc-article .toc-level-1{
  >  margin:4px 0
  > }
  > 
  > .toc-article .toc-child{}
  > 
  > @-moz-keyframes cd-bounce-1{
  >  0%{
  >      opacity:0;
  >      -o-transform:scale(1);
  >      -webkit-transform:scale(1);
  >      -moz-transform:scale(1);
  >      -ms-transform:scale(1);
  >      transform:scale(1)
  >  }
  >  60%{
  >      opacity:1;
  >      -o-transform:scale(1.01);
  >      -webkit-transform:scale(1.01);
  >      -moz-transform:scale(1.01);
  >      -ms-transform:scale(1.01);
  >      transform:scale(1.01)
  >  }
  >  100%{
  >      -o-transform:scale(1);
  >      -webkit-transform:scale(1);
  >      -moz-transform:scale(1);
  >      -ms-transform:scale(1);
  >      transform:scale(1)}
  > }
  > 
  > @-webkit-keyframes cd-bounce-1{
  >  0%{
  >      opacity:0;
  >      -o-transform:scale(1);
  >      -webkit-transform:scale(1);
  >      -moz-transform:scale(1);
  >      -ms-transform:scale(1);
  >      transform:scale(1)
  >  }
  >  60%{
  >      opacity:1;
  >      -o-transform:scale(1.01);
  >      -webkit-transform:scale(1.01);
  >      -moz-transform:scale(1.01);
  >      -ms-transform:scale(1.01);
  >      transform:scale(1.01)
  >  }
  >  100%{
  >      -o-transform:scale(1);
  >      -webkit-transform:scale(1);
  >      -moz-transform:scale(1);
  >      -ms-transform:scale(1);
  >      transform:scale(1)}
  > }
  > 
  > @-o-keyframes cd-bounce-1{
  >  0%{
  >      opacity:0;
  >      -o-transform:scale(1);
  >      -webkit-transform:scale(1);
  >      -moz-transform:scale(1);
  >      -ms-transform:scale(1);
  >      transform:scale(1)
  >  }
  >  60%{
  >      opacity:1;
  >      -o-transform:scale(1.01);
  >      -webkit-transform:scale(1.01);
  >      -moz-transform:scale(1.01);
  >      -ms-transform:scale(1.01);
  >      transform:scale(1.01)
  >  }
  >  100%{
  >      -o-transform:scale(1);
  >      -webkit-transform:scale(1);
  >      -moz-transform:scale(1);
  >      -ms-transform:scale(1);
  >      transform:scale(1)}
  > }
  > 
  > @keyframes cd-bounce-1{
  >  0%{
  >      opacity:0;
  >      -o-transform:scale(1);
  >      -webkit-transform:scale(1);
  >      -moz-transform:scale(1);
  >      -ms-transform:scale(1);
  >      transform:scale(1)
  >  }
  >  60%{
  >      opacity:1;
  >      -o-transform:scale(1.01);
  >      -webkit-transform:scale(1.01);
  >      -moz-transform:scale(1.01);
  >      -ms-transform:scale(1.01);
  >      transform:scale(1.01)
  >  }
  >  100%{
  >      -o-transform:scale(1);
  >      -webkit-transform:scale(1);
  >      -moz-transform:scale(1);
  >      -ms-transform:scale(1);
  >      transform:scale(1)}
  > }
  > 
  > .show-toc-btn{
  >  display:none;
  >  z-index:10;
  >  width:30px;
  >  min-height:14px;
  >  overflow:hidden;
  >  padding:4px 6px 8px 5px;
  >  border:1px solid #ddd;
  >  border-right:none;
  >  position:fixed;
  >  right:40px;
  >  text-align:center;
  >  background-color:#f9f9f9
  > }
  > 
  > .show-toc-btn .btn-bg{
  >  margintop:2px;
  >  display:block;
  >  width:16px;
  >  height:14px;
  >  background:url(https://7xtawy.com1.z0.glb.clouddn.com/show.png) no-repeat;
  >  -webkit-background-size:100%;
  >  -moz-background-size:100%;
  >  background-size:100%
  > }
  > 
  > .show-toc-btn .btn-text{
  >  color:#999;
  >  font-size:12px
  > }
  > 
  > .show-toc-btn:hover{
  >  cursor:pointer
  > }
  > 
  > .show-toc-btn:hover .btn-bg{
  >  background-position:0 -16px
  > }
  > 
  > .show-toc-btn:hover .btn-text{
  >  font-size:12px;
  >  color:#ea8010
  > }
  > 
  > .toc-article li ol, .toc-article li ul {
  >  margin-left: 30px;
  > }
  > .toc-article ol, .toc-article ul {
  >  margin: 10px 0;
  > }
  > ```

* 修改ejs文件：

  > 找到`themes\yilia\layout\_partial\article.ejs`,在 `</header> <% } %>`下面加入
  >
  > ```html
  > <!-- 目录内容 -->
  > <% if (!index && post.toc){ %>
  >     <p class="show-toc-btn" id="show-toc-btn" onclick="showToc();" style="display:none">
  >           <span class="btn-bg"></span>
  >           <span class="btn-text">文章导航</span>
  >           </p>
  > 	<div id="toc-article" class="toc-article">
  > 	    <span id="toc-close" class="toc-close" title="隐藏导航" onclick="showBtn();">×</span>
  > 		<strong class="toc-title">文章目录</strong>
  >            <%- toc(post.content) %>
  >          </div>
  >    <script type="text/javascript">
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
  >    </script>
  >       <% } %>
  > <!-- 目录内容结束 -->
  > ```

* 注意：此时MD文件，即文章开头需要设置toc属性——`toc: true `

  > ```yml
  > ---
  > title: 				 #文章标题
  > categories:                       #文章分類目錄 可以省略    
  > toc: true                            #目录 需要安装插件或者主题自带
  > date:                                   #文章日期
  > tags:                                    #标签
  > ---
  > ```

#### 3.5 字体美化

* 代码字体美化、颜色设置

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
  >     <ul>
  >     <% for (var i in theme.menu){ %>
  >         <li><a href="<%- url_for(theme.menu[i]) %>"><%= i %></a></li>
  >     <%}%>
  >     </ul>
  > </nav>
  > 
  > # 后面添加
  > <nav>
  >     总文章数 <%=site.posts.length%>
  > </nav>
  > ```

#### 4.3 设置个性签名

* 在 `themes/yilia/_config.yml`文件中，修改aboutme项

  > ```html
  > aboutme: <font color="purple" face="Microsoft YaHei" size= 5><b>content内容<br> </b></font>
  > ```

---

***

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

> 初始时微信分享功能有问题
>
> 解决方法：修改`themes\yilia\layout\_partial\post\share.ejs`文件
>

* 由于百度云已经不支持二维码生成功能，修改share.ejs的度盘链接，改为qrserver.com的api

  > ```html
  > # 查找
  > //pan.baidu.com/share/qrcode?url=
  > 
  > # 修改为
  > //api.qrserver.com/v1/create-qr-code/?size=150x150&data=
  > ```

* 查找share.ejs中的

  > ```html
  > # 查找
  > var sUrl = url.replace(/index\.html$/, '');
  >   sUrl = /^(http:|https:)\/\//.test(sUrl) ? sUrl : 'http:' + sUrl;
  >   
  > # 后面添加
  > # 20与博客地址的长度相关
  > test=sUrl.substring(20);
  > ```

* 再将share.ejs中的

  > ```html
  > # 查找
  > '//api.qrserver.com/v1/create-qr-code/?size=150x150&data=' + sUrl
  > 
  > # 改为
  > '//api.qrserver.com/v1/create-qr-code/?size=150x150&data=你的主页网址' + test
  > ```

* 最终结果

  > ```html
  > # 修改后代码
  > <div class="page-modal wx-share js-wx-box">
  >     <a class="close js-modal-close" href="javascript:;"><i class="icon icon-close"></i></a>
  >     <p>扫一扫，分享到微信</p>
  >     <div class="wx-qrcode">
  >       <img src="<%- 'qrcode' in locals ? qrcode(sUrl) : '//api.qrserver.com/v1/create-qr-code/?size=150x150&data=https://damonxiang.github.io' + test  %>" alt="微信分享二维码">
  >     </div>
  > </div>
  > ```

---

### 2. 文章置顶功能

#### 2.1 安装插件

* 安装 hexo-generator-index-pin-top 模块

  > ```bash
  > npm uninstall hexo-generator-index --save
  > npm install hexo-generator-index-pin-top --save
  > ```

#### 2.2 配置置顶标准

* 打开`/themes/yilia/layout/post.ejs`在最前面加入代码

  > ```html
  > <% if (page.top) { %>
  >   <i class="fa fa-thumb-tack"></i>
  >   <font color=7D26CD>置顶</font>
  >   <span class="post-meta-divider">|</span>
  > <% } %>
  > ```

#### 2.3 配置文章

* 在需要置顶的文章的Front-matter中加上top属性

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

* 修改根目录配置文件/_config.yml,top值-1标示根据top值倒序（正序设置为1即可），同样date也是根据创建日期倒序。

  > ```yml
  > # 查找
  > index_generator:
  >   path: ''
  >   per_page: 10
  >   order_by: -date
  >   
  > # 改为
  > index_generator:
  >   path: ''
  >   per_page: 10
  >   order_by:
  >     top: -1
  >     date: -1
  > ```

---

### 3. 文章标签分类功能

#### 3.1 文章分类功能

* 通过hexo新建一个页面，命名为 categories 

  > ```bash
  > hexo new page categoties
  > ```

* 编辑刚新建的页面，将页面的类型`type`设置为 categories ，主题将自动为这个页面显示所有分类。

  > 通过设置categoties，并在menu中添加对应链接来实现博文分类
  >
  > categoties是不同级的，适合纵向文件分类    
  >
  > ```yml
  > title: 分类
  > date: 2020-07-29 10:36:40
  > type: "categories"
  > comments: false
  > ```

* 菜单中添加链接。编辑主题的`_config.yml` ，将categories添加到menu中

  > ```yml
  > menu:
  >   home: /
  >   categories: /categories
  >   archives: /archives
  >   tags: /tags
  > ```

#### 3.2 文章标签分类功能

* MarkDown 各种标签设置

  > 通过设置tags，并在menu中添加对应链接来实现博文分类   
  >
  > tags标签是同级的，适合横向文件分类 
  >
  > ```yml
  > ---
  > title: 				 #文章标题
  > subtitle:                            #子标题
  > categories:                       #文章分類目錄 可以省略    
  > toc: true                            #目录 需要安装插件或者主题自带
  > reward: true                      #打赏 需要安装插件或者主题自带
  > declare: true                      #版权 需要安装插件或者主题自带
  > date:                                   #文章日期
  > header-img:                        #顶部背景图片
  > tags:                                    #标签
  > ---
  > ```


---

### 4. 添加404网页

* 参考：[自定义Github Pages](https://docs.github.com/cn/github/working-with-github-pages/creating-a-custom-404-page-for-your-github-pages-site)

* 在`/source/`目录下新建`404.md`在头部添加属性`permalink: /404.html`，页面内容可以自定义。

  > 推荐使用[腾讯公益404页面](https://www.qq.com/404/)（注意要把首页链接换成自己的）
  >
  > `404.md`文件内容
  >
  > ```html
  > ---
  > # example 404.md 
  > 
  > title: 404 Not Found：该页无法显示 
  > 
  > date: 2019-07-26 21:51:29 
  > 
  > permalink: /404.html
  > ---
  > 
  > <!DOCTYPE html>
  > <html>
  >     <head>
  >         <meta charset="UTF-8" />
  >         <title>404</title>                              
  >     </head>    
  >     <body>
  >         <script type="text/javascript" src="//qzonestyle.gtimg.cn/qzone/hybrid/app/404/search_children.js" homePageName="返回首页" homePageUrl="https://chthollists.github.io/"></script> 
  >     </body>
  > </html>
  > ```

---

### 5. 头像旋转功能

* 在`themes\yilia\source\`文件夹下增加一个css文件`avatarrotation.css`用来旋转360度

  > `avatarrotation.css`内容
  >
  > ```css
  > .left-col #header .profilepic img {
  > 	/* 控制旋转速度时间*/
  >   -webkit-transition: -webkit-transform 1.0s ease-out;
  >   -moz-transition: -moz-transform 1.0s ease-out;
  >   transition: transform 1.0s ease-out;
  > }
  > .left-col #header .profilepic img:hover {
  > 	/* 鼠标经过360% */
  >   -webkit-transform: rotateZ(360deg);
  >   -moz-transform: rotateZ(360deg);
  >   transform: rotateZ(360deg);
  > }
  > ```

* 然后在`themes\yilia\layout\_partial\head.ejs`文件中添加进去创建的`css`文件：
  找到`<%- partial('css') %>`，在它的下面添加代码，把刚才写的文件添加进去，

  > **注意！！是在它的下面添加，不然旋转速度将不会生效**
  >
  > ```html
  > <% if (theme.avatarrotation){ %>
  > 	<link rel="stylesheet" type="text/css" href="/./avatarrotation.css">
  > <% } %>
  > ```

* 最后在`theme\_config.yml`中添加

  > ```yml
  > #头像是否旋转(如果不要旋转取false)
  > avatarrotation: true
  > ```

---

### 6. 修改默认文章格式

> 修改hexo new "article" 的Front-matter的属性

* 修改根目录下的`scaffolds`文件夹的post.md`

  > ```yml
  > title: {{ title }}
  > date: {{ date }}
  > subtitle:                         #子标题
  > categories:                       #文章分類目錄 可以省略    
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
  >   <br/>
  >   <a class="cloud-tie-join-count" href="javascript:void(0);" style="color:gray;font-size:14px;">
  >   <span class="icon-sort"></span>
  >   <span id="busuanzi_container_page_pv" style="color:#ef7522;font-size:14px;">
  >             阅读数: <span id="busuanzi_value_page_pv"></span>次 &nbsp;&nbsp;
  >   </span>
  >   </a>
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
  >         本文阅读量: <span id="busuanzi_value_page_pv"></span>次
  >  </span>
  > ```

#### 7.5 不蒜子统计与Live2D冲突问题

* 参考：[不蒜子功能失效](https://boyinthesun.cn/post/error-live2d-busuanzi/)

* 问题：当配置了Live2D功能后，不蒜子统计功能会与其发生冲突从而失效

* 分析：查看网页源码Ctrl+u，可以发现：

  * 不蒜子功能正常时

    > ```html
    > <span id="busuanzi_container_site_pv">
    >     |&nbsp;<i class="far fa-eye"></i>&nbsp;总访问量:&nbsp;<span id="busuanzi_value_site_pv"
    >         class="white-color"></span>&nbsp;次
    >     </span>
    > <span id="busuanzi_container_site_uv">
    >     |&nbsp;<i class="fas fa-users"></i>&nbsp;总访问人数:&nbsp;<span id="busuanzi_value_site_uv"
    >         class="white-color"></span>&nbsp;人
    >     </span>
    > </span>
    > ```

  * 不蒜子功能失效时

    > ```html
    > <span id="busuanzi_container_site_pv" style="display: none;">
    >     |&nbsp;<i class="far fa-eye"></i>&nbsp;总访问量:&nbsp;<span id="busuanzi_value_site_pv" 
    >         class="white-color">2303950</span>&nbsp;次
    > </span>
    > <span id="busuanzi_container_site_uv" style="display: none;">
    >     |&nbsp;<i class="fas fa-users"></i>&nbsp;总访问人数:&nbsp;<span id="busuanzi_value_site_uv" 
    >         class="white-color">160644</span>&nbsp;人
    > </span>
    > ```

  * 所以导致失效的原因是多了一个display的属性值变成了none，从而不显示不蒜子统计

    > ```js
    > style="display: none;"
    > ```

* 解决办法：

  * 打开不蒜子官网上的[busuanzi.pure.mini.js](https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js)文件，将其下载到本地，放到yilia主题下的路径中

    > 如：`theme/yilia/source`路径

  * 修改busuanzi.pure.mini.js文件

    > ```js
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
  >           <%- partial('post/word') %>
  >           <% } %>
  > ```

* 最后在`themes\yilia\layout\_partial\post`目录下新建`word.ejs`,内容如下

  > ```html
  > <div style="margin-top:10px;">
  >     <span class="post-time">
  >       <span class="post-meta-item-icon">
  >         <i class="fa fa-keyboard-o"></i>
  >         <span class="post-meta-item-text" style="font-size: 16px;color: grey">  字数统计: </span>
  >         <!--这里样式可以自己定制-->
  >         <span class="post-count" style="font-size: 16px;color: grey"><%= wordcount(post.content) %>字</span>
  >       </span>
  >     </span>
  > 
  >     <span class="post-time">
  >       &nbsp; | &nbsp;
  >       <span class="post-meta-item-icon">
  >         <i class="fa fa-hourglass-half"></i>
  >         <span class="post-meta-item-text" style="font-size: 16px;color: grey">  阅读时长: </span>
  >         <span class="post-count" style="font-size: 16px;color: grey"><%= min2read(post.content) %>分</span>
  >       </span>
  >     </span>
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
  > ```html
  > <!-- 网易云音乐插件 -->
  > <% if (theme.music && theme.music.enable){ %>
  >     <div style="position:absolute; bottom:120px left:auto; width:85%">
  >        <iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=228 height=52 src="//music.163.com/outchain/player?type=4&id=349623778&auto=0&height=32"></iframe>
  >     </div>
  > <% } %>
  > ```
  >
  >  通过设置marginheight="0" width=330 height=110 来改变大小，美化界面

* 在yilia主题配置文件`_config.yml`中添加属性

  > ```yml
  > # 网易云音乐插件
  > music:
  >   enable: true
  >   #id: 1332647902  # 网易云分享的ID
  >   autoplay: false  # 是否开启自动播放
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

      > ```ini
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
  > ```json
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
  >     enable: true
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

### 11. 版权信息功能

> 这里介绍两种添加版权信息的方式，其实两者区别不大，一种直接在articles.ejs里面添加版权信息，另一种是把版权信息单独卸载一个ejs文件declare.ejs内。

#### 11.1 直接修改版

> 参考：[添加版权信息](http://dongshuyan.com/2019/05/24/hexo%E5%8D%9A%E5%AE%A2%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9/#4-%E6%80%8E%E4%B9%88%E7%BD%AE%E9%A1%B6%E6%96%87%E7%AB%A0)

* 修改文件`themes/yilia/layout/_partial/article.ejs`

  > 代码添加的位置——此处第11行
  >
  > ```html
  > <div class="article-entry" itemprop="articleBody">
  >   <% if (post.excerpt && index){ %>
  >     <%- post.excerpt %>
  >     <% if (theme.excerpt_link) { %>
  >       <a class="article-more-a" href="<%- url_for(post.path) %>#more"><%= theme.excerpt_link %> >></a>
  >     <% } %>
  >   <% } else { %>
  >     <%- post.content %>
  >   <% } %>
  >    
  >   <-- 在此处添加代码-->
  >       
  >   <% if ((theme.reward_type === 2 || (theme.reward_type === 1 && post.reward)) && !index){ %>
  >   <div class="page-reward">
  >     <a href="javascript:;" class="page-reward-btn tooltip-top">
  >     <div class="tooltip tooltip-east">
  > ```
  >
  > 添加
  >
  > ```html
  > <!-- 《添加版权声明 -->
  > <%
  >   var sUrl = url.replace(/index\.html$/, '');
  >   sUrl = /^(http:|https:)\/\//.test(sUrl) ? sUrl : 'https:' + sUrl;
  > %>
  > 
  > <!-- #版权基础设定：0-关闭声明； 1-文章对应的md文件里有declare: true属性，才有版权声明； 2-所有文章均有版权声明 -->
  > <% if ((theme.declare.declare_type === 2 || (theme.declare.declare_type === 1 && post.declare)) && !index){ %>
  >   <div class="declare">
  >     <strong class="author">本文作者：</strong>
  >     <% if(config.author != undefined){ %>
  >       <%= config.author%>
  >     <% }else{%>
  >       <font color="red">请在博客根目录“_config.yml”中填入正确的“author”</font>
  >     <%}%>
  >     <br>
  >     <strong class="create-time">发布时间：</strong>
  >     <%- date(post.date, 'YYYY-MM-DD HH:mm:ss') %>
  >     <br>
  >     <strong class="update-time">最后更新：</strong>
  >     <%- date(post.updated, 'YYYY-MM-DD HH:mm:ss') %>
  >     <br>
  >     <strong class="article-titles">本文标题：</strong>
  >     <a href="<%= config.url%>/<%= post.path %>" title="<%= post.title %>" target="_blank"><%= post.title %></a>
  >     <br>
  >     <strong class="article-url">本文链接：</strong>
  >     <a href="<%=config.url%>/<%= post.path  %>" title="<%= post.title %>" target="_blank"><%= config.url%>/<%= page.path %></a>
  >     <br>
  >     <strong class="copyright">版权声明：</strong>
  >     本作品采用
  >     <a rel="license" href="<%= theme.declare.licensee_url%>" title="<%= theme.declare.licensee_alias %>"><%= theme.declare.licensee_name%></a>
  >     许可协议进行许可。转载请注明作者和出处！
  >     <% if(theme.declare.licensee_img != undefined){ %>
  >       <br>
  >       <a rel="license" href="<%= theme.declare.licensee_url%>"><img alt="知识共享许可协议" style="border-width:0" src="<%= theme.declare.licensee_img%>"/></a>
  >     <% } %>
  >   </div>
  > <% } else {%>
  >   <div class="declare" hidden="hidden"></div>
  > <% } %>
  > <!-- 添加版权声明》 -->
  > ```

* 创建新文件`declare.scss`

  > 路径：`themes/yilia/source-src/css/declare.scss`
  >
  > 添加css
  >
  > ```css
  > .declare {
  >     background-color: #eaeaea;
  >     margin-top: 2em;
  >     border-left: 3px solid #ff1700;
  >     padding: .5em 1em;
  > }
  > ```

* 修改文件`themes/yilia/source-src/css/main.scss`

  > ```css
  > @import "./declare";
  > ```

* 修改主题配置文件`themes/yilia/_config.yml`

  > ```yml
  > # 版权声明
  > #在需要进行版权声明的文章的md文件头部，设置属性declare: true。
  > #版权基础设定：0-关闭声明； 1-文章对应的md文件里有declare: true属性，才有版权声明； 2-所有文章均有版权声明
  > declare:
  >   declare_type: 1
  >   licensee_url: https://creativecommons.org/licenses/by-nc-sa/4.0/        #当前应用的版权协议地址。
  >   licensee_name: 'CC BY-NC-SA 4.0'                                        #版权协议的名称
  >   licensee_alias: '知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议'       # alias别名
  >   licensee_img: https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png    #版权协议的Logo
  > ```

* 然后在需要进行版权声明的文章的md文件头部，设置属性declare

  > ```yml
  > declare:true
  > ```

#### 11.2 新建文件版

> 参考：[添加版权信息](https://blog.csdn.net/qq_40922859/article/details/97035497?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param)

* 新建文件`declare.ejs`

  > 路径：`\themes\yilia\layout\_partial\post\`
  >
  > ```html
  > <!-- 《添加版权声明 -->
  > <%
  >   var sUrl = url.replace(/index\.html$/, '');
  >   sUrl = /^(http:|https:)\/\//.test(sUrl) ? sUrl : 'https:' + sUrl;
  > %>
  > 
  > <!-- #版权基础设定：0-关闭声明； 1-文章对应的md文件里有declare: true属性，才有版权声明； 2-所有文章均有版权声明 -->
  > <% if ((theme.declare.declare_type === 2 || (theme.declare.declare_type === 1 && post.declare)) && !index){ %>
  >   <div class="declare">
  >     <strong class="author">本文作者：</strong>
  >     <% if(config.author != undefined){ %>
  >       <%= config.author%>
  >     <% }else{%>
  >       <font color="red">请在博客根目录“_config.yml”中填入正确的“author”</font>
  >     <%}%>
  >     <br>
  >     <strong class="create-time">发布时间：</strong>
  >     <%- date(post.date, 'YYYY-MM-DD HH:mm:ss') %>
  >     <br>
  >     <strong class="update-time">最后更新：</strong>
  >     <%- date(post.updated, 'YYYY-MM-DD HH:mm:ss') %>
  >     <br>
  >     <strong class="article-titles">本文标题：</strong>
  >     <a href="<%= config.url%>/<%= post.path %>" title="<%= post.title %>" target="_blank"><%= post.title %></a>
  >     <br>
  >     <strong class="article-url">本文链接：</strong>
  >     <a href="<%= config.url%>/<%= post.path  %>" title="<%= post.title %>" target="_blank"><%= config.url%>/<%= page.path %></a>
  >     <br>
  >     <strong class="copyright">版权声明：</strong>
  >     本作品采用
  >     <a rel="license" href="<%= theme.declare.licensee_url%>" title="<%= theme.declare.licensee_alias %>"><%= theme.declare.licensee_name%></a>
  >     许可协议进行许可。转载请注明作者和出处！
  >     <% if(theme.declare.licensee_img != undefined){ %>
  >       <br>
  >      <!--版权logo如果加载不出来可以注释掉-->
  >       <a rel="license" href="<%= theme.declare.licensee_url%>"><img alt="知识共享许可协议" style="border-width:0" src="<%= theme.declare.licensee_img%>"/></a>
  >     <% } %>
  >   </div>
  > <% } else {%>
  >   <div class="declare" hidden="hidden"></div>
  > <% } %>
  > <!-- 添加版权声明》 -->     
  > ```

* 修改文件`themes\yilia\layout\_partial\article.ejs`

  > 添加
  >
  > ```html
  > <!-- 《添加版权声明 -->
  >         <% if(theme.declare){%>
  >             <%- partial('post/declare') %>
  >         <% } %>
  > <!-- 添加版权声明》 -->
  > ```

* 修改文件`\themes\yilia\source\main.xxxxx.css`

  > 在底部添加
  >
  > ```css
  > /* 版权声明 */
  > .declare {
  > 	background-color: #eaeaea;
  > 	margin-top: 2em;
  > 	border-left: 3px solid #ff1700;
  > 	padding: .5em 1em;
  > }
  > ul.declare li {
  >     font-size: 19px;
  >     font-weight: bold;
  >     line-height: 1.5;
  > }
  > /* 版权声明移动端 */
  > @media screen and (max-width:800px){
  > 	li.declare_article-url{
  > 		display: none;
  > 	}
  >   ul.declare li{
  > 		font-weight: normal;
  > 		font-size: 16px;
  > 	}
  > }
  > ```

* 修改主题配置文件`\themes\yilia\_config.yml`:

  > 添加
  >
  > ```yml
  > # 版权声明
  > #在需要进行版权声明的文章的md文件头部，设置属性declare: true。
  > #版权基础设定：0-关闭声明； 1-文章对应的md文件里有declare: true属性，才有版权声明； 2-所有文章均有版权声明
  > declare:
  >   declare_type: 1
  >   licensee_url: https://creativecommons.org/licenses/by-nc-sa/4.0/        #当前应用的版权协议地址。
  >   licensee_name: 'CC BY-NC-SA 4.0'                                        #版权协议的名称
  >   licensee_alias: '知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议'       # alias别名
  >   licensee_img: https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png    #版权协议的Logo
  > ```

* 在需要进行版权声明的文章的md文件头部，设置属性copyright

  > ```yml
  > declare: true
  > ```

* 注意：

  * 第二种方法中，没有新建declare.sccs文件，而是直接将版权信息的显示格式设置放在了`\themes\yilia\source\main.xxxxx.css`中；

  * 版权协议的Logo所在网址需要人机验证，所以获取不到Logo时可以手动登录[协议Logo](https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png)进行验证，从而使得Logo可以被获取；

  * 一些属性定义

    * `page`和`post`基本一致

    - **theme.author** ： 作者名
    - **post.title** ：文章标题
    - **post.path**　：文章相对路径
    - **post.permalink** : 文章绝对路径
    - **post.date** : 日期

---

### 12. 爱心、光玉特效功能

* [特效大全](https://yansheng836.github.io/article/cf9c6a5e.html#%E5%8A%A8%E6%80%81%E7%BA%BF%E6%9D%A1%E8%83%8C%E6%99%AF)
* 爱心、雪花、动态线条、文字、彩带等特效

#### 12.1 点击爱心

* 新建js文件：\yilia\source\路径下新建`clicklove.js`,内容如下

  > `clicklove.js`
  >
  > ```js
  > ! function (e, t, a) {
  >     function n() {
  >         c(".heart{width: 10px;height: 10px;position: fixed;background: #f00;transform: rotate(45deg);-webkit-transform: rotate(45deg);-moz-transform: rotate(45deg);}.heart:after,.heart:before{content: '';width: inherit;height: inherit;background: inherit;border-radius: 50%;-webkit-border-radius: 50%;-moz-border-radius: 50%;position: fixed;}.heart:after{top: -5px;}.heart:before{left: -5px;}"), o(), r()
  >     }
  >     function r() {
  >         for (var e = 0; e < d.length; e++) d[e].alpha <= 0 ? (t.body.removeChild(d[e].el), d.splice(e, 1)) : (d[e].y--, d[e].scale += .004, d[e].alpha -= .013, d[e].el.style.cssText = "left:" + d[e].x + "px;top:" + d[e].y + "px;opacity:" + d[e].alpha + ";transform:scale(" + d[e].scale + "," + d[e].scale + ") rotate(45deg);background:" + d[e].color + ";z-index:99999");
  >         requestAnimationFrame(r)
  >     }
  >     function o() {
  >         var t = "function" == typeof e.onclick && e.onclick;
  >         e.onclick = function (e) {
  >             t && t(), i(e)
  >         }
  >     }
  >     function i(e) {
  >         var a = t.createElement("div");
  >         a.className = "heart", d.push({
  >             el: a,
  >             x: e.clientX - 5,
  >             y: e.clientY - 5,
  >             scale: 1,
  >             alpha: 1,
  >             color: s()
  >         }), t.body.appendChild(a)
  >     }
  >     function c(e) {
  >         var a = t.createElement("style");
  >         a.type = "text/css";
  >         try {
  >             a.appendChild(t.createTextNode(e))
  >         } catch (t) {
  >             a.styleSheet.cssText = e
  >         }
  >         t.getElementsByTagName("head")[0].appendChild(a)
  >     }
  >     function s() {
  >         return "rgb(" + ~~(255 * Math.random()) + "," + ~~(255 * Math.random()) + "," + ~~(255 * Math.random()) + ")"
  >     }
  >     var d = [];
  >     e.requestAnimationFrame = function () {
  >         return e.requestAnimationFrame || e.webkitRequestAnimationFrame || e.mozRequestAnimationFrame || e.oRequestAnimationFrame || e.msRequestAnimationFrame || function (e) {
  >             setTimeout(e, 1e3 / 60)
  >         }
  >     }(), n()
  > }(window, document);
  > ```

* 修改ejs文件，找到`themes\yilia\layout\_partial\after-footer.ejs`

  > ```html
  > # 添加
  > <!--页面点击小红心-->
  > <script type="text/javascript" src="/clicklove.js"></script>
  > ```
  >
  > 注意：这里的路径要对应之前创建clicklove.ejs的地方

#### 12.2 光玉特效

* 新建snow.js文件，路径：`themes\yilia\source\`

  > ```js
  > var snow = {
  >   info: {
  >     top: 0,
  >     left: 0,
  >     zIndex: 500,
  >     number: 70
  >   },
  >   down: function () {
  >     var f = window.innerWidth;
  >     var g = window.innerHeight;
  >     var d = document.createElement("canvas");
  >     d.style.position = "fixed";
  >     d.style.pointerEvents = "none";
  >     d.style.top = snow.info.top + "px";
  >     d.style.left = snow.info.left + "px";
  >     d.style.zIndex = snow.info.zIndex;
  >     d.width = f;
  >     d.height = g;
  >     document.body.appendChild(d);
  >     var c = [];
  >     for (var a = 0; a < snow.info.number; a++) {
  >       c.push({
  >         x: Math.random() * f,
  >         y: Math.random() * g,
  >         r: Math.random() * 4 + 1,
  >         n: Math.random() * 70
  >       })
  >     }
  >     var b = d.getContext("2d");
  >     var e = 0;
  >     setInterval(function () {
  >         b.clearRect(0, 0, f, g);
  >         b.fillStyle = "rgba(233,235,154, 1)";
  >         b.shadowBlur = 5;
  >         b.shadowColor = "rgba(250,250,210 2)";
  >         b.beginPath();
  >         for (var j = 0; j < 70; j++) {
  >           var h = c[j];
  >           b.moveTo(h.x, h.y);
  >           b.arc(h.x, h.y, h.r, 0, Math.PI * 2, 0)
  >         }
  >         b.fill();
  >         e += 0.01;
  >         for (var j = 0; j < 70; j++) {
  >           var h = c[j];
  >           h.y += Math.cos(e + h.n) + h.r / 2;
  >           h.x += Math.sin(e) * 2;
  >           if (h.x > f + 5 || h.x < -5 || h.y > g) {
  >             c[j] = j % 3 > 0 ? {
  >               x: Math.random() * f,
  >               y: -10,
  >               r: h.r,
  >               n: h.n
  >             } : Math.sin(e) > 0 ? {
  >               x: -5,
  >               y: Math.random() * g,
  >               r: h.r,
  >               n: h.n
  >             } : {
  >               x: f + 5,
  >               y: Math.random() * g,
  >               r: h.r,
  >               n: h.n
  >             }
  >           }
  >         }
  >       },
  >       15)
  >   }
  > };
  > // yilia-plus主题已经集成了，可以直接用
  > ```

* 修改`themes\yilia\layout\layout.ejs`文件

  > 添加
  >
  > ```html
  > <!--雪花特效-->
  > <% if (theme.snow){ %>
  >   <script type="text/javascript" src="<%- url_for('snow.js') %>"></script>
  >   <script type="text/javascript" src="<%- url_for('jquery-2.1.4.min.js') %>"></script>
  >   <script>
  >     snow.down();
  >     $(window).resize(function() {
  >       $("canvas").css("z-index","500").remove();
  >       snow.down();
  >     });
  >   </script>
  >   <% } %>
  > ```

* 修改主题配置文件`themes\yilia\_config.yml`

  > ```yml
  > # 飘雪特效
  > # https://github.com/MlgmXyysd/snow.js
  > snow: true
  > ```

---

### 13. SEO优化

* SEO(Search Engine Optimization)搜索引擎优化

  > 搜索引擎优化是一种利用搜索引擎的搜索规则来提高目的网站在有关搜索引擎内的排名的方式。

  * 针对个人网站的页面排版进行SEO的优化
  * 针对搜索引擎进行主动的优化

#### 13.1 加密文章URL

> 博客默认的地址 `网址/年/月/日/文章名` 格式不便于搜索引擎抓取，需要进行优化
>
> 注意：修改之后旧文章的统计数据(浏览量)会重新计数。

* 安装插件 hexo-abbrlink 

  > ```bash
  > npm install hexo-abbrlink --save
  > ```

* 修改根目录配置文件_config.yml

  > 查找
  >
  > ```yml
  >  permalink: :year/:month/:day/:title/ 
  > ```
  >
  > 修改
  >
  > ```yml
  >  permalink: post/:abbrlink.html
  > ```
  >
  > 并在下面添加：
  >
  > ```yml
  > abbrlink:
  >   alg: crc32  # 算法
  >   rep: hex    # 进制
  > ```

#### 13.2 添加站点地图SiteMap

* 安装插件 hexo-generator-sitemap 

* 安装插件 hexo-generator-baidu-sitemap 

  > ```bash
  >   npm install hexo-generator-sitemap --save     //默认的
  >   npm install hexo-generator-baidu-sitemap --save    //百度专属
  > ```

* 重新清理、渲染

  > pulic文件夹下会生成一个sitmap.xml文件
  >
  > ```bash
  > hexo clean
  > hexo g
  > ```

* 注意：sitemap 的初衷是给搜索引擎看的，为了提高搜索引擎对自己站点的收录效果，可以：

  * 手动到 Google 和百度等搜索引擎提交 sitemap.xml
  * 添加蜘蛛协议 robots，新建robots.txt文件，添加以下文件内容，把robots.txt放在hexo站点的source文件下

#### 13.3 注册百度站长平台

* 在百度站长平台添加网站

  > [百度搜索资源平台](https://ziyuan.baidu.com/)→用户中心→站点管理→添加网站
  >
  > 添加需要管理的网站地址

* 验证网站：三种方法——本文选用html标签验证

  * 文件验证

    > 将百度验证文件放在网站首页下，即：博客根目录/source下
    >
    > 但是hexo在渲染生成时，会修改html文件，导致验证失败

  * html标签验证

    > 将html验证代码添加到网站首页HTML代码的<head>标签之间
    >
    > 在 根目录/themes/yourtheme/layout/_partial/head.ejs 中添加如下代码:
    >
    > ```html
    > <meta name="baidu-site-verification" content="RdypQbGwZW" />
    > ```

  * Cname

    > 将RdypQbGwZW.yoursite使用CNAME解析到ziyuan.baidu.com

#### 13.4 针对百度搜索引擎的优化

* 提交SiteMap站点地图文件

  > 转到普通收录，选择自己的站点网址。
  >
  > 自动提交，选择sitemap。按照提示的格式添加自己的sitemap文件
  >
  > ```yml
  > https://chthollists.github.io/sitemap.xml
  > https://chthollists.github.io/baidusitemap.xml
  > ```

* 添加**蜘蛛协议** robots

  > 新建robots.txt文件，添加以下文件内容，把robots.txt放在hexo站点的source文件下
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
  > 可以在[百度搜索资源平台](https://ziyuan.baidu.com/)验证文件是否生效

#### 13.5 网站结构自身优化

* keywords 和 description

  > 在\scaffolds\post.md中添加如下代码，用于生成的文章中添加关键字和描述。
  >
  > ```yml
  > keywords: #文章关键词，多个关键词用英文逗号隔开
  > description: #文章描述，便于搜索引擎用关键词检索
  > ```

* 文章地址优化

  > 参见13.1 加密URL

---

### 14. 百度推送收录功能

* 除了通过提交SiteMap文件推送网站的地址外，还有自动提交：主动推送、自动推送，手动提交方式。详情参考[Hexo实现百度收录](https://zhuanlan.zhihu.com/p/100922816)
* 由于Github Pages上禁止百度爬虫，所以需要把Hexo博客部署到双线服务器上，选择Coding Pages，详情参考[Hexo双线部署CodingPages](https://blog.csdn.net/qq_36759224/article/details/100879609)
* [Hexo博客+Coding Pages](http://mfln8n.coding-pages.com/)

#### 14.1 新增博客文章页面自动推送

* 修改文件 <博客根目录>/themes/yilia/layout/_partial/article.ejs

  > 查找
  >
  > ```html
  > <%
  >         var sUrl = url.replace(/index\.html$/, '');
  >         sUrl = /^(http:|https:)\/\//.test(sUrl) ? sUrl : 'https:' + sUrl;
  >       %>
  > ```
  >
  > 在**前面**添加
  >
  > ```html
  > <!- 百度自动推送方式提交 -->
  > <% if (1){ %>
  >   <script>
  >   (function(){
  >       var bp = document.createElement('script');
  >       var curProtocol = window.location.protocol.split(':')[0];
  >       if (curProtocol === 'https') {
  >           bp.src = 'https://zz.bdstatic.com/linksubmit/push.js';
  >       }
  >       else {
  >           bp.src = 'http://push.zhanzhang.baidu.com/push.js';
  >       }
  >       var s = document.getElementsByTagName("script")[0];
  >       s.parentNode.insertBefore(bp, s);
  >   })();
  >   </script>
  > <% } %>
  > <!- 百度自动推送方式提交结束 -->
  > ```

* 注意：只会主自动推送新增博客文章页面，不包括其他页面。

#### 14.2 主动推送

> 主动推送最为快速的提交方式，是被百度收录最快的推送方式。
>
> 其实现原理如下：
>
> 新链接的产生， hexo generate 会产生一个文本文件，里面包含最新的链接；
>
> 新链接的提交， hexo deploy 会从上述文件中读取链接，提交至百度搜索引擎。

* 安装插件 hexo-baidu-url-submi

  > ```bash
  > npm install hexo-baidu-url-submit --save
  > ```

* 修改博客根目录配置文件_config.yml

  > 添加
  >
  > ```yml
  > baidu_url_submit:
  >   count: 20 ## 提交最新的20个链接
  >   host: http://mfln8n.coding-pages.com/ ## 百度站长平台中注册的域名
  >   token:  ## 16位准入秘钥
  >   path: baidu_urls.txt ## 文本文档的地址， 新链接会保存在此文本文档里
  > ```

* 获取准入秘钥

  > 在[百度搜索资源平台](https://ziyuan.baidu.com/)的平台收录中获取准入秘钥token ---16位

* 查看根目录下的_config.yml文件中url的值， 必须包含是百度站长平台注册的域名

  > 如：
  >
  > ```yml
  > # URL
  > url: http://mfln8n.coding-pages.com/
  > root: /
  > ```

* 加入新的deployer:

  > ```yml
  > deploy:
  >  - type: git
  >    repo: git@github.com:xxxxx
  >    branch: master
  >    # 加入新的type
  >  - type: baidu_url_submitter ## 添加这里内容即可
  > ```

#### 14.3 自动推送

* 安装自动推送JS代码的网页，在页面被访问时，页面URL将立即被推送给百度。

* 修改主题目录下的layout/post.ejs文件

  > 末尾添加自动推送代码：
  >
  > ```html
  > <script>
  > (function(){
  >     var bp = document.createElement('script');
  >     var curProtocol = window.location.protocol.split(':')[0];
  >     if (curProtocol === 'https') {
  >         bp.src = 'https://zz.bdstatic.com/linksubmit/push.js';
  >     }
  >     else {
  >         bp.src = 'http://push.zhanzhang.baidu.com/push.js';
  >     }
  >     var s = document.getElementsByTagName("script")[0];
  >     s.parentNode.insertBefore(bp, s);
  > })();
  > </script>
  > ```

---

### 15. 百度统计功能

* 在[百度统计官网](https://tongji.baidu.com)上添加网站，获取统计代码

  > ```html
  > <script>
  > var _hmt = _hmt || [];
  > (function() {
  >   var hm = document.createElement("script");
  >   hm.src = "https://hm.baidu.com/hm.js?0113964bae57c4e8dcc0a90d835680c4";
  >   var s = document.getElementsByTagName("script")[0]; 
  >   s.parentNode.insertBefore(hm, s);
  > })();
  > </script>
  > ```

* 修改文件`themes/yilia/layout/_partial/baidu-analytics.ejs`

* 或者新建文件`themes/yilia/layout/baidu-tongji.ejs`

  > 将获取的统计代码添加进去
  >
  > `baidu-analytics.ejs`
  >
  > 或者
  >
  > 在`baidu-tongji.ejs`中
  >
  > ```html
  > <% if (theme.baidu_tongji) { %>
  >    //这里添加统计代码
  > <% } %>
  > ```

* 修改主题的配置文件`themes/yilia/_config.yml`

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

  > 还需要在`themes\yilia\layout\_partial\head.ejs`,在`</head>`上面添加：
  >
  > ```html
  > <%- partial("baidu_tongji") %>
  > ```

* 可以通过百度统计的[代码安装检查](https://tongji.baidu.com/sc-web/10000247748/home/js/check?siteId=15660763)功能来验证百度统计功能是否添加成功。

---

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
  >   <% if (theme.valine && theme.valine.appid && theme.valine.appkey){ %>
  >       <section id="comments" class="comments">
  >         <style>
  >           .comments{margin:30px;padding:10px;background:#fff}
  >           @media screen and (max-width:800px){.comments{margin:auto;padding:10px;background:#fff}}
  >         </style>
  >         <%- partial('post/valine', {
  >           key: post.slug,
  >           title: post.title,
  >           url: config.url+url_for(post.path)
  >           }) %>
  >     </section>
  >   <% } %>
  > <% } %>
  > ```

* 修改`theme/yilia/_config.yml`

  > 添加
  >
  > ```yml
  > #6、Valine https://valine.js.org
  > valine_type: 1 #0 关闭评论  2全开评论  1需要再文章md开头加入valineenbale:true
  > valine: 
  >  enable: true
  >  appid:  你在注册时候得到的id
  >  appkey:  你在注册时候得到的key
  >  #这里的appid和appkey如果配置失败可以试试改成app_id和app_key
  >  verify: true #验证码
  >  notify: true #评论回复提醒
  >  avatar:  identicon   #评论列表头像样式：''/mm/identicon/monsterid/wavatar/retro/hide
  >  avatar_cdn: '' #头像CDN
  >  #评论框占位符
  >  placeholder: ヾﾉ≧∀≦)o '友情提醒，留下正确的邮箱地址可以第一时间获取评论反馈'
  >  guest_info: nick,mail,link # custom comment header
  >  pageSize: 10 # pagination size
  >  visitor: true
  > ```

* 修改CDN加速：新建`theme/yilia/layout/_partial/post/valine.ejs`

  > 官方的CDN是
  >
  > ```html
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
  >    var notify = '<%= theme.valine.notify %>' == true ? true : false;
  >    var verify = '<%= theme.valine.verify %>' == true ? true : false;
  >    new Valine({
  >             av: AV,
  >             el: '#vcomment',
  >             notify: notify,
  >             verify: verify,
  >             app_id: "<%= theme.valine.appid %>",
  >             app_key: "<%= theme.valine.appkey %>",
  >             placeholder: "<%= theme.valine.placeholder %>",
  >             avatar: "<%= theme.valine.avatar %>",
  >             avatar_cdn: "<%= theme.valine.avatar_cdn %>",
  >             pageSize: "<%= theme.valine.pageSize %>"
  >     });
  > </script>
  > ```

* 如果你的valine_type: 也是1，那么然后就需要在你文章抬头加上

  > ```yml
  > valineenbale:true
  > ```

* 注意：由于不明原因没有登录功能，所以后续准备改用Gitment/Gitalk评论系统。

#### 16.3 Gitalk评论系统

* 注册(已有Github可以跳过注册)

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
  > Client ID: ef41cd3f6bb938f5b4c6
  > Client Secret: 0a387b3802665d8a71757a618f93dfd8cb5ec7d8
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
  >  clientID: '<%=theme.gitalk.ClientID%>',
  >  clientSecret: '<%=theme.gitalk.ClientSecret%>',
  >  repo: '<%=theme.gitalk.repo%>',
  >  owner: '<%=theme.gitalk.githubID%>',
  >  admin: ['<%=theme.gitalk.adminUser%>'],
  >  id: '<%= page.date %>',
  >  distractionFreeMode: '<%=theme.gitalk.distractionFreeMode%>'
  >  pageSize: "<%= theme.gitalk.pageSize %>"
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
  >   padding: 0 30px !important;
  >   min-height: 20px;
  > }
  > 
  > #SOHUCS {
  >   #SOHU_MAIN .module-cmt-list .block-cont-gw {
  >     border-bottom: 1px dashed #c8c8c8 !important;
  >   }
  > }
  > ```

* 修改`layout/_partial/article.ejs`

  > 文件内新增gitalk相关的配置代码：
  >
  > ```html
  > <% if ((theme.gitalk_type === 2 || (theme.gitalk_type === 1 && post.gitalkenbale)) && !index){ %>
  > <% if(theme.gitalk.enable){ %>
  > <%- partial('post/gitalk', {
  >    key: post.slug,
  >    title: post.title,
  >    url: config.url+url_for(post.path)
  >  }) %>
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
  >   enable: true
  >   #enable: false
  >   scriptFrom: local # 默认
  >   pluginRootPath: live2dw/ # 插件在站点上的根目录(相对路径)
  >   pluginJsPath: lib/ # 脚本文件相对与插件根目录路径
  >   pluginModelPath: assets/ # 模型文件相对与插件根目录路径
  >   # scriptFrom: jsdelivr # jsdelivr CDN
  >   # scriptFrom: unpkg # unpkg CDN
  >   # scriptFrom: https://cdn.jsdelivr.net/npm/live2d-widget@3.x/lib/L2Dwidget.min.js # 你的自定义 url
  >   tagMode: false # 标签模式, 是否仅替换 live2d tag标签而非插入到所有页面中
  >   debug: false # 调试, 是否在控制台输出日志
  >   model:
  >     use:  # 模型名
  >     # use: live2d-widget-model-wanko # npm-module package name
  >     # use: wanko # 博客根目录/live2d_models/ 下的目录名
  >     # use: ./wives/wanko # 相对于博客根目录的路径
  >     # use: https://cdn.jsdelivr.net/npm/live2d-widget-model-wanko@1.0.5/assets/wanko.model.json # 你的自定义 url
  >   display:
  >     position: right
  >     width: 145
  >     height: 315
  >   mobile:
  >     show: true # 是否在移动设备上显示
  >     scale: 0.5 # 移动设备上的缩放       
  >   react:
  >     opacityDefault: 0.7
  >     opacityOnHover: 0.8
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
  
  