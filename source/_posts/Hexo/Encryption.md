---
title: Encryption test
password: ly666996
abstract: 本文是基于Eencrypt插件实现的文章加密功能的测试文章。
message: 请输入密码或联系Author
toc: true
categories: 
- others
- Hexo
tags:
  - Hexo
  - Blog
  - Encrypt
abbrlink: 7496a9d6
date: 2020-07-29 14:09:29
---

* 本文是基于Eencrypt插件实现的文章加密功能的测试文章。

* 文章加密功能的教程。

  <!-- more -->

# 文章加密功能

* 文章加密方式

  * Nginx授权访问

  * Hexo-Blog-Encrypt

    > 由于Nginx授权访问的前置条件是：Hexo博客搭在Nginx服务器；这里用Encrypt插件实现加密。

* 第一步：安装加密插件Encrypt

  * 引入插件：找到博客根目录下的`package.json`，最下面添加

    > ```yml
    > "hexo-blog-encrypt": "2.0.*"
    > ```

  * 安装插件：进入博客根目录，通过Git bash安装

    > ```bash
    > npm install
    > # 或者
    > cnpm
    > ```

* 第二步：修改博客配置

  > 找到根目录下的`_config.yml`文件，开启`encrypt`项
  >
  > ```yml
  > # Security
  > #
  > encrypt:
  >  enable: true
  > ```

* 第三步：修改MD文章开头属性

  > ```yml
  > # 在需要加密的文章.md文件开头写入
  > --- 
  > title: 加密文章标题
  > date: 2019-05-30 22:02:07
  > password: 加密的密码
  > abstract: 这里是博客简述（能被访客看见）
  > message: 输入密码提示语句（例如：请输入密码）
  > ---
  > ```