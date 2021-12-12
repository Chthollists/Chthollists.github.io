---
title: Hexo博客搭建番外篇——多节点更新Hexo博客
reward: true
declare: true
gitalkenable: true
categories:
  - others
  - Hexo
toc: true
top: 6
tags:
  - Hexo
abbrlink: 246f003b
date: 2021-12-11 23:35:58
---
---

* 利用GitHub的版本控制来实现多台主机同步更新博客的功能
  * Hexo原理的简单分析
  * 通过Git版本控制实现：分支、私有仓库
  * 新节点的依赖配置：`Node.js`、`Git`、`Hexo`
  * 参考教程：[Hexo博客多主机更新配置](https://www.jianshu.com/p/0b1fccce74e0?utm_campaign=maleskine)

<!-- more -->

# Hexo博客搭建番外篇——多节点更新

## 1. Hexo原理简析

### 1.1 依赖分析

* 环境依赖
  * `Node.js`
  * `Git`以及`GitHub Pages`：通过SSH关联
  * 其他`Pages`：如`Gitee`、`Coding Pages`
  * `MarkDown`编辑器：如`Typora`
  * 云对象存储客户端：如腾讯云客户端`cosbrowser`
* 资源配置依赖
  * Hexo相关的基础依赖包：`hexo-generator-archive`、`hexo-generator-tag`
  * Hexo后期扩展功能的包：如`hexo-helper-live2d`、`hexo-generator-baidu-sitemap`
  * Hexo远程部署到GitHub的依赖包：`hexo-deployer-git`
  * 相关的配置会保存在：源文件的`package.json`中
* 安装Hexo
  * Hexo客户端：`npm install -g hexo-cli`
  * Hexo依赖包：`npm intall`
* 博客内容依赖
  * 博文：源文件的`source`目录下的文件
  * 主题：源文件的`themes`目录下的文件

### 1.2 原理分析

* Hexo静态博客

  > Hexo实际上是一个静态博客生成工具，会根据个人的配置、主题样式等，将其中的MarkDown文件解析生成对应的HTML、CSS、JS等文件，组成静态页面。同时可以通过SSH的方式部署到GitHub Pages上，完成远端部署，从而实现长期访问。

* Hexo的源文件目录结构

  ![Hexo文件目录结构](https://raw.githubusercontent.com/Chthollists/PicRepo/master/hexo/Hexo%E6%96%87%E4%BB%B6%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84.bmp)

* Hexo的本质操作

  * 解析Hexo源文件，生成对应的静态页面文件，即`public`目录下的文件
  * 将该文件初始化为Git目录，并根据配置关联到对应的GitHub账户及其仓库分支，即`.deploy_git`目录
  * 进行`git add .`、`git commit`、`git push`等操作，将静态页面文件同步到远程仓库中
  * 通过访问GitHub Pages，即可访问生成的静态博客

### 1.3 同步更新Hexo的方式

* 物理方式

  > 使用网盘、U盘等存储介质同步Hexo源文件，即整个Hexo文件目录结构中的文件。

* Git版本控制方式

  * 双仓库方式
    * 在GitHub上创建两个仓库，分别管理Hexo源文件，以及生成、部署的静态页面文件
    * 优势：可以将管理Hexo源文件的仓库设置为私有的，避免泄露信息
  * 单仓库多分支方式
    * 在GitHub上的io仓库上创建新的分支，主分支与新分支分别管理Hexo源文件和网站静态页面文件
    * 优势：便捷但是有安全风险

* 同步原理思路

  * Master分支：管理Hexo生成的静态页面文件，并用于访问博客页面
  * HexoSource分支(或新的私有仓库)：管理Hexo源文件(source、themes等部署文件)
  * 原节点、新节点
    * clone新分支或新仓库到本地，通过pull、push操作进行Hexo源文件版本控制
    * 通过`hexo g -d`进行博客静态页面部署
  
* 分支同步博客更新：注意本文的前提是已经搭建了Hexo博客并部署到GitHub Pages上

  * 方式一：在GitHub图形界面上创建分支
  * 方式二：通过终端创建分支

---

## 2. 多节点同步配置

### 2.1 终端方式

* 第一步：源文件托管到新分支

  > ```bash
  > # git初始化
  > git init
  > # 添加远程仓库地址
  > git remote add origin https://github.com/用户名/仓库名(GitHub用户名.github.io).git
  > # 新建分支并切换到新建的分支
  > git checkout -b 分支名
  > # 添加所有本地文件到git
  > git add .
  > # git提交
  > git commit -m "提交的版本注释信息"
  > # 文件推送到hexo分支
  > git push origin 分支名
  > 
  > # 示例
  > git init 
  > git remote add origin https://github.com/chthollists/chthollists.github.io.git 
  > git checkout -b hexo_source
  > git add . 
  > git commit -m "create hexo_source branch"
  > git push origin hexo_source
  > 
  > # 如果出现错误信息
  > # ERROR: Repository not found.   
  > # fatal: Could not read from remote repository
  > # 则需要检查SSH是否配置成功、Git地址是否正确
  > git remote -v
  > # 如果不一致，通过该命令修改
  > git remote set-url origin https://github.com/用户名/仓库名.git
  > ```

* 第二步：原节点更新博客

  * 在Hexo源文件目录下的`source/_post/`目录下更新`md`博文，或者可以修改Hexo的配置主题等

  * 更新源文件到新分支：hexo_source

    > ```bash
    > # 先拉原来Github分支上的源文件到本地，进行合并
    > # 分支名后面的“--allow-unrelated-histories”是为了弹出“fatal: refusing to merge unrelated histories.”的错误
    > git pull origin hexo_source --allow-unrelated-histories
    > # 提交更新
    > git add . 
    > git commit -m "update xxx"
    > git push origin hexo_source
    > ```

  * Hexo部署静态文件到主分支：master

    > ```bash
    > $ hexo clean
    > $ hexo generate
    > $ hexo deploy
    > ```

### 2.2 GitHub图形界面方式

* 第一步：新建分支：hexo_source

  ![新建分支示意图](https://raw.githubusercontent.com/Chthollists/PicRepo/master/hexo/Step1-%E6%96%B0%E5%BB%BA%E5%88%86%E6%94%AF.bmp)
  
* 第二步：设置新分支为默认分支(非必须，也可通过命令指定分支)

  * Hexo目录下的源文件和远程仓库上的文件是不同的，实际上是`public`文件夹的文件通过`hexo d`上传到GitHub上了，其他的文件则留在本地目录下
  * Hexo实际上是将源文件中的博文、主题等发送到`username.github.io`远程仓库，同时把`md`文件转换成静态网页文件

* 第三步：原节点`clone`新分支到本地

  * 克隆：`git clone -b 分支名 仓库地址`

  * 查看分支：在`username.github.io`目录下执行`git branch`，结果为新分支名

    ![新分支原始的文件](https://raw.githubusercontent.com/Chthollists/PicRepo/master/hexo/hexo%E5%88%86%E6%94%AF%E5%88%9D%E5%A7%8B%E7%9A%84%E6%96%87%E4%BB%B6.bmp)

* 第四步：将Hexo源文件全部复制到`username.github.io`目录下

  > 需要将`themes`目录下的所有主题的`.git`目录，因为一个git仓库中不能包含另一个git仓库，提交主题文件夹会失败。

* 第五步：提交更新新分支

  > ```bash
  > # 提交更新
  > git add . 
  > git commit -m "update xxx"
  > git push origin hexo_source
  > ```

  ![新分支提交后的文件](https://raw.githubusercontent.com/Chthollists/PicRepo/master/hexo/hexo%E5%88%86%E6%94%AF%E6%8F%90%E4%BA%A4%E5%90%8E%E7%9A%84%E6%96%87%E4%BB%B6.bmp)

* 第六步：原节点更新博文

  * 通过`git pull`、`git push`来管理新分支上的博客源文件
  * 通过`hexo`来将博客的博文、主题配置等部署到主分支上，提供远程访问

* 第七步：新节点更新博文

  * 新节点首先要安装`Node.js`、`Git`、`Hexo`，并且要生成SSH公钥和私钥，与GitHub账户关联

  * 新节点首次使用Hexo时，需要`clone`远程仓库`username.github.io`的新分支，即管理Hexo源文件的分支，并在本地克隆的新分支目录下，执行`npm install`

    > 这是由于仓库有一个`.gitignore`文件，里面默认是忽略掉`node_modules`文件夹的，也就是说仓库的新分支并没有存储Hexo所需的插件依赖，所以需要install。

  * 克隆并安装完依赖之后，就可以和原节点一样，通过`git`管理Hexo源文件的版本，通过`hexo`部署静态页面

* 注意：所有节点在更新博客时，都要先`git pull`，从而保证操作的时最新版本

### 2.3 私有仓库隐藏源文件

* 分支同步方式的缺陷

  > 如果将Hexo源文件同步至新分支，则会由于`GitPages`或者`CodingPages`的特性，自动变为公开目录，如果其中有一些不希望别人看见的信息，则会变得不方便，所以可以新建一个私有仓库来进行同步，而不使用分支。

* 步骤

  * 第一步：在GitHub上新建私有仓库(会员功能，2019.1.8后免费使用)

  * 第二步：原节点在本地创建仓库，存放Hexo源文件，并关联到GitHub上的远程私有仓库，注意`git push -u`时由于第一次上传到该仓库需要带上`-u`参数

    > ```bash
    > # 示例
    > git init 
    > git remote add origin https://github.com/chthollists/HexoBlogPrivateRepo.git 
    > git checkout -b hexo_source
    > git add . 
    > git commit -m "update hexo_source flies to prviate repo"
    > git push -u origin master
    > ```

  * 第三步：新节点`git clone`该私有仓库，并更新博客，`push`提交，`hexo g -d`部署到`GitHub Pages`上

    > ```bash
    > # 第一次同步时
    > git clone -b <分支，默认master> 仓库地址 <本地存放路径，可省略>
    > npm install
    > 
    > # 非第一次同步
    > git pull # 先拉取，避免缺少文件
    > 
    > # 提交更新
    > git commit -a -m 'content'
    > git push
    > 
    > # 部署到GitPages
    > $ hexo clean
    > $ hexo g # hexo d -g
    > $ hexo d -w # -w用来查看变化
    > ```

---

## 3. 其他问题

### 3.1 新节点拉取文件

* 分支方式：直接`pull`拉取

  > ```bash
  > git pull
  > ```

* 私有仓库方式：使用`fetch`代替`pull`

  > ```bash
  > # 将远程 git 仓库上最新的内容拉取到本地，将本地库所关联的远程库更新至最新
  > git fetch --all
  > # 强制将本地库的内容指向最新远程仓库的master分支   
  > git reset --hard origin/master 
  > ```

### 3.2 同步与主题文件的影响

* 主题文件中`.git`的删除：`rm -rf ./themes/yilia/.git`

  > 由于Git仓库中不能嵌套另一个，所以Hexo源文件中的主题文件需要与GitHub上的主题仓库解除关联，即删除主题文件中的`.git`文件。

* [`Next`主题](https://github.com/Chthollists/hexo-theme-next)的特殊情况：详见[`issue`](https://github.com/theme-next/hexo-theme-next/issues/564#issuecomment-451385682)

  > 对于常用的`Next`主题，除了主题目录下的`.git`文件，在`themes/next/source/lib`下还有一个`.gitignore`文件，该文件会使`git`操作忽略其中设置的文件类型，从而导致部分主题文件没有被同步到GitHub上。此时需要将`themes/next/source/lib`手动复制到不同节点的对应目录下。

* 主题的更新

  > 由于删除了主题的`.git`文件，所以主题无法通过git进行版本控制，此后需要更新主题时，可以在另一个地方`git clone`下来该主题的最新版本，然后将内容拷到当前主题目录。

### 3.3 新节点安装Hexo依赖的问题

* Hexo依赖安装

  * 命令：`npm install`
  * 执行位置：Hexo源文件的根目录下

* 安装第三方插件的问题

  * 受到`node`的版本的影响、跨平台、兼容性的影响
  * 如：`fsevent`不支持`node v11+`版本，[解决方案](https://www.alankeene.com/2019/0102/fsevents-didnot-soppurt-node11.html)

* 解决方法：降低新节点的`node`的版本

  * 小白法：删除后重新安装

  * `nvm`方式：[Node Version Manager](https://github.com/creationix/nvm)

    > Node Version Manager工具用来管理多个版本的node环境，使用`nvm`的命令行可以实现不同版本的node环境的切换。

### 3.4 参考资料

1. [利用Hexo在多台电脑上提交和更新github pages博客 - 简书 (jianshu.com)](https://www.jianshu.com/p/0b1fccce74e0?utm_campaign=maleskine)
2. [利用Hexo在多台电脑上提交和更新博客](https://zhuanlan.zhihu.com/p/55796451)
3. [Hexo博客的跨设备同步](https://cloud.tencent.com/developer/article/1196386?from=article.detail.1046404)
4. [最安全的 hexo 多电脑同步博客解决方案--非新建分支 - 简书 (jianshu.com)](https://www.jianshu.com/p/dddd75f62751?utm_campaign=maleskine)
5. [Hexo博客的安装部署及多电脑同步 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1046404)
6. [多台电脑使用Hexo](https://www.jianshu.com/p/4bcf2848b3fc)

