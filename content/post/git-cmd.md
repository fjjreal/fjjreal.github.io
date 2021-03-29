---
title: "Git Cmd"
date: 2020-11-29T16:55:35+08:00
cover: /images/git_cmd_s.jpg
tags: [
    "command",
]
categories: [
    "Cmd",
]
---

<!--more-->


![git cmd](/images/git_cmd.png)

# 不重复登录

- 保存账号密码

```

git config --global credential.helper store

```

- ssh

1.获取ssh

```

ssh-keygen -t rsa -C "xxxxx@xx.com"

cat ~/.ssh/id_rsa.pub

```

2.在仓库中添加公钥(id_rsa.pub内容)


# 切换 ssh 获取

	```

	git remote set-url origin git@github.com:githubname/project.git

	```
