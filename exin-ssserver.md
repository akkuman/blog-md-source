title: e信与酸酸结合开wifi使用路由器上网
date: 2017-11-12 11:21:43
update: ""
author: me
tags:
- life
categories:
- life
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---


关于e信“正常情况下”使用路由器网上是有方法的，入户线插上lan，电脑接lan拨号
我想要说的是连接e信后使用路由器上网，并且是绝对正常的思维
<!--more-->
手机也是可以连接上wifi，但是手机上连接wifi后的ip地址不是我们的路由器分配和路由器网关，我们改掉，使手机与电脑处于同一网关
然后电脑开ssserver（这玩意是什么不用我多说，其实你也可以电脑搭建http proxy server（比如使用cow），然后手机连接wifi设置直接通过代理，但是对于纯tcp和udp就无能为力了）
手机连接电脑ssserver，可以上网了，通过开debug模式可以发现走的是电脑ssserver上网
具体的小白详细教程做法看心情和时间吧
