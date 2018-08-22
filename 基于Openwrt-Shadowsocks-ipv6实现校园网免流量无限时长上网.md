title: 基于Openwrt+Shadowsocks+ipv6实现校园网免流量无限时长上网
date: 2016-09-24 10:46:02
update: ""
author: me
tags:
- life
- Hacker
- 生活
categories:
- 生活
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---



> 转载自[Dyhube](http://www.jianshu.com/p/4d44172f1a5b)

# 简述

笔者利用笔记本电脑实现ipv6免费上网已经有一段时间了，原理是通过ipv6访问ipv4资源，在学校网络不限流量、不限时长、20兆带宽（我们学校ipv6限速上下对等20兆，没办法！）,电脑开热点全寝室共用，那真是爽翻天 !

![ipv6](http://7xusrl.com1.z0.glb.clouddn.com/ipv6.png)

<!--more-->

但是每天回到寝室总是打开电脑开热点还真是蛋疼的事情。再说电脑也不能总是开着吧。这时我就想能不能找个路由器，一天二十四小时开机，电脑、手机、平板随时都可以连。这个想法大概出现在半年前，由于手里没有路由器，就一直没弄，但是网上是有各种成功的案例的。

前段时间手里终于进了台K1，由于之前已经查了相当多的教程，所以就顺风顺水，很快就成功了。下面我就主要讲一下openwrt客户端的配置问题。

# 意义

在大部分高校，ipv4一般是计流量或计时收费的，（笔者学校就是计时收费的，50元200小时网通十兆带宽）而且，由于校园的特殊性，相应的价格也比市面上宽带服务商要高。万幸的是，这些高校一般具有ipv6网络环境，并且由于国家的大力支持，普及范围广，而且不计算流量，聪明的人早就想能不能通过利用ipv6已达到免流量及无限时长上网？答案是可行的，鉴于目前公网的环境普遍是ipv4，我们可以找一台同时具有ipv4和ipv6地址的服务器，我们在校内通过ipv6访问服务器，然后服务器处理我们的访问请求以`ipv4/ipv6双栈`的方式代替我们访问互联网，再将数据通过ipv6反馈给我们，从而到达免流上网的目的。并且，考虑到大部分高校ipv6没有限制速度，理论上可以达到服务器出口的带宽，当然，具体取决于你们学校的ipv6出口带宽。

# 为什么用Shadowsocks？

配置简单，真的简单！以前看到过信息学院的学长写的一篇blog,原理是`ipv6 to ipv4` 从而ipv6 to ipv4网络,其实原理是一样的，只是他用了`openvpn`这个软件，但是感觉实现起来好难。像这样的开源支持ipv6协议的软件还是有很多的，这里就不再陈述。

回到原题为什么用Shadowsocks，配置简单。vps服务提供商[搬瓦工](https://bandwagonhost.com/)现在为了迎合国人的需求现在已经预配了Shadowsocks,只需要点击以下安装就ok了。

![1](http://7xusrl.com1.z0.glb.clouddn.com/877518-ab3dea8d36104b08.png)

![2](http://7xusrl.com1.z0.glb.clouddn.com/877518-ab3dea8d36104b08.png)

# 适用对象

具有`ipv6地址`、`ipv4流量`（计时）收费贵爱折腾的大学生。不推荐打国服游戏，延迟你懂的，但对延迟没要求的游戏还是可以玩的，美服、亚服、台服随你玩。

# 准备
## openwrt固件路由器
路由器的刷机请自行Google,教程一大堆，刷机时笔者也遇到过很多问题，坚持！如果你的也是K1路由器，也要刷机，不妨看这个[教程](http://akkuman.coding.me/2016/09/22/%E7%BB%99%E6%96%90%E8%AE%AFK1%E5%88%B7%E6%9C%BA%E5%B9%B6%E6%8B%A8%E5%8F%B7e%E4%BF%A1-%E6%B9%96%E5%8C%97%E5%9C%B0%E5%8C%BA%E6%B5%8B%E8%AF%95%E6%97%A0%E9%97%AE%E9%A2%98/)。刷机的重点是刷`Shadowsocks插件`，我的K1直接刷的来自恩山网友的固件，固件里已经附带了Shadowsocks。[openwrt](http://pan.baidu.com/s/1dFJO4hF)固件自取。openwrt控制面板上图。

![3](http://7xusrl.com1.z0.glb.clouddn.com/877518-ed11845c67728119.png)

## Shadowsocks+ipv6节点信息
因为笔者手里有台美国的vps，并且配置了Shadowsocks，所以现在拿来就直接用，老实说搭建的Shadowsocks平常很少用，之前觉得租这个vps很是浪费。但是自从寝室里有了这台全天候开机的路由器，值了！在这里我要强调一下，Shadowsocks的节点我们需要ipv6地址的，不然还是没法走校内的ipv6通道。

# 前方高能预警

## 操作

首先openwrt固件路由器登陆`192.168.1.1`，初始登录默认密码是：`admin`。登进去之后最好先不要对任何东西改动，按照正常路由器的配置对路由器进行拨号上网。然后选择`Shadowsocks插件`，选择`启动`。（为什么这样做呢？笔者尝试了几下，不拨号上网的话，`Shadowsocks`和`DNS`配置好了以后无法上网，最后总结，先拨号上网、再配置`Shadowsocks`和`DNS`信息）

步骤：点击 `openwrt服务`>`Shadowsocks`，出现以下界面。

![4](http://7xusrl.com1.z0.glb.clouddn.com/877518-8304c61781a62c95.png)

## Shadowsocks的配置
```
服务器ip：  
密码：  
服务器端口：  
加密方式：  
```
对Shadowsocks配置好了以后，点击下面的透明代理，选择`启动`。

![5](http://7xusrl.com1.z0.glb.clouddn.com/877518-b2304b09473a3a76.png)

对Shadowsocks配置好以后，我们的任务还没有结束，最重要的就是配置`DNS信息`。这里如果不配置DNS，IP地址选择ipv4的，Shadowsocks是国外的，那么通过这种方式使用Shadowsocks就是通过路由器来翻fq，在这里我就不多说了。

## DNS的配置

DNS设置有两种方案，一种是利用`ChinaDNS`，还有一种直接在`DHCP/DNS`设置页面（`网络>DHCP/DNS`）进行填写。

由于本次折腾的特殊性，路由器工作在`纯ipv6环境`下，也就是说路由器没有ipv4的网络，但常用的DNS服务器大多是以ipv4地址方式提供的，如果使用ipv4的DNS服务器就会导致无法解析。此处用了`[2001:470:0:c0::2]`，但是很不幸，该DNS被污染了，无法解析如google，youtube一类网址，但是对国内的网站的解析很好。


```
2001:470:0:c0::2
```

![6](http://7xusrl.com1.z0.glb.clouddn.com/877518-a819f528346ea980.png)

其他的DNS最好选择Google的，相对的来说，网站解析最全面，而且还可以fq,只是一部分了，选择Google的公共DNS有一个缺点，就是像移动端的微信或者qq了，朋友圈的信息或公众号加载不出来，这是很蛋疼的事情。个人还是推荐上面的那条DNS,速度快、国内网站全面，几乎全覆盖。

下面是一些从网上找来的公共DNS，可以试验一下，说不定有什么以外的收获呢。

```
ordns.he.net  2001:470:20::2     74.82.42.42

tserv1.fmt2.he.net  2001:470:0:45::2   72.52.104.74

tserv1.dal1.he.net  2001:470:0:78::2   216.218.224.42

tserv1.ams1.he.net  2001:470:0:7d::2   216.66.84.46

tserv1.mia1.he.net  2001:470:0:8c::2   209.51.161.58

tserv1.tor1.he.net  2001:470:0:c0::2   216.66.38.58

ns.ipv6.uni-leipzig.de  2001:638:902:1::10   139.18.25.34
```

## Google Public DNS

```
google-public-dns-a.google.com  2001:4860:4860::8888   8.8.8.8

google-public-dns-b.google.com  2001:4860:4860::8844   8.8.4.4
```

码字不容易，在这里非常感谢[_Echo](http://www.jianshu.com/p/6559d6e4e7ab)和[张哲](https://www.zhangzhe.info/2016/03/openwrt-shadowsocks-ipv6/)两人的post.


> 转载自[Dyhube](http://www.jianshu.com/p/4d44172f1a5b)
