---
layout:     post
title:      "websocket 服务器搭建"
subtitle:   "websocket"
date:       2018-05-06 12:00:00
author:     "Ikin"
catalog: php
tags:
    - php
    - websocket
    - tcp
    - wss
    - nodejs
---
> 背景：需要实现websocket长连接的数据传输，同时解耦应用依赖

### 一、socket网络知识
#### 什么是socket？    
    socket 是套接字即ip地址与端口的结合描述协议（RFC 793） 
    涵盖了stream socket 和datagram socket （tcp，udp） 
#### socket与tcp、udp
    tcp是面向连接的通讯协议，通过三次握手建立的连接通讯完成时拆除连接。由于是面向连接的，所以只能端到端通讯
    udp是面向无连接的协议，由于通讯不需要连接，所以可以实现广播发送，并不局限于端到端
### 二、安装

### 三、部署
`因选则ws，遂用pm2管理websocket服务器`

* 参考文档及拓展阅读  
[1.rabbitmq文档](https://rabbitmq.shujuwajue.com/tutorials_with_php/[3]Publish_Subscribe.md.html)  
[2.Mac OS安装RabbitMQ](https://blog.csdn.net/u011186019/article/details/70918288)
[3.RabbitMQ+PHP演示实例](https://www.cnblogs.com/miketwais/p/RabbitMQ.html)
[4.读书笔记](https://laravelacademy.org/resources/notebook)
[5.rabbitmq](http://www.php.cn/php-weizijiaocheng-375956.html)
[6.nodejs websocket测试工具](https://blog.csdn.net/dai_jing/article/details/52095974)
[7.使用四种框架分别实现百万websocket常连接的服务器](https://colobu.com/2015/05/22/implement-C1000K-servers-by-spray-netty-undertow-and-node-js/#node-js)