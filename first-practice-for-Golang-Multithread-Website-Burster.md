title: Golang初练手-多线程网站路径爆破
date: 2017-02-08 01:25:48
update: ""
author: me
tags:
- Golang
- Tools
categories:
- Golang
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---


以前用Python写过这个工具，前两天看了golang的基础，就想着用这个语言把这个工具重写一遍

先放张图![演示1.gif](https://ooo.0o0.ooo/2017/03/04/58ba4e8c66d38.gif)
<!--more-->

用法
```
    Example : Buster.exe -u=https://www.baidu.com -d=asp.txt -t=5
    Buster是你的程序名字
    -u后面填网址参数，格式如上
    -d选字典
    -t是线程数
    当你第一次运行请直接在命令行运行你的程序，什么参数都别加，他会有提示信息告诉你怎么做的
```
话不多说，直接上代码，字典采用的以前搜集的一个珍藏的大字典，跑起来可能耗时比较久，文件外链会放在底下
```Golang
package main

import (
	"bufio"
	"flag"
	"fmt"
	"io/ioutil"
	"net/http"
	"os"
	"sync"
)

var urls chan string
var no404URL = make(chan string)
var wg sync.WaitGroup //等待goroutine完成

func main() {

	var baseURL string
	var dicPath string
	var threadCount int
	flag.StringVar(&baseURL, "u", "https://www.baidu.com", "website which you want to burst")
	flag.StringVar(&dicPath, "d", "asp.txt", "dic which you want to use")
	flag.IntVar(&threadCount, "t", 5, "number of Thread")
	flag.Parse()

	if len(os.Args) == 1 {
		fmt.Println("------------------------------------")
		fmt.Println(" Author      |       Akkuamn")
		fmt.Println("------------------------------------")
		fmt.Println(" Update-v1.0 |      2017-02-07")
		fmt.Println("-------------------------------------")
		fmt.Printf("\nUsage : \n\tExample : %s -u=https://www.baidu.com -d=asp.txt -t=5\n\n", os.Args[0])
		fmt.Printf("View more help via %s -h\n\n", os.Args[0])
		listDic("dic")
	} else {
		dicPath = "./dic/" + dicPath
		start(baseURL, dicPath, threadCount)
		wg.Wait() //等待goroutine完成
	}
}

func start(baseURL string, dicPath string, threadCount int) {

	dicFile, dicError := os.OpenFile(dicPath, os.O_RDONLY, 0)
	if dicError != nil {
		fmt.Printf("\nOpenFile Error:文件打开出错，请检查字典文件是否存在，或文件名是否准确\n")
		return
	}
	defer dicFile.Close()

	//把处理后的需要爆破的url全部传到信道urls
	ReturnBurstURL(dicFile, baseURL)

	//单独开goroutine从信道no404URL取数据写入文件
	go func() {
		resultTxt, err := os.OpenFile("result.txt", os.O_CREATE|os.O_TRUNC|os.O_RDWR, 0660)
		if err != nil {
			fmt.Println("OpenFile Error:" + err.Error())
		}
		resultWriter := bufio.NewWriter(resultTxt)
		defer resultTxt.Close()
		for {
			_, err = resultWriter.WriteString(<-no404URL)
			if err != nil {
				fmt.Println("resultWriter Error:" + err.Error())
			}
			resultWriter.Flush()
		}
	}()

	//并发访问网址并将状态码不为404的网址加入信道no404URL
	for i := 0; i < threadCount; i++ {
		wg.Add(1)
		go func(i int) {
			for len(urls) > 0 {
				url := <-urls
				status := HTTPStatus(url)
				fmt.Printf("[%d]%s-----%s\n", i, status, url)
				if status != "404 Not Found" {
					no404URL <- status + "-----" + url + "\n"
				}
			}
			wg.Done()
		}(i)
	}
}

//返回HTTP访问状态码
func HTTPStatus(url string) (status string) {
	client := http.DefaultClient
	reqest, err := http.NewRequest("HEAD", url, nil)
	if err == nil {
		reqest.Header.Set("User-Agent", "Mozilla/5.0 (X11; Ubuntu; Linux i686; rv:51.0) Gecko/20100101 Firefox/51.0")
		reqest.Header.Set("Accept", "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8")
		response, err1 := client.Do(reqest)
		if err1 != nil {
			fmt.Println("HTTPRequest Error:" + err1.Error())
		}

		defer response.Body.Close()
		return response.Status
	} else {
		fmt.Println("NewRequest Error:" + err.Error())
		return "400 Bad Request"
	}
}

//把处理后的需要爆破的url全部传到信道urls
func ReturnBurstURL(fURL *os.File, baseurl string) {
	var urlList []string
	allURLTxt := bufio.NewScanner(fURL)
	for allURLTxt.Scan() {
		newurl := baseurl + "/" + allURLTxt.Text()
		urlList = append(urlList, newurl)
	}
	urls = make(chan string, len(urlList))
	for _, url := range urlList {
		urls <- url
	}
	fmt.Printf("\n读取字典完成，准备开始，请等待...\n")
}

//罗列出可用字典
func listDic(dicDir string) {
	dirList, err := ioutil.ReadDir(dicDir)
	if err != nil {
		fmt.Println("ReadDir Error : " + err.Error() + "\n")
	}
	fmt.Println("Dic you can select : ")
	for _, file := range dirList {
		fmt.Printf("    %s\n", file.Name())
	}
}
```

只编译了win平台下的，如果有需要可以自行编译

**[源码及字典及win程序](http://pan.baidu.com/s/1c2BYT8k)**
**密码: g1gd**
