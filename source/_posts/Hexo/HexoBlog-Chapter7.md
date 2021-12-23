---
title: Hexo博客搭建系列Chapter7——Hexo博客功能扩展(3/5)
toc: true
reward: true
declare: true
gitalkenable: true
categories: 
- others
- Hexo
tags:
  - Hexo
abbrlink: 65de1306
date: 2020-07-31 20:10:53
top:
---

---

* 在Windows系统上利用Hexo搭建博客系列——Chapter7
* Chapter7：
  * 扩展了版权信息功能；参考：[添加版权信息](http://dongshuyan.com/2019/05/24/hexo%E5%8D%9A%E5%AE%A2%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9/#4-%E6%80%8E%E4%B9%88%E7%BD%AE%E9%A1%B6%E6%96%87%E7%AB%A0)
  * 扩展了博客特效：点击爱心、自动光玉；更多特效教程参考：[特效大全](https://yansheng836.github.io/article/cf9c6a5e.html#%E5%8A%A8%E6%80%81%E7%BA%BF%E6%9D%A1%E8%83%8C%E6%99%AF)

<!-- more -->

# Hexo博客搭建系列——Chapter 7

---

## 五、高级功能实现

### 11. 版权信息功能

* 两种添加版权信息的方式
  * 方式一：直接在`articles.ejs`里面添加版权信息
  * 方式二：把版权信息单独写在一个`ejs`文件`declare.ejs`内
  * 两者区别不大

#### 11.1 方式一：直接修改版

* 参考教程：[添加版权信息](http://dongshuyan.com/2019/05/24/hexo%E5%8D%9A%E5%AE%A2%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9/#4-%E6%80%8E%E4%B9%88%E7%BD%AE%E9%A1%B6%E6%96%87%E7%AB%A0)

* 第一步：找到文件`themes/yilia/layout/_partial/article.ejs`，在`div:article-entry`标签后，约第11行处准备添加新内容

  > ```html
  ><div class="article-entry" itemprop="articleBody">
  > <% if (post.excerpt && index){ %>
  > <%- post.excerpt %>
  > <% if (theme.excerpt_link) { %>
  >  <a class="article-more-a" href="<%- url_for(post.path) %>#more"><%= theme.excerpt_link %> >></a>
  >  <% } %>
  >    <% } else { %>
  >  <%- post.content %>
  > <% } %>
  >  
  > <-- 在此处添加代码-->
  > 
  > <% if ((theme.reward_type === 2 || (theme.reward_type === 1 && post.reward)) && !index){ %>
  >    <div class="page-reward">
  > <a href="javascript:;" class="page-reward-btn tooltip-top">
  > <div class="tooltip tooltip-east">
  >  ```
  >  
  
* 第二步：在`article.ejs`的该位置处添加代码

  > ```html
  > <!-- 《添加版权声明 -->
  > <%
  > var sUrl = url.replace(/index\.html$/, '');
  > sUrl = /^(http:|https:)\/\//.test(sUrl) ? sUrl : 'https:' + sUrl;
  > %>
  > 
  > <!-- #版权基础设定：0-关闭声明； 1-文章对应的md文件里有declare: true属性，才有版权声明； 2-所有文章均有版权声明 -->
  > <% if ((theme.declare.declare_type === 2 || (theme.declare.declare_type === 1 && post.declare)) && !index){ %>
  > <div class="declare">
  > <strong class="author">本文作者：</strong>
  > <% if(config.author != undefined){ %>
  > <%= config.author%>
  > <% }else{%>
  > <font color="red">请在博客根目录“_config.yml”中填入正确的“author”</font>
  > <%}%>
  > <br>
  > <strong class="create-time">发布时间：</strong>
  > <%- date(post.date, 'YYYY-MM-DD HH:mm:ss') %>
  > <br>
  > <strong class="update-time">最后更新：</strong>
  > <%- date(post.updated, 'YYYY-MM-DD HH:mm:ss') %>
  > <br>
  > <strong class="article-titles">本文标题：</strong>
  > <a href="<%= config.url%>/<%= post.path %>" title="<%= post.title %>" target="_blank"><%= post.title %></a>
  > <br>
  > <strong class="article-url">本文链接：</strong>
  > <a href="<%=config.url%>/<%= post.path  %>" title="<%= post.title %>" target="_blank"><%= config.url%>/<%= page.path %></a>
  > <br>
  > <strong class="copyright">版权声明：</strong>
  > 本作品采用
  > <a rel="license" href="<%= theme.declare.licensee_url%>" title="<%= theme.declare.licensee_alias %>"><%= theme.declare.licensee_name%></a>
  > 许可协议进行许可。转载请注明作者和出处！
  > <% if(theme.declare.licensee_img != undefined){ %>
  > <br>
  > <a rel="license" href="<%= theme.declare.licensee_url%>"><img alt="知识共享许可协议" style="border-width:0" src="<%= theme.declare.licensee_img%>"/></a>
  > <% } %>
  > </div>
  > <% } else {%>
  > <div class="declare" hidden="hidden"></div>
  > <% } %>
  > <!-- 添加版权声明》 -->
  > ```

* 第三步：创建新文件`themes/yilia/source-src/css/declare.scss`，注意路径

  > ```css
  > .declare {
  > background-color: #eaeaea;
  > margin-top: 2em;
  > border-left: 3px solid #ff1700;
  > padding: .5em 1em;
  > }
  > ```

* 第四步：修改文件`themes/yilia/source-src/css/main.scss`

  > ```css
  > @import "./declare";
  > ```

* 第五步：修改主题配置文件`themes/yilia/_config.yml`

  > ```yml
  > # 版权声明
  > #在需要进行版权声明的文章的md文件头部，设置属性declare: true。
  > #版权基础设定：0-关闭声明； 1-文章对应的md文件里有declare: true属性，才有版权声明； 2-所有文章均有版权声明
  > declare:
  > declare_type: 1
  > licensee_url: https://creativecommons.org/licenses/by-nc-sa/4.0/        #当前应用的版权协议地址。
  > licensee_name: 'CC BY-NC-SA 4.0'                                        #版权协议的名称
  > licensee_alias: '知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议'       # alias别名
  > licensee_img: https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png    #版权协议的Logo
  > ```

* 第六步：然后在需要进行版权声明的文章的`md`文件头部，设置属性declare

  > ```yaml
  > declare:true
  > ```

#### 11.2 方式二：新建文件版

* 参考教程：[添加版权信息](https://blog.csdn.net/qq_40922859/article/details/97035497?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param)

* 第一步：新建文件`\themes\yilia\layout\_partial\post\declare.ejs`，注意路径

  > ```html
  ><!-- 《添加版权声明 -->
  > <%
  > var sUrl = url.replace(/index\.html$/, '');
  > sUrl = /^(http:|https:)\/\//.test(sUrl) ? sUrl : 'https:' + sUrl;
  > %>
  > 
  > <!-- #版权基础设定：0-关闭声明； 1-文章对应的md文件里有declare: true属性，才有版权声明； 2-所有文章均有版权声明 -->
  > <% if ((theme.declare.declare_type === 2 || (theme.declare.declare_type === 1 && post.declare)) && !index){ %>
  > <div class="declare">
  > <strong class="author">本文作者：</strong>
  > <% if(config.author != undefined){ %>
  >  <%= config.author%>
  >  <% }else{%>
  >    <font color="red">请在博客根目录“_config.yml”中填入正确的“author”</font>
  >  <%}%>
  >    <br>
  >  <strong class="create-time">发布时间：</strong>
  >  <%- date(post.date, 'YYYY-MM-DD HH:mm:ss') %>
  >  <br>
  >  <strong class="update-time">最后更新：</strong>
  >  <%- date(post.updated, 'YYYY-MM-DD HH:mm:ss') %>
  >  <br>
  >  <strong class="article-titles">本文标题：</strong>
  >  <a href="<%= config.url%>/<%= post.path %>" title="<%= post.title %>" target="_blank"><%= post.title %></a>
  >  <br>
  >  <strong class="article-url">本文链接：</strong>
  >  <a href="<%= config.url%>/<%= post.path  %>" title="<%= post.title %>" target="_blank"><%= config.url%>/<%= page.path %></a>
  >  <br>
  >  <strong class="copyright">版权声明：</strong>
  >  本作品采用
  >  <a rel="license" href="<%= theme.declare.licensee_url%>" title="<%= theme.declare.licensee_alias %>"><%= theme.declare.licensee_name%></a>
  >  许可协议进行许可。转载请注明作者和出处！
  >  <% if(theme.declare.licensee_img != undefined){ %>
  >  <br>
  >  <!--版权logo如果加载不出来可以注释掉-->
  >    <a rel="license" href="<%= theme.declare.licensee_url%>"><img alt="知识共享许可协议" style="border-width:0" src="<%= theme.declare.licensee_img%>"/></a>
  >   <% } %>
  >    </div>
  >  <% } else {%>
  > <div class="declare" hidden="hidden"></div>
  > <% } %>
  > <!-- 添加版权声明》 -->     
  > ```
  
* 第二步：在文件`themes\yilia\layout\_partial\article.ejs`中添加对`declare.ejs`文件的引用

  > ```html
  ><!-- 《添加版权声明 -->
  >   <% if(theme.declare){%>
  >       <%- partial('post/declare') %>
  >      <% } %>
  >    <!-- 添加版权声明》 -->
  >    ```
  
* 第三步：在文件`\themes\yilia\source\main.xxxxx.css`的底部添加样式

  > ```css
  >/* 版权声明 */
  > .declare {
  > 	background-color: #eaeaea;
  > 	margin-top: 2em;
  > 	border-left: 3px solid #ff1700;
  > 	padding: .5em 1em;
  > }
  > ul.declare li {
  > font-size: 19px;
  > font-weight: bold;
  >  line-height: 1.5;
  >  }
  >  /* 版权声明移动端 */
  > @media screen and (max-width:800px){
  > 	li.declare_article-url{
  > 		display: none;
  > 	}
  > ul.declare li{
  > 		font-weight: normal;
  > 		font-size: 16px;
  > 	}
  > }
  > ```
  
* 第四步：修改主题配置文件`\themes\yilia\_config.yml`，在里面添加

  > ```yml
  ># 版权声明
  > #在需要进行版权声明的文章的md文件头部，设置属性declare: true。
  > #版权基础设定：0-关闭声明； 1-文章对应的md文件里有declare: true属性，才有版权声明； 2-所有文章均有版权声明
  > declare:
  > declare_type: 1
  > licensee_url: https://creativecommons.org/licenses/by-nc-sa/4.0/        #当前应用的版权协议地址。
  > licensee_name: 'CC BY-NC-SA 4.0'                                        #版权协议的名称
  > licensee_alias: '知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议'       # alias别名
  > licensee_img: https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png    #版权协议的Logo
  > ```
  
* 第五步：在需要进行版权声明的文章的md文件头部，设置属性`copyright`

  > ```yml
  > declare: true
  > ```

* 注意：

  * 第二种方法中，没有新建`declare.sccs`文件，而是直接将版权信息的显示格式设置放在了`\themes\yilia\source\main.xxxxx.css`中

  * 版权协议的Logo所在网址需要人机验证，所以获取不到Logo时可以手动登录[协议Logo](https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png)进行验证，从而使得Logo可以被获取

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

* 第一步：新建`js`文件：`\yilia\source\`路径下新建`clicklove.js`

  > ```js
  > // clicklove.js
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

* 第二步：修改`ejs`文件，找到`themes\yilia\layout\_partial\after-footer.ejs`

  > ```html
  > <!-- src路径要对应之前创建clicklove.ejs的地方 -->
  > <!--页面点击小红心-->
  > <script type="text/javascript" src="/clicklove.js"></script>
  > ```
  >

#### 12.2 光玉特效

* 第一步：新建`snow.js`文件，路径：`themes\yilia\source\`

  > ```js
  > var snow = {
  > info: {
  >  top: 0,
  >  left: 0,
  >  zIndex: 500,
  >  number: 70
  > },
  > down: function () {
  >  var f = window.innerWidth;
  >  var g = window.innerHeight;
  >  var d = document.createElement("canvas");
  >  d.style.position = "fixed";
  >  d.style.pointerEvents = "none";
  >  d.style.top = snow.info.top + "px";
  >  d.style.left = snow.info.left + "px";
  >  d.style.zIndex = snow.info.zIndex;
  >  d.width = f;
  >  d.height = g;
  >  document.body.appendChild(d);
  >  var c = [];
  >  for (var a = 0; a < snow.info.number; a++) {
  >    c.push({
  >      x: Math.random() * f,
  >      y: Math.random() * g,
  >      r: Math.random() * 4 + 1,
  >      n: Math.random() * 70
  >    })
  >  }
  >  var b = d.getContext("2d");
  >  var e = 0;
  >  setInterval(function () {
  >      b.clearRect(0, 0, f, g);
  >      b.fillStyle = "rgba(233,235,154, 1)";
  >      b.shadowBlur = 5;
  >      b.shadowColor = "rgba(250,250,210 2)";
  >      b.beginPath();
  >      for (var j = 0; j < 70; j++) {
  >        var h = c[j];
  >        b.moveTo(h.x, h.y);
  >        b.arc(h.x, h.y, h.r, 0, Math.PI * 2, 0)
  >      }
  >      b.fill();
  >      e += 0.01;
  >      for (var j = 0; j < 70; j++) {
  >        var h = c[j];
  >        h.y += Math.cos(e + h.n) + h.r / 2;
  >        h.x += Math.sin(e) * 2;
  >        if (h.x > f + 5 || h.x < -5 || h.y > g) {
  >          c[j] = j % 3 > 0 ? {
  >            x: Math.random() * f,
  >            y: -10,
  >            r: h.r,
  >            n: h.n
  >          } : Math.sin(e) > 0 ? {
  >            x: -5,
  >            y: Math.random() * g,
  >            r: h.r,
  >            n: h.n
  >          } : {
  >            x: f + 5,
  >            y: Math.random() * g,
  >            r: h.r,
  >            n: h.n
  >          }
  >        }
  >      }
  >    },
  >    15)
  > }
  > };
  > // yilia-plus主题已经集成了，可以直接用
  > ```

* 第二步：修改`themes\yilia\layout\layout.ejs`文件，添加

  > ```html
  ><!--雪花特效-->
  > <% if (theme.snow){ %>
  > <script type="text/javascript" src="<%- url_for('snow.js') %>"></script>
  > <script type="text/javascript" src="<%- url_for('jquery-2.1.4.min.js') %>"></script>
  > <script>
  > snow.down();
  > $(window).resize(function() {
  >  $("canvas").css("z-index","500").remove();
  >  snow.down();
  >    });
  >    </script>
  >  <% } %>
  > ```
  
* 第三步：修改主题配置文件`themes\yilia\_config.yml`

  > ```yml
  > # 飘雪特效
  > # https://github.com/MlgmXyysd/snow.js
  > snow: true
  > ```

---

