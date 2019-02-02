---
layout:     post
title:      "xdebug"
subtitle:   "PhpStorm+Xdebug+Docker实现断点调试+chrome/postman（Mac平台亲测）"
date:       2018-08-30 12:00:00
author:     "Ikin"
catalog: php
tags:
    - php
    - xdebug
---

> 写在前面：代码调试端能被访问很重要（有独立ip或者直接是本地）所以，最好在你的服务器端 先测试一下,如果连接不成功，先解决网络问题  
`telnet phpstorm机器ip phpstorm配置的debug端口`  


### 原理

### 安装配置

* 1. 安装xdebug，安装哪个版本参考https://xdebug.org/说明。 下述2.3.3可匹配php 5.4    

```
pecl install Xdebug-2.3.3 

vim /usr/local/php/etc/php.ini

[Xdebug]
zend_extension=/usr/local/php/lib/php/extensions/no-debug-non-zts-20100525/xdebug.so
xdebug.remote_enable=1

;机器ip #注意docker虚拟机不能用172.17.0.1直接访问宿主机端口，必须用局域网ip：192.168.199.x
xdebug.remote_host=192.168.199.41

;配置的debug端口（缺省是9000）
xdebug.remote_port=9000

xdebug.remote_handler=dbgp
xdebug.remote_mode=req
xdebug.show_error_trace=on
xdebug.auto_trace=on
xdebug.idekey=PHPSTORM

```

* 2配置phpstorm, 如下图
![配置端口](https://upload-images.jianshu.io/upload_images/752480-ed368fad3d946130..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)
![配置DBGP](https://upload-images.jianshu.io/upload_images/752480-3e0bd1e70a415519..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 3. 安装xdebug_helper插件,开启插件的debug功能。postman需要添加cookie XDEBUG_SESSION=PHPSTORM

* 4. phpstrom-》run -》 start listening for php connections 打开

* 5. 打断点，访问就这么简单
