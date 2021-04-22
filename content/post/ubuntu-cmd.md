---
title: "Ubuntu Cmd"
date: 2021-03-25T16:16:36+08:00
draft: false
tags: [
    "command",
]
categories: [
    "Cmd",
]
---

<!--more-->

# 获取ssh
```

ssh-keygen -t rsa -C "xxxxx@xx.com"

```

# win10 wsl2 ubuntu20.04-LTS LNMP

## php

- sudo apt -y install software-properties-common

- sudo add-apt-repository ppa:ondrej/php

- sudo apt update

- 查询
	- sudo apt-cache search php7.4

- 安装
	- sudo apt install php5.6-fpm php5.6-mysql php5.6-curl php5.6-gd php5.6-mbstring php5.6-mcrypt php5.6-xml php5.6-xmlrpc php5.6-zip php5.6-opcache -y

- 切换cli默认版本
	- sudo update-alternatives --config php

- 启动fpm
	- sudo service php7.4-fpm start

- ini
	- sudo vim /etc/php/7.4/fpm/php.ini

- sock
	- sudo vim /etc/php/7.4/fpm/pool.d/www.conf
	- listen = /run/php/php7.4-fpm.sock

- php ext -- redis、swoole
	- sudo apt install php7.4-redis
	- sudo apt install php7.4-swoole

## nginx

- normal

```
// nginx normal
server {
    listen        80;
    server_name  mysite.de;
    root   /mnt/c/works/mysite;
    location / {
        index index.php index.html error/index.html;
        autoindex  off;
    }
    location ~ \.php(.*)$ {
        fastcgi_pass   unix:/run/php/php5.6-fpm.sock;
        fastcgi_index  index.php;
        fastcgi_split_path_info  ^((?U).+\.php)(/?.+)$;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```

- laravel

```
// nginx laravel
server {
    listen        80;
    server_name  mysite2.de;
    root   /mnt/c/works/mysite2/public;

    location / {
        index index.php index.html error/index.html;
        if (!-e $request_filename) {
            rewrite ^(.*)$ /index.php?s=/$1 last;
            break;
        }
        autoindex  off;
    }

    location ~ \.php(.*)$ {
        fastcgi_pass   unix:/run/php/php7.4-fpm.sock;
        fastcgi_index  index.php;
        fastcgi_split_path_info  ^((?U).+\.php)(/?.+)$;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        fastcgi_param  PATH_INFO  $fastcgi_path_info;
        fastcgi_param  PATH_TRANSLATED  $document_root$fastcgi_path_info;
        include        fastcgi_params;
    }
}
```

## /etc/apt/sources.list

```
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse

```
- sudo apt update

## mysql

- sudo mysql

- use mysql;

- ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';

- or -> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root';
