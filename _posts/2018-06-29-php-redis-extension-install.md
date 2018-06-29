---
layout:     post
title:      "lnmp 环境下php多版本共存及其php扩展安装"
subtitle:   "如何安装php扩展"
date:       2018-06-25 12:00:00
author:     "Ikin"
catalog: php
tags:
    - php
    - redis
    - php扩展
---

##### lnmp php 多版本 https://lnmp.org/faq/upgrade1-4.html

##### 更改nginx 将里面的include enable-php.conf; 替换为 include enable-php7.1.conf; 

##### /etc/init.d/php-fpm7.1 start

##### 添加redis扩展

* 1.官网相关扩展包下载  
  https://pecl.php.net/
  
* 2.参考前面php-rar扩展安装（注意路径） [参考](http://oschina.online/2017/07/25/php-rar-extension-install/)
    














