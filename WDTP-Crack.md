title: WDTP注册破解
date: 2018-02-26 20:34:50
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



今天来讲讲WDTP这个软件的破解。

## 简介
WDTP 不止是一款开源免费的 GUI 桌面单机版静态网站生成器和简单方便的前端开发工具，更是一款跨平台的集笔记、个人知识管理、写作/创作、博客/网站内容与样式管理等功能于一体的多合一内容处理/管理器，同时还是一款高度追求用户体验与计算机文本编写良好感受的 Markdown 编辑器。该软件研发的核心思想是：简洁高效、轻灵优雅、先进强悍、操作简单。
<!--more-->
## 破解
之前这个软件是开源的，后来作者把它闭源了，然后加上了注册机制，我今天测试了一下，在我写了十多篇文章之后我再添加就提示我需要注册。
![](http://ww1.sinaimg.cn/large/c0264382gy1fou2567e65j20b0088t8y.jpg)
查一下壳，没有壳，64位的
![](http://ww1.sinaimg.cn/large/c0264382gy1fou25yvsaxj20ei074q3n.jpg)
直接附加到x64dbg中，然后我们搜索一下字符串serial，可以找到错误提示的地方。
![](http://ww1.sinaimg.cn/large/c0264382gy1fou27t4wefj213q0fetcj.jpg)
我们反汇编窗口中下个断，我们可以看到上方的ret，说明提示错误信息是跳转进来的，然后我们在段首下好断，重新注册可以找到调用这里的地方
![](http://ww1.sinaimg.cn/large/c0264382gy1fou2buv9phj20fs06qaay.jpg)
我们跟过去
![](http://ww1.sinaimg.cn/large/c0264382gy1fou2d3h9nvj20hc02zt8z.jpg)
可以看到错误提示的call，这个call上方有一个jmp可以跳过，说明在前方应该有一个跳转跳过了这个jmp，直接跳到了这个错误提示call。我们再往前看一点。
![](http://ww1.sinaimg.cn/large/c0264382gy1fou2fedcjfj20m50chgnw.jpg)
我们可以看到上面的je，je前面的call是一个对比的call，爆破的话，我们不管这个，直接把je给nop掉。
然后我们执行，发现还是点击新建就会弹出来注册框，功能无法使用。
我们继续在字符串中找，可以看到窗口上面的Purchase，Question等等字符，可以发现错误提示的上面一段就是这个注册窗口弹出的一段，我们依旧在这个段的段首下段，然后找到调用它（弹注册窗）的地方。
![](http://ww1.sinaimg.cn/large/c0264382gy1fou2xb5dm0j20ia03lgm3.jpg)
它是直接jmp下来的，我们可以看到上面有一个call之后跟着一个test然后一个jne，我们可以猜想是你新建文档的时候先比对一下你是否注册，然后根据结果跳转，我们直接把jne改成jmp试试，让它直接跳过弹注册窗口。
![](http://ww1.sinaimg.cn/large/c0264382gy1fou33aziswj209q054q2u.jpg)
完美，现在新建没问题了。

## 导出
所以我们只需要把它的这个弹注册窗的地方直接jmp过就好，我们在我们修改的命令上面右键补丁
![](http://ww1.sinaimg.cn/large/c0264382gy1fou352soi2j20ed0dbt90.jpg)
然后点击修复文件即可导出成一个破解版的exe。

## 下载
下载后注意校验信息
文件信息：
```
文件版本: 1.1.1004
修改时间: 2018年2月26日, 19:40:44
MD5: 5B8DF3D4572842376EA850B8551DEEED
SHA1: B282AC870E4159A2ACEA389015FE4F4409A0F887
CRC32: F51675CE
```
[密码：h7b4ru](https://share.weiyun.com/5f8f4a09b5fb84f23479479e661b0c69)
