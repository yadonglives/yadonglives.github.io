---
layout: post
title: "centos nginx 安装"
subtitle: 'Nginx, nginx linux安装, centos nginx'
keyword: 'Nginx, nginx linux安装, centos nginx'
author: "Yadong"
tags:
    - Nginx
---

# 简介 #
Nginx是一款轻量级的网页服务器、反向代理服务器。相较于Apache、lighttpd具有占有内存少，稳定性高等优势。它最常的用途是提供反向代理服务。

# 安装 #
>   在Centos下，yum源不提供nginx的安装，可以通过切换yum源的方法获取安装。也可以通过直接下载安装包的方法，
>   以下命令均需root权限执行：首先安装必要的库（nginx 中gzip模块需要 zlib 库，rewrite模块需要 pcre 库，ssl 功能需要openssl库）。
>   选定/usr/local为安装目录，以下具体版本号根据实际改变。



### 安装前的准备 ###

- pcre，本次版本为：pcre-8.37，该文件为正则表达式库。让nginx支持rewrite需要安装这个库，[点击下载][4]

- openssl，本次下载版本使用1.0.1j，[点次下载][1]，

- nginx，本次下载nginx版本为nginx-1.14.2[点击下载][3]

- 确保进行了安装了linux常用必备支持库。在CentOS安装软件的时候，可能缺少一部分支持库，而报错。这里首先安装系统常用的支持库。那么在安装的时候就会减少很多的错误的出现。

```shell
yum install -y gcc gdb strace gcc-c++ autoconf libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs patch e2fsprogs-devel krb5-devel libidn libidn-devel openldap-devel nss_ldap openldap-clients openldap-servers libevent-devel libevent uuid-devel uuid mysql-devel    
```




### 安装PCRE库 ###

```shell
tar -zxvf pcre-8.37.tar.gz
cd pcre-8.37
./configure
make && make install
```



### 安装SSL库 ###
```shell
tar -zxvf openssl-1.0.1j.tar.gz
cd openssl-1.0.1j
./config
make && make install
```



### 安装nginx ###

```shell
tar -zxvf nginx-1.14.2.tar.gz 
cd nginx-1.14.2
./configure --user=nobody --group=nobody --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_gzip_static_module --with-http_realip_module --with-http_sub_module --with-http_ssl_module

make && make install
```
**注**: --with-http_ssl_module:这个不加后面在nginx.conf配置ssl:on后,启动会报nginx: [emerg] unknown directive "ssl" in /opt/nginx/conf/nginx.conf 异常)



# 常见问题 #

### 启动时报错 ###
```shell
./nginx: error while loading shared libraries: libpcre.so.1: cannot open shared object file: No such file or directory
```

执行命令，查看缺少到文件
```shell
ldd $(which /usr/local/nginx/sbin/nginx)
```
显示libpcre.so.1 => not found，执行命令
```shell
ln -s /usr/local/lib/libpcre.so.1 /lib64
```
如果是32位系统，执行命令
```shell
ln -s /usr/local/lib/libpcre.so.1 /lib
```


  [1]: http://www.openssl.org/source/openssl-1.0.1j.tar.gz
  [3]: http://nginx.org/download/nginx-1.14.2.tar.gz
  [4]: http://jaist.dl.sourceforge.net/project/pcre/pcre/8.37/pcre-8.37.tar.gz
