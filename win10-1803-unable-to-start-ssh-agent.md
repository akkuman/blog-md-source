title: win10 1803版本unable to start ssh-agent service, error :1058
date: 2018-09-01 16:26:11
update: ""
author: me
tags: 
- 问题解决
categories: 
- 问题解决
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---



PowerShell安装了pshazz或者posh-git，但是打开的时候提示 `unable to start ssh-agent service, error :1058`  

1803的设置上面可以看到这个版本是默认带了openssh客户端的，我们不需要另外去安装，但是命令行运行 `ssh-agent` 依然是显示 `unable to start ssh-agent service, error :1058`  

既然有这个东西，但是服务启动失败，那我们看看本地服务，果然，在本地服务中禁用了，我们改成手动或者自动就能解决这个问题了