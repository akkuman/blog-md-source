title: otunnel：一个和lcx差不多的端口转发的工具
date: 2017-02-15 20:58:16
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


这是一个采用Golang编写的和lcx差不多的端口转发的工具，用来突破内网环境

# 项目地址
[ooclab/otunnel](https://github.com/ooclab/otunnel)
<!--more-->
# 下载地址(内涵各大平台)
[http://dl.ooclab.com/otunnel/](http://dl.ooclab.com/otunnel/)

# otunnel 用法
前提：
1. 假设 server 的地址为 example.com
2. 从 client 能连接 server (client 与 server 无需在同一个网络)

**注意**  otunnel 程序可以作为 server 和 client 两种角色（运行参数不同）

## 快速上手

### server
```bash
./otunnel listen :10000 -s longlongsecret
```

### client
#### 反向代理
举例：将 client 可以访问的 192.168.1.3:22 映射到 server 上的 10022 端口：
```bash
./otunnel connect example.com:10000 -s longlongsecret -t 'r:192.168.1.3:22::10022'
```
现在访问 example.com:10022 即等于访问了 client 内网的 192.168.1.3:22

#### 正向代理
举例：假设 example.com 的 127.0.0.1:3128 服务（你懂得），在 client 运行：
```bash
./otunnel connect example.com:10000 -s longlonglongsecret -t 'f::20080:127.0.0.1:3128'
```
现在 client 的 20080 端口， 等于访问 example.com 上的 127.0.0.1:3128

## 程序用法
### -t 格式
包含多个字段信息，以` : `隔开(为空的字段也不能省略` : `)。

```
代理类型:本地地址:本地端口:远程地址:远程端口
```

| 字段 | 含义 |
| ------------- |:-------------:|
| 代理类型 | r 表示反向代理; f 表示正向代理 |
| 本地地址 | IP或域名 |
| 本地端口 | 整数 |
| 远程地址 | IP或域名 |
| 远程端口 | 整数 |

**注意**
1. `本地地址`或`远程地址`如果为空，表示所有网口
2. otunnel 命令行可以包含多个`-t`选项，同时指定多条隧道规则

# 特点及优势
otunnel 是一款对称的安全隧道工具。

- 单二进制程序：otunnel 为一个独立的二进制程序，可以作为 server 和 client 端。
- 支持多操作系统平台：支持GNU/Linux, Unix-like, Mac, Windows，其他如 ddwrt 等 arm 平台。
- 无需配置文件：命令行使用
- 对称设计：同时支持 正、反向代理（端口映射）
-  安全加密：支持 AES 对称加密

![otunnel反向代理图示](https://ooo.0o0.ooo/2017/02/15/58a455a1b0a71.png)
