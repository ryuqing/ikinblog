---
layout:     post
title:      "php sphinx coreseek安装配置"
subtitle:   "sphinx 安装配置"
date:       2017-07-28 12:00:00
author:     "Ikin"
catalog: laravel
tags:
    - php
    - sphinx
    - coreseek
    - 搜索
---

> 最近在看搜索，各种方案都想试一遍。这里首先介绍一下coreseek(sphinx)

##  概念介绍 ##
* sphinx: 
Sphinx是开源的搜索引擎，它支持英文的全文检索。
* coreseek: 
单独搭建Sphinx，已经可以使用全文索引了。但是往往我们要求的是中文索引,且英文分词都是按照空格来的而中文需要根据语义来搜索内容,这时候一般我们都会用coreseek(Coreseek实际上的内核还是Sphinx)。只不过，现在coreseek不维护了，官网打不开。所有的知识都只能通过博客来获取了。慎入！！！


##  安装coreseek ##

1. 下载压缩包 我下载的是` coreseek-3.2.14`,官网打不开了，自己网上搜索吧

2. 安装必要依赖

```
yum -y install autoconf automake
aclocal
yum -y install libtool
aclocal
libtoolize –force
automake --add-missing
make
make install
```

3. 安装中文分词mmseg

```
./bootstrap  
./configure --prefix=/usr/local/mmseg3  
make && make install 
```
安装完可以进去看一下在`/usr/local/mmseg3/etc/unigram.txt`可以看到所有词库分词就是根据这个词库来的

4. 安装coreseek

```
cd ..
cd csft-3.2.14
sh buildconf.sh 
./configure --prefix=/usr/local/coreseek  --without-unixodbc --with-mmseg --with-mmseg-includes=/usr/local/mmseg3/include/mmseg/ --with-mmseg-libs=/usr/local/mmseg3/lib/ --with-mysql  
make
make install
```
编译时可能配到的碰到问题1.

```
sphinxexpr.cpp:1047:43: note: declarations in dependent base ‘Expr_ArgVsSet_c<int>’ are not found by unqualified lookup
sphinxexpr.cpp:1047:43: note: use ‘this->ExprEval’ instead
Makefile:390: recipe for target ‘sphinxexpr.o‘ failed
make[2]: *** [sphinxexpr.o] Error 1
make[2]: Leaving directory ‘/home/lxy/package/coreseek-3.2.14/csft-3.2.14/src‘
```
解决：

```
vim ./src/sphinxexpr.cpp
#替换所有的T val = ExprEval ( this->m_pArg, tMatch ); 为 T val = this->ExprEval ( this->m_pArg, tMatch );
%s/T val = ExprEval ( this->m_pArg, tMatch )/T val = this->ExprEval ( this->m_pArg, tMatch )
#退出重新编译
wq
```

make 时可能碰到的问题

```
/opt/aikaiyuan/coreseek-3.2.14/csft-3.2.14/src/sphinx.cpp:4764: undefined reference to `libiconv’
libsphinx.a(sphinx.o): In function `CSphTokenizer_zh_CN_UTF8_Private::GetConverterOutput(char const*, char const*)’:
/opt/aikaiyuan/coreseek-3.2.14/csft-3.2.14/src/tokenizer_zhcn.h:86: undefined reference to `libiconv_open’
/opt/aikaiyuan/coreseek-3.2.14/csft-3.2.14/src/tokenizer_zhcn.h:89: undefined reference to `libiconv’
libsphinx.a(sphinx.o): In function `xmlUnknownEncoding’:
/opt/aikaiyuan/coreseek-3.2.14/csft-3.2.14/src/sphinx.cpp:20719: undefined reference to `libiconv_open’
/opt/aikaiyuan/coreseek-3.2.14/csft-3.2.14/src/sphinx.cpp:20737: undefined reference to `libiconv’
/opt/aikaiyuan/coreseek-3.2.14/csft-3.2.14/src/sphinx.cpp:20743: undefined reference to `libiconv_close’
libsphinx.a(sphinx.o): In function `CSphTokenizer_zh_CN_GBK::SetBuffer(unsigned char*, int)’:
/opt/aikaiyuan/coreseek-3.2.14/csft-3.2.14/src/sphinx.cpp:4792: undefined reference to `libiconv’
libsphinx.a(sphinx.o): In function `CSphTokenizer_zh_CN_UTF8_Private::GetConverter(char const*, char const*)’:
/opt/aikaiyuan/coreseek-3.2.14/csft-3.2.14/src/tokenizer_zhcn.h:70: undefined reference to `libiconv_open’
/opt/aikaiyuan/coreseek-3.2.14/csft-3.2.14/src/tokenizer_zhcn.h:73: undefined reference to `libiconv’
```
解决办法还是修改配置：

```
#找到LIBS =
LIBS = -lm -lexpat -L/usr/local/lib
#改成
LIBS = -lm -lexpat -liconv -L/usr/local/lib

