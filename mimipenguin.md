title: 抓取当前登录用户登录密码的工具：mimipenguin
date: 2017-04-02 10:03:29
update: ""
author: me
tags:
- Tools
categories:
- Tools
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---


**[Github项目地址](https://github.com/huntergregal/mimipenguin)**

前有Mimikatz，今有mimipenguin，近日国外安全研究员huntergregal发布了工具mimipenguin，一款Linux下的密码抓取神器，可以说弥补了Linux下密码抓取的空缺。
编写思路来自流行的[windows密码抓取神器mimikatz](https://github.com/gentilkiwi/mimikatz)
<!--more-->
![](https://ooo.0o0.ooo/2017/04/06/58e5a6e8ac47f.png)

## 详情
通过转储过程和提取那些包含明文密码可能性很高的行（hang），充分利用内存中的明文凭证。通过检查/etc/shadow文件hash,内存中的hash和正则匹配去尝试计算出每个单词的概率

## 要求
- root权限

## 已支持(以下环境已通过测试)
- Kali 4.3.0 (rolling) x64 (gdm3)
- Ubuntu Desktop 12.04 LTS x64 (Gnome Keyring 3.18.3-0ubuntu2)
- Ubuntu Desktop 16.04 LTS x64 (Gnome Keyring 3.18.3-0ubuntu2)
- XUbuntu Desktop 16.04 x64 (Gnome Keyring 3.18.3-0ubuntu2)
- VSFTPd 3.0.3-8+b1 (Active FTP client connections)
- Apache2 2.4.25-3 (Active/Old HTTP BASIC AUTH Sessions) [Gcore dependency]
- openssh-server 1:7.3p1-1 (Active SSH connections - sudo usage)

## 记录
- 在内存中的密码 - 100%有效
- 计划扩大支持和其他凭证位置
- 努力扩展到非桌面环境
- 已知bug - 有时gcore会挂起脚本，不过这是gcore导致的问题
- 开放提出请求和社区研究
- 计划未来的LDAP研究（nscld winbind等）

## 联系方式
- Twitter: [@huntergregal](https://twitter.com/HunterGregal)
- 个人站点: [huntergregal.com](http://huntergregal.com/)
- Github: [huntergregal](https://github.com/huntergregal)

## 特别鸣谢
- the-useless-one for remove Gcore as a dependency, cleaning up tabs, and adding output option
- gentilkiki for Mimikatz, the inspiration and the twitter shoutout
- pugilist for cleaning up PID extraction and testing
- ianmiell for cleaning up some of my messy code
- w0rm for identifying printf error when special chars are involved
- benichmt1 for identifying multiple authenticate users issue
- ChaitanyaHaritash for identifying special char edge case issues

转载自[mimipenguin](https://github.com/huntergregal/mimipenguin/blob/master/README.md)
