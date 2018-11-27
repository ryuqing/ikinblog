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

### 一、安装

#### 官方提供各种包安装方式  
[安装方法及最新下载地址](https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html#install-elasticsearch)

* 安装例子    
进入官方下载：https://www.elastic.co/downloads/elasticsearch tar.gz压缩包，也可以直接下载rpm包直接用rpm安装。这里使用可直接执行的压缩包
安装前需要安装jdk8以上  

```
$ wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.4.0.tar.gz
$ unzip elasticsearch-6.4.0.tar.gz
$ cd elasticsearch-6.4.0/ 
./bin/elasticsearch -d #这是以后台程序的方式运行
```

* 注意
rpm安装后 配置文件 存放在 ` ls /etc/elasticsearch/`；执行文件 存放在 `/usr/share/elasticsearch/`；
与直接下载的可执行压缩包文件目录不同


二、 关于相似度评分的理解

#### 词频

词在文档中出现的频度是多少？ 频度越高，权重 越高 。 5 次提到同一词的字段比只提到 1 次的更相关。词频的计算方式如下：
tf(t in d) = √frequency 
`词 t 在文档 d 的词频（ tf ）是该词在文档中出现次数的平方根。`

#### 逆向文档频率
词在集合所有文档里出现的频率是多少？频次越高，权重 越低 。 常用词如 and 或 the 对相关度贡献很少，因为它们在多数文档中都会出现，
一些不常见词如 elastic 或 hippopotamus 可以帮助我们快速缩小范围找到感兴趣的文档。逆向文档频率的计算公式如下：
idf(t) = 1 + log ( numDocs / (docFreq + 1)) 

`词 t 的逆向文档频率（ idf ）是：索引中文档数量除以所有包含该词的文档数，然后求其对数。`

#### 字段长度归一值
字段的长度是多少？ 字段越短，字段的权重 越高 。如果词出现在类似标题 title 这样的字段，要比它出现在内容 body 这样的字段中的相关度更高。

#### 结合使用编辑
以下三个因素——词频（term frequency）、逆向文档频率（inverse document frequency）和字段长度归一值（field-length norm）——是在索引时计算并存储的。
最后将它们结合在一起计算单个词在特定文档中的 权重 。

* [详见 相似度评分背后理论](https://www.elastic.co/guide/cn/elasticsearch/guide/current/scoring-theory.html#idf)


> 网上优秀的文章不少，如果初次使用请把下列文档通读一遍，相信增益不少。我这里权当记录了，后续使用过程中有更深刻的理解再作补充。  

* 参考文档及拓展阅读  
[Elasticsearch权威指南 - 官方中文文档,使用前至少阅读前几章](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html)   --文章基于2.0版本有些可能不对，但依然是很好的书
[Elasticsearch 官方php客户端](https://www.elastic.co/guide/cn/elasticsearch/php/current/index.html)  
[Elasticsearch 简介 - 星爷的博客，收入浅出的文章](http://lxwei.github.io/posts/Elasticsearch-%E7%AE%80%E4%BB%8B.html)；
[搜索引擎选择： Elasticsearch与Solr - 搜索引擎选型调研文档](http://i.zhcy.tk/blog/elasticsearchyu-solr/)
[全文搜索引擎 Elasticsearch 入门教程 - 阮一峰](http://www.ruanyifeng.com/blog/2017/08/elasticsearch.html)