---
layout: post
title: centos7.2下安装文档转换工具
tags:
- centos
- java
categories: linux
description: centos7.2下安装文档转换工具,word、ppt、pdf转换成jpg图片
---
##主题介绍
centos7.2下安装文档转换工具,实现word、ppt、pdf转换成jpg图片，并设置服务器开机启动进程守护。脚本jar包持续运行中

<!-- more -->
##安装java
- **java下载地址**

>http://download.oracle.com/otn-pub/java/jdk/8u102-b14/jdk-8u102-linux-x64.rpm

- **rmp包安装**

```
$ rpm -ivh jdk-8u102-linux-x64.rpm

$ java -version
```

##文档转换设置
- **jar包及数据库配置下载地址**

> 链接: http://pan.baidu.com/s/1kVGwa5T 密码: `rn4h`

###服务配置
- **jar包及数据库移动到`/alidata/lib`文件夹下**
```
$ vim /etc/systemd/system/convert-java.service
{
    [Unit]
    Description=Document Convert Service
    Requires=httpd.service
    After=httpd.service

    [Service]
    ExecStart=/usr/bin/java -jar convert-java.jar
    Restart=always
    RestartSec=10
    User=www
    Group=www
    #Environment=
    WorkingDirectory=/alidata/lib

    [Install]
    WantedBy=multi-user.target
}

$ systemctl daemon-reload

$ systemctl enable convert-java   //注：service httpd stop -->systemctl restart httpd）

$ systemctl start convert-java
```

###注意事项
- **httpd服务用`systemctl`自身的`httpd`**
>手动编译的`apache` 跟服务器的`httpd`可能路径配置不同，需要把服务器的`httpd`改成手动编译的`httpd`
```
$ vim /etc/systemd/system/httpd.service
{
    [Unit]
    Description=The Apache HTTP Server
    After=network.target remote-fs.target nss-lookup.target
    Documentation=man:httpd(8)
    Documentation=man:apachectl(8)

    [Service]
    Type=forking
    #EnvironmentFile=/alidata/server/httpd/bin/envvars
    ExecStart=/alidata/server/httpd/bin/apachectl start
    ExecReload=/alidata/server/httpd/bin/apachectl graceful
    ExecStop=/alidata/server/httpd/bin/apachectl graceful-stop
    # We want systemd to give httpd some time to finish gracefully, but still want
    # it to kill httpd after TimeoutStopSec if something went wrong during the
    # graceful stop. Normally, Systemd sends SIGTERM signal right after the
    # ExecStop, which would kill httpd. We are sending useless SIGCONT here to give
    # httpd time to finish.
    #KillSignal=SIGCONT
    #PrivateTmp=true

    [Install]
    WantedBy=multi-user.target
}
```