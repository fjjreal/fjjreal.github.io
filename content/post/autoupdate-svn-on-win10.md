---
title: "win10下自动更新svn脚本"
date: 2020-12-17T10:10:02+08:00
draft: false

cover: /images/svn-update-cover.png

tags: [
	"svn"
]

categories: [
    "soft",
]

---

os: - win10

tool: - ApacheSubversion命令行工具

<!--more-->

# 获取ApacheSubversion

![svn cmd tool](/images/svn-cmd-tool.png)

- [visualsvn](https://www.visualsvn.com/downloads/)

- [百度网盘 code:1024 ](https://pan.baidu.com/s/1wX43nE2hWtx-YyI73tazKQ)

	![百度网盘](/images/svn-cmd-tool-baidu-download.png)

下载完解压到 D:\svn_cmd_tool 下
	
    |--svn_cmd_tool
    |    |--bin
    |    |    svn.exe

# 脚本目录 D:\svn_hooks

    |--svn_hooks
    |    |    svn-update.bat
    |    |    callSVNUpdate.vbs
    |    |    svn.update.log // svn-update.bat 生成

# 更新脚本 svn-update.bat

```
@echo off
set url=SVN服务器端项目地址
for /f "tokens=2* delims=:" %%i in ('"D:\svn_cmd_tool\bin\svn.exe info %url% | find "Last Changed Rev:""') do set online=%%i
set localurl=本地项目地址
for /f "tokens=2* delims=:" %%i in ('"D:\svn_cmd_tool\bin\svn.exe info %localurl% | find "Revision:""') do set local=%%i
if "%online%" equ "%local%" (echo "not need svn update") else (DATE >> svn.update.log & D:\svn_cmd_tool\bin\svn.exe update 本地项目地址 >> svn.update.log)
pause

// 也可以给svn配置环境变量，将D:\svn_cmd_tool\bin\svn.exe替换为svn
// win7把需要更新时的DATE改为echo %date% %time%
```

# 循环进程 callSVNUpdate.vbs

```
'指定时间间隔调用.bat文件
'停止脚本请在任务管理器结束wscript.exe
Set ws=wscript.createobject("wscript.shell")
dim bat
'需运行的文件
bat="cmd.exe /c svn-update.bat"
do
'0表示不显示窗口，1显示，调试用
ws.run bat,0
'每5分钟运行一次
wscript.sleep 300000
loop
```

# 加入开机自启计划任务

![win10 计划任务](/images/set-vbs-autostart.png)

### 触发器 开机时自启

### 操作 地址:*****\callSVNUpdate.vbs

![win10 计划任务-结果](/images/svn-update-cover.png)
