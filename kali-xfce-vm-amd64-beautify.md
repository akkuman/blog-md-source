title: Kali Linux Xfce版美化虚拟机镜像
date: 2018-09-04 10:59:43
update: ""
author: me
tags: 
- Kali
categories: 
- Kali
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---



## 起因

这两天来学校把硬盘基本全部清空了，所以以前的虚拟机就需要重新安装了。

`Kali` 一直用的是 `xfce` 版本，至于为什么用这个版本，是因为我感觉 `gnome3` 在虚拟机上表现欠佳。当然，默认的 `gnome3` 看起来还是不错的，而 `xfce` 默认的就看起来很寒碜了

默认的 `Kali-Xfce` 是这个样子的

<!--more-->

![原版kali-xfce](http://ww1.sinaimg.cn/large/c0264382ly1fuxco29z1zj20le0c0146.jpg)

具体过程不表了，如果有人有需要我再发吧，毕竟这次美化过程没有记录，我也懒得再重操一遍了，直接上美化后的截图吧

## 美化截图

![](http://ww1.sinaimg.cn/large/c0264382ly1fuxcov30z4j21hc0u07bd.jpg)

![](http://ww1.sinaimg.cn/large/c0264382ly1fuxd0a4t5ej21hc0u0u0x.jpg)

![](http://ww1.sinaimg.cn/large/c0264382ly1fuxd11uu4qj21hc0u0b29.jpg)

## 使用方法

### 基础使用

**注意是64位的镜像，需要cpu虚拟化开启支持**

直接解压然后导入vmware（version >= 10.X）虚拟机即可，默认账户密码为 `root:toor`

### 顶栏透明

图片上的顶栏可以改为透明的，在顶栏上右键然后找到 `面板首选项 -> 外观 -> alpha` 改为 `0` ，顶栏可透明

### 更新vmtool

打开终端

```bash
apt update
apt install open-vm-tools-desktop
```

如果有新版本vmtool会提示更新

## 下载

### 校验

```
大小: 3649679846 字节
修改时间: 2018年9月4日, 11:18:46
MD5: EDC1BF26205D06EA668F8EA03A05D456
SHA1: 4C2F32BA2DDC53425F34B4316F55C66755A08ACA
CRC32: A51255F0
```

## 地址

- [百度网盘 | 密码: jcus](https://pan.baidu.com/s/1Neyff9GpVm08w5A6lesmQQ)
