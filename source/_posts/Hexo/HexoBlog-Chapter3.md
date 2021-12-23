---
title: Hexo博客搭建系列Chapter3——Hexo教程
toc: true
reward: true
declare: true
gitalkenable: true
abbrlink: 8dfbe351
date: 2020-07-31 15:48:48
categories: 
- others
- Hexo
top: 
tags: 
- Hexo
- hexo-deployer-git
---

---

* 在Windows系统上利用Hexo搭建博客系列——Chapter3
* Chapter3：
  * 主要分享了如何安装配置Hexo以及Hexo的基本用法
  * 利用Hexo初步搭建了简易静态博客，并部署到Github
  * 参考教程：[Hexo博客搭建教程](http://dongshuyan.com/2016/04/07/%E6%80%8E%E4%B9%88%E5%86%99%E5%8D%9A%E5%AE%A2/)

<!-- more -->

# Hexo博客搭建系列——Chapter 3

---

## 三、利用Hexo搭建博客

### 1. 准备工作 Node.js & Git

* 配置Node.js：[Nodejs官网](https://nodejs.org/en/)

* 配置Git：[Git官网](https://git-scm.com/downloads)

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

* 安装Hexo：[Hexo官网](https://hexo.io/)

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

* Hexo启动：本地访问地址：http://localhost:4000

  > ```bash
  > # 启动Hexo 搭建初始博客，默认4000端口
  > hexo s
  > # 或者
  > hexo server
  > # 当Git bash上停止服务器后，无法访问
  >```
  
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

  * 在Github上新建库，库名为：用户名.github.io
  
  * ssh：git@github.com:Amaduess/Amaduess.github.io.git

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