---
title: Hexo博客搭建系列Chapter1——简介
toc: true
reward: true
declare: true
gitalkenable: true
abbrlink: 63f5827d
date: 2020-07-31 15:47:53
categories: 
- others
- Hexo
top: 
tags:
- Hexo
- Hugo
- WordPress
- VuePress
- Jekyllrb
---

---

* 在Windows系统上利用Hexo搭建博客系列——Chapter1
*  Chapter1：作为博客搭建的开端，简单分享了搭建静态博客和动态博客需要的工具

<!-- more -->

# Hexo博客搭建系列——Chapter 1

---

## 博客分类及搭建工具

> 目前，主流的博客主要分静态博客和动态博客两类。所谓动态，就是指有前端有后端，可以登录到后台进行管理。静态博客是纯前端的展示，没有后端。
>

### 1. 动态博客 Dynamic Blog

* 简书、CSDN、知乎、博客园、掘金等动态博客公共平台上搭建博客

  * 优点：简单
  * 缺点：受到平台的限制

* WordPress框架：[WordPress官网](https://wordpress.org/)

  * 基于PHP，使用WordPress等成熟框架搭建动态博客 + 服务器部署
  
  * 优点：相对简单
  * 缺点：笨重
  
* 前后端开发自行搭建

  * 使用 Java、PHP、Python等语言开发属于自己的动态博客 + 服务器部署

  * 优点：功能强大
  * 缺点：难度大

---

### 2. 静态博客 Static blog

#### 2.1 静态博客框架：轻量级、快速

* Hexo：[Hexo官网](https://hexo.io/)

  > 基于 JavaScript，需要配置Node.js环境。

* Jekyllrb：[Jekyllrb官网](https://jekyllrb.com/)

  > 配置环境：Ruby、RubyGems、Node.js。

* Hugo：[Hugo官网](https://www.gohugo.org/)

  > 基于Go，后端开发新宠。

* VuePress：[VuePres官网](https://vuepress.vuejs.org/zh/guide/)

  > 基于Vue.js前端框架，配置环境： [Vue](http://vuejs.org/)、[Vue Router](https://github.com/vuejs/vue-router) 、[webpack](http://webpack.js.org/)。

* Solo：[Solo官网](https://solo.b3log.org/)

  > 基于Java，配置环境为JDK。

#### 2.2 服务器

* 托管在GitHub、Coding、Gitee码云等公共平台

  * 优点：免费、简便
  * 缺点：访问速度慢、Github限制百度爬虫，不方便实现SEO优化
  * 注意：可多站点部署

* 自己搭建云服务器：[Hexo+阿里云教程](https://mp.weixin.qq.com/s?sn=8009155566a3f5cd3e46b4052a047f73&idx=1&mid=2247483706&lang=zh_CN&__biz=MzUzMTA2Njk3Mw%3D%3D&chksm=fa497808cd3ef11ea8ba66a60bb131716b8278fcd1bf3105cf67c988b367d110089af453736c&token=55710950&bili_only=0#rd)

  * 优点：访问速度快、独立域名、自主可控
* 缺点：域名和云服务器均付费

