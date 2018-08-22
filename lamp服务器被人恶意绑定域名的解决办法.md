title: lamp服务器被人恶意绑定域名的解决办法
date: 2016-05-28 22:09:02
update: ""
author: me
tags:
- Linux
categories:
- Linux
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---


# 还没开始就被别人绑定了域名

## 事情的起因与发现
刚买了个服务器搭建了一个dz，想着域名还没备案，就先搭建了起来，然后在做DDOS测试时偶然发现服务器被别人恶意把域名绑定了

## 最初的解决方案
没管。。。。。。
后来发现有影响，朋友也一直给我说叫我整下

## 利用重定向把恶意指向过来的域名指到别处
要利用301重定向，首先我们要在Apache上配置一下，Apache默认是不开启.htaccess的


<!--more-->


----------


### 0x01.编辑httpd.conf文件

打开/etc/httpd/conf目录下的httpd.conf文件，找到这一行：
```bash
LoadModule rewrite_module modules/mod_rewrite.so
```
>  当然，你得确定你的/etc/httpd/modules下有mod_rewrite.so这个文件
> ```bash
> ls /etc/httpd/modules | grep mod_rewrite
> ```
如果你没有找到这一行，记得在httpd.conf文件里直接添加这一行


----------


### 0x02.设置AllowOverride

同样的在httpd.conf文件中找到：
```bash
<Directory "/var/www/html">
    #
    # Possible values for the Options directive are "None", "All",
    # or any combination of:
    #   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
    #
    # Note that "MultiViews" must be named *explicitly* --- "Options All"
    # doesn't give it to you.
    #
    # The Options directive is both complicated and important.  Please see
    # http://httpd.apache.org/docs/2.4/mod/core.html#options
    # for more information.
    #
    Options Indexes FollowSymLinks

    #
    # AllowOverride controls what directives may be placed in .htaccess files.
    # It can be "All", "None", or any combination of the keywords:
    #   Options FileInfo AuthConfig Limit
    #
    AllowOverride All

    #
    # Controls who can get stuff from this server.
    #
    Require all granted
</Directory>
```
或者它长这个样子：
```bash
<Directory />
Options FollowSymLinks
AllowOverride None
</Directory>
```
什么，你告诉我还是找不到？？？
那教你一个办法
锁定关键词`FollowSymLinks`和`AllowOverride None`

> vi的向下查找命令是`:/你要查找的`
> vi的向上查找命令是`:?你要查找的`
> n是下一个
> N是上一个

相信你已经找到了
接下来把`None`改成`All`


----------


### 0x03.编写规则文件.htaccess
跑去网站根目录下，比如我的是/var/www/html
如果存在.htaccess，忽略下一步，直接打开编辑
然后新建.htaccess文件`touch .htaccess`
编辑.htaccess文件`vi .htaccess`
添加如下规则
```bash
RewriteEngine on
RewriteCond %{HTTP_HOST} ^别人的域名.com$ [OR]
RewriteCond %{HTTP_HOST} ^www.别人的域名.com$
RewriteRule ^(.*)$ http://www.自己的域名.com/$1 [R=301,L]
```


----------


# 个人的修改
我知道，你在网上所找到的方法都是上面那种代码，并且应该都没有提 教你怎么开启.htaccess
![去他爹的](http://7xusrl.com1.z0.glb.clouddn.com/%E6%9A%B4%E6%BC%AB%E5%8E%BB%E4%BB%96%E7%88%B9%E7%9A%84%E9%80%BB%E8%BE%91.jpg)
但是本人实验过，这配置进去还有问题，设置重启Apache后，访问网站提示500错误
![500error](http://7xusrl.com1.z0.glb.clouddn.com/500error.png)
机智的我总要查看日志啊
```bash
cat /var/log/messages | grep httpd
```
找到了错误
![httpderror](http://7xusrl.com1.z0.glb.clouddn.com/QQ%E5%9B%BE%E7%89%8720160528214617.png)
英语不太好，但是大致知道是服务器没有限定域名，需要修改ServerName,而ServerName字段值在httpd.conf中是被注释掉的
我们在httpd.conf修改它
```bash
#ServerName: www.example.com:80
```
改为
```bash
ServerName: 115.**.**.57:80
```
然后重启Apache，可以访问了


----------


# 后续
好的故事都会有后续的

以为这样就万事大吉了?

但是我这个被坑得不轻
admin.xx.com都被他解析到我服务器上来了

老衲怎么破
![成龙挠头](http://7xusrl.com1.z0.glb.clouddn.com/%E6%9A%B4%E6%BC%AB%E6%88%90%E9%BE%99.jpg)
.htaccess好像可以用正则表达式，一查，果然
那就改一下.htaccess咯
![shaxiao](http://7xusrl.com1.z0.glb.clouddn.com/%E6%9A%B4%E6%BC%AB%E5%82%BB%E7%AC%91.jpg)
```bash
RewriteEngine on
RewriteCond %{HTTP_HOST} ^别人的域名.com$ [OR]
RewriteCond %{HTTP_HOST} ^.*.别人的域名.com$
RewriteRule ^(.*)$ http://www.自己的域名.com/$1 [R=301,L]
```
机智的你已经发现第三行中的www被我改成了.*，就是匹配0个或者多个字符，当然*你可以改成+

然后重启Apache

```bash
systemctl restart httpd
```
或者
```bash
service httpd restart
```

现在我再访问。。。嘿嘿嘿，被我跳转到百度了
![heihei](http://7xusrl.com1.z0.glb.clouddn.com/%E6%9A%B4%E6%BC%AB%E5%98%BF%E5%98%BF%E5%98%BF.jpeg)


----------


## 思考
当然，还有其他的方法，自己也可以去网上找找
对了，那个刚才在httpd.conf里换ip的地方也可换自己的域名，因为我的还在备案，就没改
