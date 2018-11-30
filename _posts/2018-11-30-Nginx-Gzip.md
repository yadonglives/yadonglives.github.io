---
layout: post
title: "Nginx开启Gzip压缩"
subtitle: 'Nginx，Gzip, 压缩, Nginx开启Gzip压缩'
keyword: 'Nginx，Gzip, nginx gzip 配置, 压缩, Nginx开启Gzip压缩'
author: "Yadong"
header-img: "img/post-bg-alitrip.jpg"
tags:
    - Nginx
---

# 前言 #
买了个阿里云主机，奈何没有钱，配置低，没贷款，流量还很低。但是还是要想着要怎么让资源加载更快，提高体验。那么开启Nginx的压缩功能，就是一个很好的优化点之一。于是写下这篇记录一下安装的过程。

# 网页压缩 #
网页压缩是一项由 WEB 服务器和浏览器之间共同遵守的协议，也就是说WEB服务器和浏览器都必须支持该技术，所幸的是现在流行的浏览器都是支持的，包括 IE、FireFox、Opera 等；服务器有 Apache 和 IIS 等。

双方的协商过程如下：首先浏览器请求某个 URL 地址，并在请求的头 (head) 中设置属性 accept-encoding 值为 gzip, deflate，表明浏览器支持 gzip 和 deflate 这两种压缩方式（事实上 deflate 也是使用 gzip 压缩协议，下面我们会介绍二者之间的区别）。

WEB服务器接收到请求后判断浏览器是否支持压缩，如果支持就传送压缩后的响应内容，否则传送不经过压缩的内容；浏览器获取响应内容后，判断内容是否被压缩，如果是则解压缩，然后显示响应页面的内容。在实际的应用中我们发现压缩的比率往往在 3到10倍，也就是本来50k大小的页面，采用压缩后实际传输的内容大小只有 5 至 15k 大小，这可以大大节省服务器的网络带宽，同时如果应用程序的响应足够快时，网站的速度瓶颈就转到了网络的传输速度上，因此内容压缩后就可以大大的提升页面的浏览速度。

# 配置启用gzip #
vi nginx.conf修改配置：
```
	gzip on;
	gzip_min_length  5k;
	gzip_buffers     4 16k;
	#gzip_http_version 1.0;
	gzip_comp_level 3;
	gzip_types       text/plain application/x-javascript text/css application/xml
	text/javascript application/x-httpd-php image/jpeg image/gif image/png;
	gzip_vary on;
```	
:wq退出保存并退出编辑。然后重启nginx，是配置生效
```
    /usr/local/nginx/sbin/nginx -s reload
```    