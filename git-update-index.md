title: git忽略对已入库文件的修改
date: 2018-08-22 15:18:42
update: ""
author: me
tags: 
- git
categories: 
- git
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---



项目开发过程中，会遇到本地配置文件每个开发人员不同的情况，但如果遇到类似数据库配置这种最终需要加入 git 版本控制的配置，则会陷入两难境地。要么不跟踪，要么有人提交后其他人同步下来必须手动修改，非常麻烦。其实，对于已被纳入版本管理的文件，git 也提供了很好的解决办法。

- 告诉git**忽略**对已经纳入版本管理的文件 `.classpath` 的修改，git 会一直忽略此文件直到重新告诉 git 可以再次跟踪此文件 `$ git update-index --assume-unchanged .classpath`

- 告诉 git **恢复跟踪** `$ git update-index --assume-unchanged .classpath`

- **查看**当前被忽略的、已经纳入版本库管理的文件：`$ git ls-files -v | grep -e "^[hsmrck]"`
