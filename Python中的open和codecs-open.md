title: Python中的open和codecs.open
date: 2016-06-10 01:26:22
update: ""
author: me
tags:
- Python
categories:
- Python
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---



最近老被编码困扰，多次折腾之后，感觉python的编解码做得挺好的，只要了解**下边的流程，一般都能解决**

**input文件(gbk, utf-8...)   ----decode----->   unicode  -------encode------> output文件(gbk, utf-8...)**
很多文本挖掘的package是在unicode上边做事的，比如nltk. 所以开始读入文件后要decode为unicode格式，可以通过下边两步：
```python
f=open('XXXXX', 'r')
content=f.read().decode('utf-8')
```

更好的方法是使用codecs.open读入时直接解码：
```python
f=codecs.open(XXX, encoding='utf-8')
content=f.read()
```


转自: http://f.dataguru.cn/thread-237116-1-1.html
