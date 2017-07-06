---
layout:     post
title:      "supervisor 守护 php artisan进程"
subtitle:   " \"supervisor配置与使用\""
date:       2017-07-04 12:00:00
author:     "Ikin"
catalog: laravel
tags:
    - Linux
    - 消息队列
    - supervisor
---
## 介绍
>Supervisor 是一个 Python 写的进程管理工具，有时一个进程需要在后台运行，并且意外挂掉后能够自动重启，就需要这么一个管理进程的工具。在 Laravel 开发中，也经常使用到队列监听，可以配合 Supervisor 来管理 Laravel 队列进程。  

>要客旅游（接口,商户后台, 管理后台）laravel队列命令统一用进程管理工具supervisor守护

## Ubuntu安装
`sudo apt-get install supervisor`

## Supervisor配置

1. 打开默认配置文件  
`sudo vim /etc/supervisor/supervisord.conf`  

2. 翻到最后一行配置监控目录  
`[include]
files = /etc/supervisor/conf.d/*.conf`

3. `cd ./conf.d` 配置各个项目需要监控的进程  
e.g. 配置商户端命令程序监控目录

```
[program:yaoktravel-merchant]
command                 =  php artisan queue:work --delay=2 --sleep=3 --tries=3 --daemon  //你要监控的命令程序
directory               = /srv/merchant/  你的文件目录
process_name            = %(program_name)s_%(process_num)s
numprocs                = 6
autostart               = true
autorestart             = true
stdout_logfile          = /srv/merchant/storage/logs/supervisor_yaoktravel.log
stdout_logfile_maxbytes = 10MB
stderr_logfile          = /srv/merchant/storage/logs/supervisor_yaoktravel.log
stderr_logfile_maxbytes = 10MB
```
## 启动监控程序
```
启动程序：
supervisord -c /etc/supervisord.conf 
启动监控项目：
sudo supervisorctl start yaoktravel-merchant:*
```
## 常用命令
```
supervisorctl shutdown 关闭supervisor
supervisorctl //为客户端命令，可以监控远程服务，也可以监控本地服务 必选要先启动本地服务，本地进程才能被监控
sudo supervisorctl reread //重新载入配置
sudo supervisorctl update //更新配置文件
sudo supervisorctl start yaok-travel-admin:* //启动程序
supervisorctl status [program] 查看program进程 状态

supervisorctl help 命令可以查看可用命令
```

*配置参考（http://www.tuicool.com/articles/y2iAze）