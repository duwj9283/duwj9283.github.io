---
layout: post
title: centos7.2下安装php phalcon 扩展
tags:
- php
- phalcon
categories: php
description: centos7.2下安装php phalcon 扩展
---
##主题介绍
centos7.2下安装php phalcon 扩展

<!-- more -->
##安装git
```
$ yum install php-dev git
$ mkdir build && cd build
```
##git下载phalcon扩展

- **此phalcon默认是php7下的扩展，如果当前php是5.5，请选择`cd cphalcon/build/php5/64bits`**

```
$ git clone https://github.com/phalcon/cphalcon.git \
    && cd cphalcon/build/php7/64bits \
    && phpize \
    && ./configure \
    && make \
    && make install

$ bash -c 'echo "extension=phalcon.so" > /alidata/server/php/etc/php.ini'

$ service httpd restart
```


