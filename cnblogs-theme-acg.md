title: 把博客园自己博客皮肤改了下
date: 2017-12-28 15:25:58
update: ""
author: me
tags:
- theme
- life
categories:
- theme
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---



具体效果看[我博客园](http://akkuman.cnblogs.com/)，或者看[原作者frantic1048博客](http://www.cnblogs.com/frantic1048/)

<!--more-->

## 出处
[折腾了一个新皮肤，自带预览图](https://group.cnblogs.com/topic/71186.html)
这个皮肤使用博主已经说的比较清楚了，我再发一遍是因为这个主题在手机上emmmm...比较惨不忍睹，自己小小的优化了一下（其实就是隐藏加margin-right哈哈），让手机端可以正常显示了，另外侧边栏有些h3标题无效果，也改进了一下，本人也没专门学过css，只是小修了一下，还是希望大家共同努力，毕竟frantic1048博主提供的这个皮肤真的挺好看

## 使用方法
主题选择`Gertrude Blue`，`禁用模板默认CSS`勾选上，然后把地址[http://www.cnblogs.com/blog/customcss/359968.css](http://www.cnblogs.com/blog/customcss/359968.css)中的css全部复制到`页面定制CSS代码`，头像自定义在css文件的239行，可以自行更换地址  

`博客侧边栏公告`里面是 
```html
<div id="sidebar-cus">
<div id="cus-avatar"></div>
</div>
```

 如果想要使用页首，遵循下面的结构，其中第二个 span 标签是可选的:
```
<div id="top-qoute-container">
    <span id="top-qoute-context">It is the path you have chosen. Take pride in it.</span>
    <span id="top-qoute-from">Kotomine Kirei</span>
</div>
```
你也可以自定义一个名言列表进行轮换，不过那个需要js权限

其实和frantic1048说明一样，只是css改动了一点

祝大家使用愉快

## 参考资料：
- [折腾了一个新皮肤，自带预览图](https://group.cnblogs.com/topic/71186.html)
- [frantic1048的博客](http://www.cnblogs.com/frantic1048/)
