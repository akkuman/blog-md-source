title: CodeTyphon 跨平台交叉编译的配置
date: 2018-01-01 21:55:26
update: ""
author: me
tags:
- Lazarus
- Delphi
categories:
- Lazarus
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---



CodeTyphon和Lazarus的关系相当于就是ubuntu和linux的关系  

不过CodeTyphon提供了很多一键配置即可使用的交叉编译配置，而Lazarus就比较麻烦了，我也没用Lazarus交叉编译过  

首先假设我们交叉编译是在**windows编译出linux可执行程序**，那么我们需要做的事情大致上分为以下几步：
<!--more-->
## 下载跨平台交叉工具链(Download Cross Toolchains)

框选出来的两个都可以

![TIM图片20180101212151.png](https://i.loli.net/2018/01/01/5a4a3e4098508.png)


然后选择我们所需的linux，平台cpu位数需要自己根据自己的需求来，选择好后点选最右边的下载标识等待下载（我们这里选择的**win64-i386-linux**）

![TIM图片20180101212410.png](https://i.loli.net/2018/01/01/5a4a3e3f83041.png)

## 下载系统二进制库(Download OSes Libraries)

下载**win64-i386-linux**对应的库，你也可以选择qt4那个，只是界面库不一样而已

![TIM图片20180101212939.png](https://i.loli.net/2018/01/01/5a4a3e3f9f0f9.png)


## FPC Cross elements

这一步就相当于写处理配置了，根据你选择的**win64-i386-linux**来

![TIM图片20180101213357.png](https://i.loli.net/2018/01/01/5a4a3e40e8a7a.png)



## Typhon的工程配置选择

前几步做好后，现在只需要在ide里面做一些设置即可了，我直接放图，应该大家能看懂
打开 `工程 > 工程选项 > 编译选项 > 路径`把`Libraries`路径设置好

![TIM图片20180101214030.png](https://i.loli.net/2018/01/01/5a4a3e41f1894.png)


然后选择平台

![TIM图片20180101214132.png](https://i.loli.net/2018/01/01/5a4a3e412bc5e.png)


Lazarus和CodeTyphon编译出来的程序体积都比较大，减小体积可以把`generate debugging info for GDB`的选项去掉

![TIM图片20180101214407.png](https://i.loli.net/2018/01/01/5a4a3e41635f5.png)


最后编译程序即可

## 参考资料：  
- [CodeTyphon - Cross-Build for Android](http://www.pilotlogic.com/sitejoom/index.php/93-wiki/ct-tutorials/222-cross-build-for-android)
