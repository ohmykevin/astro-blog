---
pubDatetime: 2024-03-15
title: "TYPECHO 安装配置"
slug: '1710685688'
tags:
  - typecho
  - mysql
  - caddy
  - php
description: ""
---

### 系统初始操作

```bash
useradd -m -s /bin/bash username # add user

passwd username

vim /etc/ssh/sshd_config # modify ssh port and stop root login

-------------------------------------------------

vim /etc/sudoers

mdzz    ALL=(ALL:ALL) ALL # add sudo user

-------------------------------------------------

sudo apt update && sudo apt upgrade # update

sudo apt install unzip

sudo reboot # reboot
```

### CADDY 安装

```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https curl
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update && sudo apt install caddy
------------------------------------------------------
sudo vim /etc/caddy/Caddyfile

writely.xyz {
        redir https://www.writely.xyz{uri}
}
www.writely.xyz {
@robot {
    not path /robots.txt
  }
  respond /robots.txt 200 {
    body "User-agent: Googlebot
Disallow:
User-agent: DuckDuckBot
Disallow:
User-agent: googlebot-mobile
Disallow:
User-agent: Slurp
Disallow:
User-agent: Bingbot
Disallow:
User-agent: baiduspider
Disallow: /
User-agent: *
Disallow: /
Crawl-delay: 10"
  }
        @static {
                path *.js
                path *.css
                path *.jpg
                path *.png
                path *.ico
                path *.svg
                path *.ttf
                path *.woff
                path *.woff2
                path *.xsl
        }
        file_server @static {
                hide .git
        }
        php_fastcgi unix//run/php/php8.1-fpm.sock
        encode zstd gzip
        root @robot /var/www/typecho/
        @redirected {
                not path *admin*
                not path *robots.txt
                path */
        }
        rewrite @redirected /index.php
}
```

### PHP 安装

```bash
sudo apt install php-fpm php-mysql php-curl php-gd php-mbstring php-xml -y

sudo vim /etc/php/8.1/fpm/pool.d/www.conf

user = caddy
group = caddy
listen.owner = caddy
listen.group = caddy
-------------------------------------------------
sudo systemctl restart php8.1-fpm.service
```

### MYSQL 8.0 安装

```bash
sudo apt install mysql-server

sudo mysql

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Sn1234.xyz';

sudo mysql_secure_installation

-------------------------------------------------

sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf

[mysqld]
user            = mysql
# pid-file      = /var/run/mysqld/mysqld.pid
# socket        = /var/run/mysqld/mysqld.sock
port           = 23306 # modify port
# datadir       = /var/lib/mysql
# bind-address          = 127.0.0.1
mysqlx-bind-address     = 127.0.0.1

-------------------------------------------------

sudo systemctl restart mysql

-------------------------------------------------

sudo mysql -uroot -p

use mysql;

CREATE USER `root`@`%` IDENTIFIED BY 'yourpassword';

ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY  'yourpassword';

GRANT ALL ON *.* TO `root`@`%` WITH GRANT OPTION;
```

### TYPECHO 默认主题美化

修改字体，将从小米开源字体下载的字体文件上传到网站根目录下 fonts 目录下

```bash
mdzz@racknerd-6ce5c6:/var/www/typecho/fonts$ pwd
/var/www/typecho/fonts
mdzz@racknerd-6ce5c6:/var/www/typecho/fonts$ ls
MiSans-Medium.woff2
```

引用字体文件,修改主题文件下的 style.css 文件

```css
@font-face {
    font-family: 'MiSans';
    src: url('/fonts/MiSans-Medium.woff2'); #引入字体文件
  }

body {
  background-color: #FFF;
  color: #444;
  /*font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;*/
  font-family: "Helvetica Neue", Helvetica, Arial, "PingFang SC", "Hiragino Sans GB", "Heiti SC", "MiSans", 
 "Microsoft YaHei", "WenQuanYi Micro Hei", sans-serif; # 引用字体文件
  font-size: 100%; #字体大小调整
}
```

增加 favicon, 将 favicon.ico 文件上传到默认主题目录下，然后在 header.php 文件中引用

```html
<link rel="shortcut icon" href="/usr/themes/default/favicon.ico" type="image/x-icon" />
```
