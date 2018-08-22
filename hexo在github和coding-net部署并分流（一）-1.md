title: hexo在github和coding.net部署并分流（一）
date: 2017-01-10 21:22:00
update: ""
author: Akkuman
tags:
- Hexo
categories:
- Hexo
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---


# 安装GIT和Node.JS
首先在自己的电脑上安装好git和node.js，这一步怎么做自己搜索，安装软件都是下一步下一步，应该不难,GIT安装完成后打开git cmd输入
```bash
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```
因为Git是分布式版本控制系统，所以，每个机器都必须自报家门：你的名字和Email地址。
**注意：**git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

<!--more-->

#安装并初始化HEXO
如果你是在Windows上，请打开Git-CMD
![1](http://7xusrl.com1.z0.glb.clouddn.com/git-cmd-exa.png)
假如你是想在D:\blog\下建立你的博客，请先在D盘下新建文件夹blog
在Git-CMD中输入`npm install -g hexo-cli`回车开始安装hexo
安装完成后将git cmd工作目录切换至D:\blog\然后输入`hexo init`回车，或者直接在git cmd中输入`hexo init d:\\blog`
如果你的d:\blog\下的目录形式是
```
.
├── _config.yml // 网站的配置信息，你可以在此配置大部分的参数。
├── package.json 
├── scaffolds // 模板文件夹。当你新建文章时，Hexo会根据scaffold来建立文件。
├── source // 存放用户资源的地方
|   ├── _drafts
|   └── _posts
└── themes // 存放网站的主题。Hexo会根据主题来生成静态页面。
```
那么你的hexo安装并初始化完成
然后输入`hexo server`启动本地demo，打开浏览器，查看http://localhost:4000/可以看到自己的博客

# 将之托管到github和coding上
## github项目创建
1.注册github账号
2.创建项目仓库
进入[github.com](https://github.com/)，然后点击右上角 + -->new repository

![2](http://7xusrl.com1.z0.glb.clouddn.com/new%20rep.png)

3.在Repository name中填写Github账号名.github.io，点击Create repository，完成创建。

![3](http://7xusrl.com1.z0.glb.clouddn.com/hexo-github-1.png)

## Coding项目创建
1.注册Coding账号
2.创建项目仓库

![4](http://7xusrl.com1.z0.glb.clouddn.com/hexo-coding-1.png)

3.填写项目名称描述创建即可

![5](http://7xusrl.com1.z0.glb.clouddn.com/hexo-coding-2.png)

## 配置SHH

配置shh key是让本地git项目与远程的github建立联系
1.检查是否已经有SSH Key，打开Git Bash，输入
```bash
cd ~/.ssh
```
2.如果没有.ssh这个目录，则生成一个新的SSH，输入
```bash
ssh-keygen -t rsa -C "your e-mail"
```
注意1: 此处的邮箱地址，你可以输入自己的邮箱地址；注意2: 此处的「-C」的是大写的「C」
接下来几步都直接按回车键,然后系统会要你输入密码
```bash
Enter passphrase (empty for no passphrase):<输入加密串>
Enter same passphrase again:<再次输入加密串>
```
这个密码会在你提交项目时使用，如果为空的话提交项目时则不用输入。这个设置是防止别人往你的项目里提交内容。个人建议为空比较方便
注意：输入密码的时候没有*字样的，你直接输入就可以了。 
3.最后看到这样的界面，就成功设置ssh key了 
![6](http://7xusrl.com1.z0.glb.clouddn.com/wangzhanssh%20key.jpg)

## 添加 SSH Key 到 GitHub和Coding
复制`~/.ssh/id_rsa.pub`中的内容
~是个人文件夹，比如我的电脑上是C:\Users\Administrator\.ssh\id_rsa.pub，将其中的文本复制
进入github，点击头像-->Setting-->SSH and GPG keys,然后在右侧点击New SSH key，
Title随便写，key中填写id_rsa.pub中复制的内容，然后Add SSH key就ok了
进入Coding.net，点击头像-->个人设置-->SSH公钥，新增公钥，公钥名称随便，公钥内容是填写id_rsa.pub中复制的内容，有效期可以勾选永久，然后添加ok

## 测试SSH是否配置成功

1.打开Git Bash，然后输入
```bash
ssh -T git@github.com
```
如配置了密码则要输入密码,输完按回车
如果显示以下内容，则说明Github中的ssh配置成功。
```bash
Hi username! You've successfully authenticated, but GitHub does not
provide shell access.
```
2.再输入
```bash
ssh -T git@git.coding.net
```
如果显示以下则说明coding中的ssh配置成功
```bash
Hello username You've connected to Coding.net by SSH successfully!
```
## 创建Github Pages和Coding Pages 服务
1.GitHub Pages分两种，一种是你的GitHub用户名建立的username.github.io这样的用户&组织页（站），另一种是依附项目的pages。想建立个人博客是用的第一种，形如cnfeat.github.io这样的可访问的站，每个用户名下面只能建立一个。
Coding Pages服务开启在官网说的很详细，不知道请百度
2.打开D:\blog文件夹中的_config.yml文件，找到如下位置，填写
```bash
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
- type: git
  repo: 
    github: git@github.com:yourname/yourname.github.io.git,master
    coding: git@git.coding.net:yourname/yourname.git,coding-pages
```
**注：** (1) 其中yourname替换成你的Github账户名;(2)注意在yml文件中，:后面都是要带空格的。
#部署完成
在blog文件夹中空白处右击打开Git Bash输入
```bash
hexo clean
hexo d- g
```
此时，通过访问http://yourname.github.io和http://yourname.coding.me可以看到默认的Hexo首页面（与之前本地测试时一样）。
