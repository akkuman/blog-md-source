title: OpenWRT路由器使用ipv6拨号上网教程
date: 2016-09-22 15:39:45
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



> 文章来源于群友，如有侵权，请联系我(aha971030@gmail.com)删除

# 原理介绍分析： #
湖北E信地区可以使用ipv6拨号，好处是网络是上下对等不限速网络，也就是说，你的端口上限是多少，网上就可以达到多少，我测试很多次，一般在100M左右，但是遗憾的是，该拨号方式只能使用32位系统，且由于E信软件的兼容性问题，很容易导致蓝屏死机。经过大神的抓包分析，该拨号方式是使用ipv6的隧道协议传递ipv4信号。而幸运的是，现在的openwrt支持该协议。也就是说可以使用基于openwrt的路由器采用ipv6拨号。

# 操作步骤： #

首先要明确是，该拨号方式也是需要进行账号换算的，首先启动路由器，并插上网线，在电脑上下载winscp这款软件，然后我们查询一下我们的ip地址，在电脑的dos界面输入ipconfig，找到以太网配置器

![network111](http://7xusrl.com1.z0.glb.clouddn.com/network111.png)

<!--more-->

默认网关就是路由器的管理ip。

然后我们启动软件，按照图片设置填入数据

![winscp111](http://7xusrl.com1.z0.glb.clouddn.com/winscp111.png)

然后我们就进入了路由器的文件系统

![op-config](http://7xusrl.com1.z0.glb.clouddn.com/op-config.png)

接着，我们要做的是，进入路由器设置里面设置相关端口参数

在电脑的浏览器里输入管理ip地址

![sk1530](http://7xusrl.com1.z0.glb.clouddn.com/sk1530.png)

进入端口设置界面

首先设置wan口参数

![op-01](http://7xusrl.com1.z0.glb.clouddn.com/op-01.png)

![op-02](http://7xusrl.com1.z0.glb.clouddn.com/op-02.png)

切换协议为PPPOE，并随便输入账号密码（具体的拨号的账号密码在后面我们会加以更改）并在高级设置里勾选以下参数

![op-03](http://7xusrl.com1.z0.glb.clouddn.com/op-03.png)

然后保存并应用

然后我们设置lan口参数

![op-04](http://7xusrl.com1.z0.glb.clouddn.com/op-04.png)

按照该图设置

最后，我们回到接口总界面，自己创建一个端口

![op-05](http://7xusrl.com1.z0.glb.clouddn.com/op-05.png)

名字无所谓，但协议要选择rfc6333

![op-06](http://7xusrl.com1.z0.glb.clouddn.com/op-06.png)

提交以后填写ipv6的地址，经过大神的尝试，下面给的这个地址是比较稳定的，建议使用

![op-07](http://7xusrl.com1.z0.glb.clouddn.com/op-07.png)

240e:d:1000::ffff:1:
并在高级设置里面勾选默认网关

![op-08](http://7xusrl.com1.z0.glb.clouddn.com/op-08.png)

在防火墙设置里，把这个链接拉到wan口里

![op-09](http://7xusrl.com1.z0.glb.clouddn.com/op-09.png)

最后保存

这样，路由器上的设置就结束了，下面转入配置文件的修改上

依次顺序进入到如下路径

![op-10](http://7xusrl.com1.z0.glb.clouddn.com/op-10.png)

双击network文件打开

并在文件的位置更改

![op-11](http://7xusrl.com1.z0.glb.clouddn.com/op-11.png)

然后点击保存

然后进入到此目录，上传我们准备的E信算法库文件

![op-12](http://7xusrl.com1.z0.glb.clouddn.com/op-12.png)

最后重启一下路由器，同步一下路由器的时间，就可以了
注意，不同的芯片和不同地区的openwrt路由器，sxplugin.so文件是不一样的，具体请查看我上一篇文章打包的东西。

> 再说一次，文章来源于群友，如有侵权，请联系我(aha971030@gmail.com)删除
