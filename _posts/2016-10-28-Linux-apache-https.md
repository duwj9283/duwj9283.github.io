---
layout: post
title: apache 下使用配置 https
tags:
- apache
- https
categories: linux
description: apache 下使用配置 https 验证证书
---
##主题介绍
apache 下使用配置 https 验证证书

<!-- more -->

##下载证书安装工具
- **下载地址**

> git clone https://github.com/Neilpang/acme.sh.git .acme.sh

##执行安装
```
$ ln -s /root/.acme.sh/acme.sh /usr/local/bin/

$ acme.sh --issue --dns -d www.***.com --keylength  ec-384

注：这步执行成功后会生成一个记录类型是text的_acme-challenge.www，需要到域名解析这个text

$ acme.sh --renew  -d  www.***.com --ecc

注：这步会生成合并证书和key
[Fri Oct 28 14:07:41 CST 2016] Your cert is in  /root/.acme.sh/www.iguangj.com_ecc/www.iguangj.com.cer 
* [Fri Oct 28 14:07:41 CST 2016] Your cert key is in  /root/.acme.sh/www.iguangj.com_ecc/www.iguangj.com.key 
[Fri Oct 28 14:07:42 CST 2016] The intermediate CA cert is in  /root/.acme.sh/www.iguangj.com_ecc/ca.cer 
* [Fri Oct 28 14:07:42 CST 2016] And the full chain certs is there:  /root/.acme.sh/www.iguangj.com_ecc/fullchain.cer 

```

##修改apache配置
- ** 通过火狐的设置工具：`https://mozilla.github.io/server-side-tls/ssl-config-generator` **

```
$ vim http.onfig
{
    Listen 443;
    LoadModule ssl_module modules/mod_ssl.so;
    LoadModule socache_shmcb_module modules/mod_socache_shmcb.so;
    
    # modern configuration, tweak to your needs
    SSLProtocol             all -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite          ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256
    SSLHonorCipherOrder     on
    SSLCompression          off
    
    
    # OCSP Stapling, only in httpd 2.3.3 and later
    SSLUseStapling          on
    SSLStaplingResponderTimeout 5
    SSLStaplingReturnResponderErrors off
    SSLStaplingCache        shmcb:/var/run/ocsp(128000)
}

$ vim vhost --> iguangj.conf

{
<VirtualHost *:443>
    SSLEngine on
    SSLCertificateFile      /root/.acme.sh/www.iguangj.com_ecc/fullchain.cer
    SSLCertificateKeyFile   /root/.acme.sh/www.iguangj.com_ecc/www.iguangj.com.key

    # Uncomment the following directive when using client certificate authentication
    #SSLCACertificateFile    /path/to/ca_certs_for_client_authentication

    # HSTS (mod_headers is required) (15768000 seconds = 6 months)
    Header always set Strict-Transport-Security "max-age=15768000"
        DocumentRoot /alidata/www/iguangj
        ServerName www.iguangj.com
        <Directory "/alidata/www/iguangj">
            Options Indexes FollowSymLinks
            AllowOverride all
            Order allow,deny
            Allow from all
        </Directory>
        ErrorLog "/alidata/log/httpd/iguangj-error.log"
        CustomLog "/alidata/log/httpd/iguangj.log" common
</VirtualHost>
<VirtualHost *:80>
        Redirect permanent / https://www.iguangj.com/
        ServerName www.iguangj.com
</VirtualHost>

}

$ 重启 httpd
```
