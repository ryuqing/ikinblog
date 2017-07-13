---
layout:     post
title:      "laravel 消息队列详解"
subtitle:   " \"laravel队列qumeue\""
date:       2017-07-04 12:00:00
author:     "Ikin"
catalog: laravel
tags:
    - php
    - CGI
    - FastCGI
    - PHP-FPM
---
在搭建 LAMP/LNMP 服务器时，会经常遇到 PHP-FPM、FastCGI和CGI 这几个概念。如果对它们一知半解，很难搭建出高性能的服务器。接下来我们就以图形方式，解释这些概念之间的关系。

####基础
在整个网站架构中，Web Server（如Apache）只是内容的分发者。举个栗子，如果客户端请求的是 index.html，那么Web Server会去文件系统中找到这个文件，发送给浏览器，这里分发的是静态数据。  
![GitHub Mark](http://oschina.online/img/im-post/server-html.png "introduct")

如果请求的是 index.php，根据配置文件，Web Server知道这个不是静态文件，需要去找 PHP 解析器来处理，那么他会把这个请求简单处理，然后交给PHP解析器。