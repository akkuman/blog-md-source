title: 'ProgrammingError: You must not use 8-bit bytestrings...'
date: 2016-12-06 14:31:14
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



# 问题出现：
You must not use 8-bit bytestrings unless you use a text_factory that can interpret 8-bit bytestrings (like text_factory = str). It is highly recommended that you instead just switch your application to Unicode strings.
# 产生原因：
问题在用Python的sqlite3操作数据库要插入的字符串中含有非ascii字符时产生，做插入的时候就报当前这个错误。
# 解决方法：
## 1. 按提示
```python
connection = sqlite3.connect(...)
connection.text_factory = str
```
但是如果字符中出现非ascii字符，那么依然不能解决问题，会产生不可预知的乱码，这样可以参考 2
## 2. 以utf8的编码格式进行解码转为unicode编码做插入
```python
cursor.execute('''
    INSERT INTO JAVBUS_DATA (姓名, 年龄)
    VALUES (?, ?)
    ''', ('张三'.decode('utf-8'), '22岁'.decode('utf-8')))
```
但是如果数据太长，这样一个一个敲挺麻烦的，下面是一个使用map函数简化的小例子


<!--more-->


```python
#-*-coding:utf-8-*-
import sqlite3

def decode_utf8(aStr):
    return aStr.decode('utf-8')

conn = sqlite3.connect("something.db")
cursor = conn.cursor()

cursor.execute('''
    CREATE TABLE IF NOT EXISTS JAVBUS_DATA(
        id       INT PRIMARY KEY,
        姓名     TEXT,
        年龄     TEXT);''')
print "Table created successfully"
cursor.execute('''
    INSERT INTO JAVBUS_DATA (姓名, 年龄)
    VALUES (?, ?)
    ''', map(decode_utf8, ('张三', '22岁')))

cursor.close()
conn.commit()
conn.close()
```
# 其他注意：
有时用第二种方法会出现UnicodeDecodeError
加入#-*-coding:utf-8-*-
还是不行请sys指定编码：
```python
import sys  
reload(sys)  
sys.setdefaultencoding('utf8') 
```
**这个问题在python3应该不会出现，python2编码问题，仅作记录**
