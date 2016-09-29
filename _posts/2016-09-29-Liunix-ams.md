---
layout: post
title: centos7.2下安装ams
tags:
- centos
- ams | fms
categories: linux
description: centos7.2下安装ams，支持rtmp的点播和推流直播
---
##主题介绍
centos7.2下安装ams，支持rtmp的点播和推流直播

<!-- more -->
##序列号
```
1652-5580-8001-8333-2201-1631
```
##下载软件包
- **百度网盘下载地址**
>http://pan.baidu.com/s/1i55AHxV   密码：7z6c

##执行安装
```
$ tar xvf AdobeMediaServer5_x64.tar.gz -C /tmp/

$ cd /tmp/AdobeMediaServer5_x64 && sudo ./installAMS

####注：用户选择httpd进程的用户www；组也是www(不安装AMS内置的apache，apache选择n)

$ sudo systemctl enable ams

$ rm -r /tmp/AdobeMediaServer5_x64
```

##注意事项
- **centos 7.0以下适用**
>centos 7.0一下不支持 `systemctl` ，改成 `service ams restart`