title: 推荐一个静态博客兼笔记的工具：WDTP
date: 2017-04-01 20:27:51
update: ""
author: me
tags:
- 推荐向
categories:
- 推荐向
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---


## 简介
WDTP（山湖录）不止是一款开源免费的GUI桌面单机版静态网站生成器和简单方便的前端开发工具，更是一款跨平台的集笔记、录音、个人知识管理、写作/创作、博客/网站内容与样式管理等功能于一体的多合一内容处理/管理器，同时还是一款高度追求用户体验的Markdown文本编辑器和一款方便强大的录音机。本软件研发的核心思想是：**简洁高效、轻灵优雅、先进强悍、操作简单**。

<!--more-->

![](http://underwaysoft.com/works/wdtp/media/wdtp-main.jpg)

WDTP（山湖录）可运行于macOS和Windows系统下，旨在提高这两大平台下所有写作/分享者的生产力及生产效率，节约耗时，减少无谓的智能、体力与资源消耗。它适合于以下群体：

- 以**文字、声音、图片、视频为主要内容**的写作/记录/创作/分享者
- 职业或业余作家、小说家、编剧、技术类图书的作者及编撰者
- 经常记笔记或写点东西的人
- 写作极客
- 打算采用静态页面的个人博客
- 打算采用静态页面的中小企业
WDTP的全名是：**Walden Tips**，中文名称：**山湖录**，UnderwaySoft开发出品。设计、编程及维护：SwingCoder。立项日期：2016年8月2日，第一个内测版发布日期：2017年2月3日。

## 核心功能
+ 创作。对职业作家（特别是技术作家以及需要大量构思与情节编排的文艺作家）来说比Pages、Word等WYSWYG类型的桌面文字软件更加高效、简洁和灵活的内容创作、章节管理与格式化排版工具。可方便地实现多章节（情节、场景、概念、故事主线等）并发创作/编辑、任意调序、随意归类等强大功能，完稿后一键即可成书。
+ 笔记。可随时记录并管理学习笔记、读书笔记以及有一定篇幅并打算结构化保存、管理、检视和封装的零星随记、杂感等等。可定期将所有或任意分类（目录）下的笔记“装订成册”、集中输出，一键即可完成。
+ 建站。强大而新颖的静态网站维护、编辑、生成、代码调试与内容、结构管理系统。特别适合追求全站真正静态化、内容至上的个人博客与中小企业官网。
+ Markdown编辑器。在保留并规范了大部分“正统”Markdown语法的基础上，WDTP根据大多数作者/作家的实际需求，增加了一批非常实用的新文本标记语法。比如：插入图注和表注、居中、靠右、多种类型的表格、图文混排、插入音视频媒体文件、内容注释、跨文档扩展标记等等。该编辑器针对Windows系统和macOS系统（非Retina显示屏）对中文字体的渲染结果不尽人意等情况专门做了特殊优化与调整，使用户在输入、编辑时可获得更良好的体验。
+ 以上几项，不仅可以文字输入，更可以语音输入，直接记录声音。这一点对不擅长文字表达的朋友或者记者、演员、各类主持人、音乐家、演奏家等群体来说非常方便。
+ WDTP还有极具实用价值的“复习/提醒”功能，文档隐身功能，文档缩略语功能和极其强悍的“智库”架构。
其他更多……

在“笔记、写书、建站/博客、前端开发”这几个方面，WDTP（山湖录）无缝集成，一键切换。即：同一套内容，随时可生成上述任何一种类型，还可多种类型混合使用。

**程序采用c++语言编写，作者同时也是我十分敬重的一位程序员，如果想查看更多信息请访问他的[项目主页](http://underwaysoft.com/works/wdtp/index.html)程序开源[github地址](https://github.com/LegendRhine/WDTP)**

## 上手使用
本来想说更多的，但是确实这款软件和其他的静态博客生成器不一样，拥有着方便的界面，支持english和中文，设置里面即可切换，相信只要你使用过，你就会使用它，能感受到他的方便快捷，如果想看更多玩法和说明请查看[项目主页](http://underwaysoft.com/works/wdtp/index.html)，现在只支持两种模板book(用来作为笔记)和blog(用来生成静态博客)，不过作者说会逐渐增加主题，真的除了暂时主题匮乏之外(会前端的可以自己改改主题)，其他的功能相比于其他的静态博客生成器方便得不是一丁半点

**那么生成静态文件之后如何上传到自己的vps或者github pages或者coding pages呢？**
### 上传到vps
这个你可以使用常规的FTP或者Rsync或者其他方法上传，不过我推荐自己的做法(使用Resilio Sync)
如果你的服务器是windows那么你只需要去[Resilio Sync官网](https://www.resilio.com/individuals/)下载，建议安装为服务，然后访问本机sync服务的网址，点击右上角添加文件夹添加你的网站根目录，然后复制读写key，本机安装Resilio sync客户端然后手动连接这个key到你的静态文件目录，具体可以查资料，这个不难
如果你的服务器是Linux，可以查看Resilio Sync网站上面的[How to install Sync Package on Linux](https://help.getsync.com/hc/en-us/articles/206178924)，说明比较详细，安装好之后和上面的步骤一样，然后只要你本机挂着resilio sync软件，生成就可以即时同步。
trust me， 你将找到这个软件(Resilio Sync)更多的玩法，这软件之前的名字是btsync
当然，这只是我自己使用的方法，你也可以使用其他方法
至于上传到github pages或者coding pages，这个你需要会用git，进入静态文件目录，然后bash下执行
```bash
git init
git add .
git commit //命令给文件一个仓库标记，做为仓库历史，便于以后在远程端查找
git remote add origin git@github.com:username/username.github.io.git
```
git@github.com:username/username.github.io.git的是你的git远端地址，至于为什么用这个是因为ssh创建公钥之后不用重复输入密码
**注: **[如何生成ssh公钥](https://coding.net/help/doc/account/ssh-key.html)这篇文章是以coding.net为例，不过你生成的id_rsa.pub内容同时也可以添加到github，基本相同的步骤，如果有什么疑问可以百度一下关键词为`github ssh公钥 配置`

### 添加评论功能
- 如果你不愿意麻烦可以使用邮箱来收集评论
打开qq邮箱点击上方**设置->账户->邮我->使用邮我**
然后获取代码
![snipaste_20170401_200420.png](https://ooo.0o0.ooo/2017/04/01/58df97595ea80.png)
复制`<a target="_blank" href="http://mail.qq.com/cgi-bin/qm_share?t=qm_mailme&email=64qAgJ6GioWYq5qaxYiEhg" style="text-decoration:none;">`
然后打开你的项目文件夹/themes/blog/article.html，把相应的地方改为下面例子这样
```html
<div class=page_navi align="center">
    <b><a target="_blank" href="http://mail.qq.com/cgi-bin/qm_share?t=qm_mailme&email=64qAgJ6GioWYq5qaxYiEhg" style="text-decoration:none;">评论/咨询/讨论/留言</a></b>
</div>
```
然后别人点击评论就可以打开给你发邮件的入口

- 如果你想添加社会化评论系统
鉴于多说即将关闭，国内没被墙的无需北岸的第三方评论已经很少了，这里我用[来必力](https://livere.com)做例子
1. 注册登录(如果chrome浏览器注册之后一直登录不了请使用火狐)
2. 点击顶栏安装，然后填好相关信息获取代码
![snipaste_20170401_201601.png](https://ooo.0o0.ooo/2017/04/01/58df9a0fc3eb5.png)
3. 然后打开你的项目文件夹/themes/blog/article.html，把原先的评论代码删除掉，在合适的地方插入上方代码，我插入完之后的article.html例子如下
```html
<!doctype html>
<html lang="en">
 <head>
  <meta charset="UTF-8">
  <meta name="Generator" content="WDTP by UnderwaySoft">
  <meta name="Author" content="{{author}}">
  <meta name="Keywords" content="{{keywords}}">
  <meta name="Description" content="{{description}}">
  <link rel="stylesheet" type="text/css" href="{{siteRelativeRootPath}}add-in/style.css"/>
  <title>{{title}}</title>
 </head>
 <body>
  <p> 
  {{siteLogo}}
  {{siteMenu}}
  <hr>
  {{siteNavi}}
  {{content}}
  <hr>
  {{createAndModifyTime}}

    <div align=center><h5><p style="background:PowderBlue">
	    本文版权：{{siteLink}} &emsp;
		共享协议：<a href='http://creativecommons.org/licenses/by-nc-nd/2.5/deed.zh' target='_blank'>署名-非商业使用-禁止演绎</a></h5>
    </div>

  <hr>
  {{previousAndNext}}

  {{ad}}
  <p>
  {{random}}
  <hr>
	<!-- 来必力City版安装代码 -->
	<div id="lv-container" data-id="city" data-uid="MTAyMC8yODAwMC80NTc3">
		<script type="text/javascript">
			(function(d, s) {
				var j, e = d.getElementsByTagName(s)[0];

				if (typeof LivereTower === 'function') { return; }

				j = d.createElement(s);
				j.src = 'https://cdn-city.livere.com/js/embed.dist.js';
				j.async = true;

				e.parentNode.insertBefore(j, e);
			})(document, 'script');
		</script>
	<noscript> 为正常使用来必力评论功能请激活JavaScript</noscript>
	</div>
	<!-- City版安装代码已完成 -->
  {{contact}}
  {{bottomCopyright}}

  </body>
</html>
```
4. 最后的效果如图
![snipaste_20170401_201926.png](https://ooo.0o0.ooo/2017/04/01/58df9adb69d5f.png)

### 最后要说的
这个工具确实是十分方便的，如果你作为笔记，可以使用坚果云来同步，同时它可以打包你的数据，多说无益，试用之后你会感受到它的强大



**本文部分转自[Underwaysoft](http://underwaysoft.com/works/wdtp/download.html)**
