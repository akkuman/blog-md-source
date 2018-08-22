title: Visual Studio Code配置Python开发环境
date: 2016-06-29 21:29:45
update: ""
author: me
tags:
- Python
- VSCode
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



# 1.安装Python插件

在VScode界面按`Crtl`+`Shift`+`P`或者`F1`

输入`ext install`

![ext install](http://7xusrl.com1.z0.glb.clouddn.com/ext%20install.png)

直接安装`Python`，也就是点击它，然后等待，安装好后会提示你重启
<!--more-->

# 2.配置运行Python程序

同样的打开命令面板（`Crtl`+`Shift`+`P`或`F1`），然后输入`Tasks: Configure Task Runner`（中文输入：任务，然后选择任务：配置任务运行程序），选择`Other`

此时VScode会自动生成.vscode文件夹并生成一个默认的task.json

![tasks.json](http://7xusrl.com1.z0.glb.clouddn.com/vscode-tasks.json.png)

配置如下


    "version": "0.1.0",
    "command": "python",
    "isShellCommand": true,
    "args": ["${file}"],
    "showOutput": "always"

然后写完代码后
`Crtl`+`Shift`+`B`运行Py程序
![python-run](http://7xusrl.com1.z0.glb.clouddn.com/VScode-python-run.png)
