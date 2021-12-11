---
title: Hexo博客搭建系列Chapter2——Git教程
toc: true
reward: true
declare: true
gitalkenable: true
abbrlink: fafcd3c7
date: 2020-07-31 15:48:05
categories: 
- others
- Hexo
top: 
tags:
- Git
- Github 
---

---

* 在Windows系统上利用Hexo搭建博客系列——Chapter2
*  Chapter2：主要分享了利用Hexo搭建博客的前提条件 Git 分布式版本控制系统

<!-- more -->

# Hexo博客搭建系列——Chapter 2

---

## 二、Git与Github

### 1. Git安装与配置

* Git 分布式版本控制系统原理

  > 版本控制系统，其实只能跟踪文本文件的改动，比如TXT文件，网页，所有的程序代码等等，无法追踪图片、音频、视频等的改动。

* Git 组成

  - 工作区(Working Directory)：指电脑本地实际的目录，即工作空间

  - 暂存区：stage、index

    > 一般存放在 `.git目录下`的index文件`.git/index`中，也可称为索引`index`。

  - 版本库(Repository)：工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。

* 安装包与教程

  * 安装包：[Git官网](https://git-scm.com/downloads)
  * 教程：[Git教程——廖雪峰](https://www.liaoxuefeng.com/)、[Git教程](https://blog.csdn.net/qq_39387475/article/details/84996173)

* 在Git bash中配置本地用户名及邮箱

  * 全局配置

    > 如果用了 --global 选项，那么更改的配置文件就是位于用户主目录下的文件，以后所有的项目都会默认使用这里配置的用户信息。

  * 修改配置

    > 如果要在某个特定的项目中使用其他名字或者邮箱，只要去掉 --global 选项重新配置即可，新的设定保存在当前项目的`.git/config`文件里。

  * 常用命令

    > ```bash
    > # Git bash 命令行中执行命令
    > # 查看配置
    > git config --list ：
    > # 设置全局用户名
    > git config --global user.name "your name"   
    > # 设置全局邮箱
    > git config --global user.email "your email" 
    > # 查看本目录下仓库的用户名
    > git config user.name
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

  * 添加文件：所有要管理的文件必须放在仓库中

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

  * 文件版本回溯的原理：指针与版本链

    * 在Git中，用`HEAD`表示当前版本，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，往上100个版本简写成`HEAD~100`。
    * Git在内部有个指向当前版本的`HEAD`指针，回溯版本实际上是改变指针的对象。

  * 文件版本回溯

    > ```bash
    ># 版本回溯 --旧版
    > # git reset 版本(HEAD表示法、或者log下显示的版本号---输入前几位即可自动匹配)
    >git reset --hard HEAD^
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

  * 文件撤销修改：两种情况

    * 直接撤销：一种是文件自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态

    * 暂存区撤销：一种是文件已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态

    * 总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态

      > ```bash
      > # 旧版
      > git checkout -- 文件名
      > 
      > # 新版
      > git restore 文件名
      > ```

  * 文件删除与恢复

    * 工作区的文件被删除后，在删除版本库中的文件

      > ```bash
      > git rm  文件名
      > git commit -m "说明remove "
      > ```

    * 工作区的文佳误删后，通过版本库恢复文件

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

* 将文件push到远程仓库的流程

  > ```bash
  > # 在目标路径下Git bash
  > git init
  > git add --all
  > git commit -m 'readme'
  > git remote add origin git@server-name:path/repo-name.git
  > # git remote -v
  > git push -u origin master
  > ```

* 创建SSH Key：使用注册Github、Gitee时的邮箱

  > ```bash
  > # 会在用户主目录下，生成.ssh目录，里面有id_rsa私钥、id_ras.pub公钥文件
  > ssh-keygen -t rsa -C "youremail@example.com"
  > ```
  >
  
* 登陆Github：添加公钥`Add SSH Key`

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
  > git remote -v
  > 
  > # 推送push
  > # 第一次push -u 将本地master与远程master关联
  > git push -u origin master
  > # 之后push
  > git push origin master
  > ```

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