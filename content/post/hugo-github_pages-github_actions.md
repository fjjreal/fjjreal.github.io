---
title: "Hugo github-Pages github-Actions"
date: 2020-12-02T16:19:08+08:00
cover: /images/hugo-github_pages-github_actions_cover.png
tags: [
    "github-actions",
    "github-pages",
    "hugo",
]
categories: [
    "Hugo",
]
---

自动更新博客
<!--more-->

# 新建 action 文件

- {{hugo site 目录}}/.github/workflows/aa.yml（文件名随意）


```
name: Deploy Hugo Blog

on:
  push:
    branches:
      - master   # master 更新触发 现在初始化是mian也可以重命名master

jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
      - uses: actions/checkout@v1

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: latest

      - name: Build 
        run: hugo --theme=dream --baseUrl="https://fjjreal.github.io"

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          personal_token: ${{ secrets.FJJREALBLOG }} # personal_token 这里新建一个 https://github.com/settings/tokens
          PUBLISH_BRANCH: gh-pages  # 推送到当前 gh-pages 分支
          PUBLISH_DIR: ./public  # hugo 生成到 public 作为跟目录
          commit_message: ${{ github.event.head_commit.message }}

```

- ${{ secrets.FJJREALBLOG }}

![FJJREALBLOG](/images/person_token.png)

- token 值

![get token](/images/get_token.png)

![token detail](/images/token_detail.png)


# 配置GitHub Pages

![github pages](/images/github_pages.png)

![github pages edit](/images/github_pages_edit.png)

# 建议仓库名称 {{githubname}}.github.io

## 膜拜大佬
- [GitHub Actions 入门教程](http://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html)
- [Hugo + Github Actions 实现自动化部署](https://immmmm.com/hugo-github-actions/)

