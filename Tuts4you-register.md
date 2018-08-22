title: Tuts4you注册问题解码
date: 2017-09-24 16:19:00
update: ""
author: me
tags:
- 逆向
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




只说一个，是八进制,下面是解码脚本
环境python3
<!--more-->
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=28785327&auto=1&height=66"></iframe>
```python
# -*-coding:utf-8-*-
# Author: Akkuman
# Blog: hacktech.cn

# Tust4You的问题是八进制,所以八进制转ascii即可
# 解码问题
print("Please input the code what you see on register's page of Tust 4 You:")
encode_code = input()
encode_list = encode_code.split()
print("\nthe question is\n")
for i in encode_list:
	i = int(i,8)
	print(chr(i), end="")
```
