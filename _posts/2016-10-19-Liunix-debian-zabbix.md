---
layout: post
title: Debian 8 下安装zabbix
tags:
- debian
- zabbix
categories: linux
description: Debian 8下安装zabbix，监控服务器性能
---
##主题介绍
Debian 8下安装zabbix，监控服务器性能

<!-- more -->

##下载deb安装源
- **下载地址**

> http://repo.zabbix.com/zabbix/3.2/debian/pool/main/z/zabbix-release/zabbix-release_3.2-1+jessie_all.deb

##执行安装
```
$ dpkg -i zabbix-release_3.2-1+jessie_all.deb

$ apt-get update

$ apt-get install zabbix-server-mysql zabbix-frontend-php
```

###新建数据库
```
$ shell> mysql -uroot -p<password>
$ mysql> create database zabbix character set utf8 collate utf8_bin;
$ mysql> grant all privileges on zabbix.* to zabbix@localhost identified by 'du9283';
$ mysql> quit;
```

###导入数据库
```
$ zcat /usr/share/doc/zabbix-server-mysql/create.sql.gz | mysql -uroot -p zabbix
```

- **修改zabbix_server配置**
>修改 `/etc/zabbix/zabbix_server.conf` 文件中数据库密码

```
DBPassword = du9283
```

- **重启 zabbix 服务生效**

```
$ systemctl restart zabbix-server
```

- **设置zabbix网站**

>在nginx配置中增加zabbix网站:`/usr/share/zabbix`
```
$ cp /etc/nginx/sites-available/defaule /etc/nginx/sites-available/zabbix

$ ln -s /etc/nginx/sites-available/zabbix /etc/nginx/sites-enabled

$ vim /etc/nginx/sites-enabled/zabbix

################################################
server {
        listen 80;
        listen [::]:80;

        root /usr/share/zabbix;

        index index.php index.html index.htm;

        server_name _;

        location / {
                try_files $uri $uri/ /index.php;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        }

        location ~ /\.ht {
                deny all;
        }
}
#################################################
$ systemctl reload nginx
```

>运行网站

>修改php配置
```
$ vim /etc/php/7.0/fpm/php.ini
```
>安装php扩展:`php-bcmath`

>安装代理`zabbix-agent`
```
$ apt-get install zabbix-agent
```

