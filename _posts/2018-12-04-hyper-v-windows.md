---
layout: post
title: "Windows10开启Hyper-V"
subtitle: 'Windows10开启Hyper-V, Windows10家庭版, Windows10 中文版, Windows10普通版, 开启Hyper-V'
keyword: 'Hyper-V, Windows10开启Hyper-V, Windows10家庭版, Windows10 中文版, Windows10普通版, 开启Hyper-V'
author: "Yadong"
header-img: "img/post-bg-swordsman.jpg"
header-color: "#ef9811"
header-color-hover: "#0085a1"
tags:
    - Hyper-V
    - Windows10
---


Windows10家庭版默认是不带Hyper-v功能的，如果要开启，需要我们自己在线添加。

1.首先创建一个cmd脚本，内容为

```
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del hyper-v.txt
Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
```

2.以管理员权限打开Windows PowerShell，执行上一步创建的cmd脚本。

3.安装完后，需要重启，重启后，就可以在Windows功能中添加Hyper-V功能了，启用Hyper-V后，重启电脑就可以了。