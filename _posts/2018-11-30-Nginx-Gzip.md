---
layout: post
title: "Nginx开启Gzip压缩"
subtitle: 'Nginx，Gzip, 压缩, Nginx开启Gzip压缩'
keyword: 'Nginx，Gzip, nginx gzip 配置, 压缩, Nginx开启Gzip压缩'
author: "Yadong"
header-img: "img/post-bg-swordsman.jpg"
header-color: "#ef9811"
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
gzip_types       text/plain application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
gzip_vary on;
```	
:wq退出保存并退出编辑。然后重启nginx，是配置生效
```
/usr/local/nginx/sbin/nginx -s reload
```

# 配置注释 #

**gzip**

gzip on|off
默认值: gzip off 
开启或者关闭gzip模块

**gzip_static**

gzip_static on|off
nginx对于静态文件的处理模块
该模块可以读取预先压缩的gz文件，这样可以减少每次请求进行gzip压缩的CPU资源消耗。该模块启用后，nginx首先检查是否存在请求静态文件的gz结尾的文件，如果有则直接返回该gz文件内容。为了要兼容不支持gzip的浏览器，启用gzip_static模块就必须同时保留原始静态文件和gz文件。这样的话，在有大量静态文件的情况下，将会大大增加磁盘空间。我们可以利用nginx的反向代理功能实现只保留gz文件。可以百度"nginx gzip_static"了解更多

**gzip_comp_level**

默认值：1(建议选择为4)
gzip压缩比/压缩级别，压缩级别 1-9，级别越高压缩率越大，当然压缩时间也就越长（传输快但比较消耗cpu）。

**gzip_buffers**

默认值: gzip_buffers 4 4k/8k 
设置系统获取几个单位的缓存用于存储gzip的压缩结果数据流。 

例如：
4 4k， 代表以4k为单位，按照原始数据大小以4k为单位的4倍申请内存。 
4 8k 代表以8k为单位，按照原始数据大小以8k为单位的4倍申请内存。
如果没有设置，默认值是申请跟原始数据相同大小的内存空间去存储gzip压缩结果。

**gzip_types** 
gzip_types mime-type [mime-type ...]

默认值: gzip_types text/html (默认不对js/css文件进行压缩)
压缩类型，匹配MIME类型进行压缩，不能用通配符 text/*
(无论是否指定)text/html默认已经压缩，设置哪压缩种文本文件可参考 conf/mime.types

**gzip_min_length**

默认值: 0 ，不管页面多大都压缩
设置允许压缩的页面最小字节数，页面字节数从header头中的Content-Length中进行获取。
建议设置成大于1k的字节数，小于1k可能会越压越大。 即: gzip_min_length 1024

**gzip_http_version** 

gzip_http_version 1.0|1.1
默认值: gzip_http_version 1.1(就是说对HTTP/1.1协议的请求才会进行gzip压缩)
识别http的协议版本。由于早期的一些浏览器或者http客户端，可能不支持gzip自解压，用户就会看到乱码，所以做一些判断还是有必要的。 
> 注：99.99%的浏览器基本上都支持gzip解压了，所以可以不用设这个值,保持系统默认即可。
> 假设我们使用的是默认值1.1，如果我们使用了proxy_pass进行反向代理，那么nginx和后端的upstream
> server之间是用HTTP/1.0协议通信的，如果我们使用nginx通过反向代理做Cache
> Server，而且前端的nginx没有开启gzip，同时，我们后端的nginx上没有设置gzip_http_version为1.0，那么Cache的url将不会进行gzip压缩

**gzip_proxied** 

gzip_proxied [off|expired|no-cache|no-store|private|no_last_modified|no_etag|auth|any] ...
默认值：off

Nginx作为反向代理的时候启用，开启或者关闭后端服务器返回的结果，匹配的前提是后端服务器必须要返回包含"Via"的 header头。
off - 关闭所有的代理结果数据的压缩
expired - 启用压缩，如果header头中包含 "Expires" 头信息
no-cache - 启用压缩，如果header头中包含 "Cache-Control:no-cache" 头信息
no-store - 启用压缩，如果header头中包含 "Cache-Control:no-store" 头信息
private - 启用压缩，如果header头中包含 "Cache-Control:private" 头信息
no_last_modified - 启用压缩,如果header头中不包含 "Last-Modified" 头信息
no_etag - 启用压缩 ,如果header头中不包含 "ETag" 头信息
auth - 启用压缩 , 如果header头中包含 "Authorization" 头信息
any - 无条件启用压缩

**gzip_vary**

gzip_vary on
和http头有关系，加个vary头，给代理服务器用的，有的浏览器支持压缩，有的不支持，所以避免浪费不支持的也压缩，所以根据客户端的HTTP头来判断，是否需要压缩

**gzip_disable**

gzip_disable "MSIE [1-6]."
禁用IE6的gzip压缩，又是因为杯具的IE6。当然，IE6目前依然广泛的存在，所以这里你也可以设置为“MSIE [1-5].”
IE6的某些版本对gzip的压缩支持很不好，会造成页面的假死，今天产品的同学就测试出了这个问题
后来调试后，发现是对img进行gzip后造成IE6的假死，把对img的gzip压缩去掉后就正常了
为了确保其它的IE6版本不出问题，所以建议加上gzip_disable的设置
