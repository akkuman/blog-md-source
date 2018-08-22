title: 百度搜索引擎取真实地址-python代码
date: 2017-04-11 12:49:06
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


## 代码
```python
def parseBaidu(keyword, pagenum):
    keywordsBaseURL = 'https://www.baidu.com/s?wd=' + str(quote(keyword)) + '&oq=' + str(quote(keyword)) + '&ie=utf-8' + '&pn='
    pnum = 0
    while pnum <= int(pagenum):
        baseURL = keywordsBaseURL + str(pnum*10)
        try:
            request = requests.get(baseURL, headers=headers)
            soup = BeautifulSoup(request.text, "html.parser")
            for a in soup.select('div.c-container > h3 > a'):
                url = requests.get(a['href'], headers=headers).url
                yield url
        except:
            yield None
        finally:
            pnum += 1
```
<!--more-->
## 示例用法
```python
import requests
from bs4 import BeautifulSoup

headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64; rv:53.0) Gecko/20100101 Firefox/53.0"
}

def parseBaidu(keyword, pagenum)

def main():
    for url in parseBaidu("keyword",10):
        if url:
            print(url)
        else:
            continue
```
