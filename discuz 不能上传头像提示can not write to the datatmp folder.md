title: discuz 不能上传头像提示can not write to the data/tmp folder
date: 2016-05-31 23:56:06
update: ""
author: me
tags:
- website
- Discuz
categories:
- Discuz
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
# discuz 不能上传头像提示can not write to the data/tmp folder
----------
## 解释：
disucz头像上传不成功，提示data/tmp目录没有写入权限，这里的data/tmp是网站根目录uc_server/data/tmp这个目录，而不是根目录/data/tmp目录，其实/data下面本来没有tmp目录。

<!--more-->

## 解决办法：
首先看看uc_server/data/tmp有无写入权限，如果有权限那么就按照如下解决方法，需要修改php.ini，找到open_basedir选项，行首加分号;注销即可。
