title: HTTP协议请求类型介绍
date: 2016-06-10 01:39:13
update: ""
author: me
tags:
- HTTP
- Web
categories:
- Web
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---


----------


**HTTP协议中共定义了八种方法或者叫“动作”来表明对Request-URI指定的资源的不同操作方式，具体介绍如下：**

----------
# OPTIONS：
返回服务器针对特定资源所支持的HTTP请求方法。也可以利用向Web服务器发送'*'的请求来测试服务器的功能性。
# HEAD：
向服务器索要与GET请求相一致的响应，只不过响应体将不会被返回。这一方法可以在不必传输整个响应内容的情况下，就可以获取包含在响应消息头中的元信息。
# GET：
向特定的资源发出请求。
<!--more-->
# POST：
向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的创建和/或已有资源的修改。 
# PUT：
向指定资源位置上传其最新内容。 
# DELETE：
请求服务器删除Request-URI所标识的资源。 
# TRACE：
回显服务器收到的请求，主要用于测试或诊断。 
# CONNECT：
HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。

----------
**虽然HTTP的请求方式有8种，但是我们在实际应用中常用的也就是get和post，其他请求方式也都可以通过这两种方式间接的来实现。**

-----------
转载自：http://www.xuebuyuan.com/1586750.html
