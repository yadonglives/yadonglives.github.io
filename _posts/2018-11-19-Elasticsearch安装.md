---
layout: post
title: "Elasticsearch安装"
subtitle: 'centos7.2 x64，Elasticsearch安装'
author: "YaDong"
header-img: "img/post-bg-alitrip.jpg"
tags:
    - Elasticsearch
---

## 安装 ##


----------


### 1. 准备 ###

 - Elastic 需要 Java 8 环境 
 - 此次Elasticsearch使用版本为6.5.0，点击[[下载链接]][1]。

----------


### 2. 安装 ###

** java8安装**
>    点击查看[[java8安装]][2]。

**elasticsearch安装**
将  elasticsearch-6.5.0.tar.gz 上传到服务器


    tar -zxvf elasticsearch-6.5.0.tar.gz

    mv elasticsearch-6.5.0 /opt/elasticsearch
    cd /opt/elasticsearch/bin/

**修改配置**

    vim config/elasticsearch.yml
 

> 修改内容(没有就添加):

    cluster.name: my-application
    node.name: node-1
    network.host: 0.0.0.0
    http.port: 9200

> CentOS6配置：因为Centos6不支持SecComp,而ES默认bootstrap.system_call_filter为true进行检测，需要将其关闭。

    bootstrap.memory_lock: false
    bootstrap.system_call_filter: false

 
**修改/etc/sysctl.conf，在文件最后面添加内容:**

    vim /etc/sysctl.conf
    vm.max_map_count=262144

退出，刷新生效

    sysctl -p

**修改文件/etc/security/limits.conf**


    vim /etc/security/limits.conf


> 添加如下内容：

    * soft nproc 2048
    * hard nproc 4096


## 3. 启动 ##

> 添加启动用户，否则会报异常：can not run elasticsearch as root。
这是因为处于系统安装考虑的设置，由于Elasticsearch可以接收用户输入的脚本并且执行，为了系统安全考虑，不允许root账号启动，所以建议给Elasticsearch单独创建一个用户来运行Elasticsearch。


**创建新用户**

> useradd 用户名 -g 组名 -p 密码

    groupadd elasticsearch
    useradd  es -g elasticsearch -p es

**授权访问组**

    chown -R es:elasticsearch /opt/elasticsearch

**启动**


    ./bin/elasticsearch


```
cd /opt
```

  [1]: https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.5.0.tar.gz
  [2]: https://www.cuiyadong.com/2018/11/19/jdk1.8-linux/
