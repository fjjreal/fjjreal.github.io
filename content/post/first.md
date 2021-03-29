---
title: "Hugo 使用"
date: 2020-11-19T09:25:02+08:00
description: "第一次使用hugo"
cover: /images/default1.jpg
tags: [
    "test",
]
categories: [
    "Soft",
]
---

### Hello Hugo
<!--more-->

### 环境
```
os:win10 wsl ubuntu 20.04
go:1.13
```

- 安装
```
sudo apt-get install hugo
```

- 新建项目
```
hugo new site site_name
```

- 新建文件
```
// 生成 site_name/content/file_path/file_name.md 文件
hugo new file_path/file_name.md
```

#### 文件
```
---
title: ""                       # 文章标题
date: 2020-11-19T09:25:02+08:00 # 文件时间
description: "第一次使用hugo"    # 关于此文件简单描述
cover: /img/default2.jpg        # 封面
tags: [                         # 标签
    "test",
]
categories: [                   # 文章类目
    "daily",
]
---

### Hello Hugo                  # 预览内容
<!--more-->                     # 预览结束标记

### 文章正文

```

- 使用他人主题 https://themes.gohugo.io/
```
// site_name 目录
git clont github.com/**/*****.git themes/*****
// 本地调试
hugo server --theme=***** --buildDrafts

// 部署 github page等
1.新建github项目:github用户名称.github.io
2.生成静态资源 hugo --theme=***** --baseUrl="https://github用户名称.github.io/"
3.上传
	cd public
	echo "# github用户名称.github.io" >> README.md
	git init
	git add README.md
	git commit -m "first commit"
	git branch -M main
	git remote add origin https://github.com/0000/0000.git
	git push -u origin main
```

#### tips : github初始master已改为main
