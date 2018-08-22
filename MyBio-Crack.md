title: MyBio小隐本记注册破解
date: 2018-02-28 21:40:44
update: ""
author: me
tags:
- 逆向
- Tools
categories:
- 逆向
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---



既然开始了，就把这一个系列的都破了算了，这次主角小隐本记MyBio

<!--more-->

和WDTP的原理是差不多的，先把软件界面换成e文，然后写了15个记录后提示注册，一样的路子，直接跳过注册窗口的弹出就好了

![注册窗口](http://ww1.sinaimg.cn/large/c0264382gy1fowhkbesvuj20b0088t8x.jpg)

然后查壳一样是vs2015的无壳64位程序，直接附加到x64dbg，然后有了之前WDTP的经验，我们直接找弹出注册窗口的地方，查找字符串，然后搜索上图中`Serial-number:`

一样的，找到了注册窗体生成的地方，在段首下个断，然后回溯一次，可以看到

![](http://ww1.sinaimg.cn/large/c0264382gy1fowhspimgmj20q507vgmx.jpg)

直接把这个call上方的jle改成jmp即可爆破。

------------------

软件下载地址：

[密码：0yb0cz](https://share.weiyun.com/f5a48a92d8f458277e937dadc730a5ad)

解压后注意校验

```
大小: 4181504 字节
文件版本: 2.1.1004
修改时间: 2018年2月28日, 21:27:02
MD5: EEA6B0BF010E45EA7EF340FFB543C316
SHA1: BAA4BE7B3F2DE0F75996C0E9BE8DA0C177444CE8
CRC32: 999277D5
```