注释：可能LIBS不一样 中间加上 -liconv
```
至此，安装完成

## 配置 ##

配置基本上和sphinx差不多，只不过coreseek中的配置文件叫 csft.conf,而不是sphinx.conf

```
cd /usr/local/coreseek/etc
cp sphinx.conf.dist csft.conf
vi csft.conf
#配置一个名字叫main的主数据源,main可以自己定，这里就叫main,把src1修改成main就可以,修改的配置如下：
source src1
{
     sql_host                = localhost #mysql主机ip
     sql_user                = test    ＃mysql用户名
     sql_pass                = 123456  #mysql 密码
     sql_db                  = test   #数据库名
     sql_port                = 3306   #端口
     sql_sock                = /tmp/mysql.sock  #如果是linux下需要开启，指定sock文件,这边需要修改为你的mysql.sock文件
     sql_query_pre           = SET NAMES utf8  #mysql检索编码
     sql_query_pre           = SET SESSION query_cache_type=OFF #关闭缓存
     sql_query= \   #获取数据的sql语句
     SELECT id,title,content FROM post
     #sql_attr_uint          = group_id  #对排序的字段进行注释掉
     #sql_attr_timestamp     = date_added #对排序的字段进行注释掉 
     sql_query_info          = SELECT * FROM post WHERE id=$id
}
#今天先不讲增量数据源，先把它关闭
#source src1throttled : src1
#{
#       sql_ranged_throttle     = 100
#}
#接下来配置主数据索引，名字也叫main,把index test1修改下即可
 
index test1
{
     source  = main  #主数据源名称
     path = /usr/local/webserver/sphinx/var/data/main #生成索引的名字，也叫main
     #stopwords                      = G:\data\stopwords.txt
     #wordforms                      = G:\data\wordforms.txt
     #exceptions             = /data/exceptions.txt
     charset_type            = zh_cn.utf-8
     charset_dictpath        = /usr/local/mmseg3/etc  #中文分词所在的路径
}
 
#增量索引和分布式索引也先注释，先不用它
#这里推荐个小技巧，用快的方法加注释
#:set nu
#:627,631s/^/#/g  (给627到631行的代码，添加注释)
#index test1stemmed : test1
#{
#       path                    = /usr/local/webserver/sphinx/var/data/test1stemmed
#       morphology              = stem_en
#}
＃ index dist1
#{
    #分布式索引也给注释掉
    ....
#}
#接下来修改索引器
indexer
{
    mem_limit               = 128M #(这里是128m，根据实际情况而定)
}
#接下来是searchd
searchd
{
    ...根据默认的就可以，这里不用修改
}
####配置概要####
好了之后，修改配置完毕，保存退出
:x
```

## 测试 ##

生成索引

```
/usr/local/coreseek/bin/indexer --all
```

进行索引

```
/usr/local/coreseek/bin/search '人民'
```
如果要从PHP脚本检索索引测试:
运行守护进程searchd，PHP脚本需要连接到searchd上进行检索:

```
$ cd /usr/local/coreseek/etc
$ /usr/local/coreseek/bin/searchd
```

运行PHP API 附带的test 脚本（运行之前请确认searchd守护进程已启动）

要写sphinx在你的php脚本里起作用有两种方式，1是`include('sphinxapi.php')`;或者直接安装sphinx扩展
因为本人用的是php7没有找到相匹配的sphinx客户端所以下面仅介绍使用api的例子：

```
$ cd /源代码安装目录/coreseek/api
$ vim test3.php

<?php
 $key = 'number';
 include('sphinxapi.php');
 $sp=new SphinxClient();
 $sp->SetServer('localhost',9312);
 $sp->SetArrayResult(true); //返回的结果集为数组
 $sp->SetMatchMode(SPH_MATCH_ALL);  //匹配模式 ANY为关键词自动拆词，ALL为不拆词匹配（完全匹配）
 $sp->SetSortMode(SPH_SORT_RELEVANCE);
 $res=$sp->Query($key,'*'); //星号为所有索引源
 var_dump($res);
?>
# 运行
$ php test3.php
可以看到搜索结果
```

> PS：因为coreseek已经不维护了，而我需要上生产环境，不是玩玩而已。网上能找到的新资料实在太少，我已经转[Elasticsearch](https://es.xiaoleilu.com/)搜索方案有很多种，现在阿里出了个search服务更方便。要是资源有限的话，小公司的可以用那个便宜且容易维护，自己没有开发搜索引擎的必要。


参考文档

- <http://blog.csdn.net/wepe12/article/details/52931842>
- <http://www.wuzexin.cn/post-57.html>
- <https://www.ddhigh.com/php/2017/02/28/php7-compile-sphinx-extension.html>
- <http://blog.csdn.net/slqgenius/article/details/51964586>



