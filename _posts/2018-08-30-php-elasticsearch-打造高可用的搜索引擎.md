---
layout:     post
title:      "Elasticsearch"
subtitle:   "如何安装php扩展"
date:       2018-08-30 12:00:00
author:     "Ikin"
catalog: php
tags:
    - php
    - elasticsearch
---

### 安装
```
进入官方下载：https://www.elastic.co/downloads/elasticsearch tar.gz压缩包，也可以直接下载rpm包直接用rpm安装。这里使用可直接执行的压缩包
安装前需要安装jdk8以上
$ wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.4.0.tar.gz
$ unzip elasticsearch-6.4.0.tar.gz
$ cd elasticsearch-6.4.0/ 
./bin/elasticsearch -d #这是以后台程序的方式运行

* 注意
rpm安装后 配置文件 存放在 ` ls /etc/elasticsearch/`；执行文件 存放在 `/usr/share/elasticsearch/`；
与直接下载的可执行压缩包文件目录不同
```

> 网上优秀的文章不少，如果初次使用请把下列文档通读一遍，相信增益不少。我这里权当记录了，后续使用过程中有更深刻的理解再作补充。
* 参考文档及拓展阅读  
[Elasticsearch权威指南 - 官方中文文档,使用前至少阅读前几章](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html)   --文章基于2.0版本有些可能不对，但依然是很好的书
[Elasticsearch 官方php客户端](https://www.elastic.co/guide/cn/elasticsearch/php/current/index.html)  
[Elasticsearch 简介 - 星爷的博客，收入浅出的文章](http://lxwei.github.io/posts/Elasticsearch-%E7%AE%80%E4%BB%8B.html)；
[搜索引擎选择： Elasticsearch与Solr - 搜索引擎选型调研文档](http://i.zhcy.tk/blog/elasticsearchyu-solr/)
[全文搜索引擎 Elasticsearch 入门教程 - 阮一峰](http://www.ruanyifeng.com/blog/2017/08/elasticsearch.html)