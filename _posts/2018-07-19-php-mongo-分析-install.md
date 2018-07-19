---
layout:     post
title:      "mongodb 之慢查询分析"
subtitle:   "如何安装php扩展"
date:       2018-07-19 12:00:00
author:     "Ikin"
catalog: php
tags:
    - php
    - mongo
---
#### 导读 
* MongoDB与内存: mongodb会把数据文件映射到内存中，如果是读操作，内存中的数据起到缓存的作用，如果是写操作，内存还可以把随机的写操作转换成顺序的写操作，总之可以大幅度提升性能。
MongoDB并不干涉内存管理工作，而是把这些工作留给操作系统的虚拟内存管理器去处理，这样做的好处是简化了MongoDB的工作，但坏处是你没有方法很方便的控制MongoDB占多大内存，幸运的是虚拟内存管理器的存在让我们多数时候并不需要关心这个问题。  
MongoDB的内存使用机制让它在缓存重建方面更有优势，简而言之：如果重启进程，那么缓存依然有效，如果重启系统，那么可以通过拷贝数据文件到/dev/null的方式来重建缓存   
 
* mongo索引: e.g. `db.person.createIndex( {age: 1} )` MongoDB会额外存储一份按age字段升序排序的索引数据，索引结构类似如下，
索引通常采用类似btree的结构持久化存储，以保证从索引里快速（ O(logN)的时间复杂度 ）找出某个age值对应的位置信息，然后根据位置信息就能读取出对应的文档。


#### 1 慢查询分析流程
慢查询日志一般作为优化步骤里的第一步。通过慢查询日志，定位每一条语句的查询时间。比如超过了200ms，那么查询超过200ms的语句需要优化。
然后它通过 .explain() 解析影响行数是不是过大，所以导致查询语句超过200ms。
#####所以优化步骤一般就是： 
1.用慢查询日志（system.profile）找到超过200ms的语句
2.然后再通过.explain()解析影响行数，分析为什么超过200ms 
3.决定是不是需要添加索引

### 2 开启慢查询

#### 2.1 Profiling级别说明
0：关闭，不收集任何数据。 
1：收集慢查询数据，默认是100毫秒。  
2：收集所有数据  

#### 2.2 开启Profiling和设置

##### 通过mongo shell:
需要进入server 
* 查看状态：级别和时间  
```
PRIMARY> db.getProfilingStatus()
{ "was" : 1, "slowms" : 200 }
#查看级别
PRIMARY> db.getProfilingLevel()
1
#设置级别
PRIMARY> db.setProfilingLevel(2)
{ "was" : 1, "slowms" : 100, "ok" : 1 }
#设置级别和时间
PRIMARY> db.setProfilingLevel(1,200)
{ "was" : 2, "slowms" : 100, "ok" : 1 }
```

* 注意：
a. 以上要操作要是在test集合下面的话，只对该集合里的操作有效，要是需要对整个实例有效，则需要在所有的集合下设置或则在开启的时候开启参数
b. 每次设置之后返回给你的结果是修改之前的状态（包括级别、时间参数）。

##### 不通过mongo shell:
在mongoDB启动的时候
mongod --profile=1 --slowms=200
或则在配置文件里添加2行：

profile = 1
slowms = 200

#### 3 修改“慢查询日志”的大小
```
#关闭Profiling
PRIMARY> db.setProfilingLevel(0)
{ "was" : 0, "slowms" : 200, "ok" : 1 }
#删除system.profile集合
PRIMARY> db.system.profile.drop()
true
#创建一个新的system.profile集合 --- 4M
PRIMARY> db.createCollection( "system.profile", { capped: true, size:4000000 } )
{ "ok" : 1 }
#重新开启Profiling
PRIMARY> db.setProfilingLevel(1)
{ "was" : 0, "slowms" : 200, "ok" : 1 }
```

* Profiling功能与效率  
Profiling功能肯定是会影响效率的，但是不太严重，原因是他使用的是system.profile 来记录，而system.profile 是一个capped collection， 这种collection 在操作上有一些限制和特点，但是效率更高。

#### 4 分析 (略)

* 参考文档及拓展阅读
[1.mongodb Profiling 通过慢查询日志分析查询慢的原因 相应优化](http://blog.51cto.com/qiangsh/2052609)  
[2.mongodb监控工具mongostat](https://blog.csdn.net/u011186019/article/details/70918288)