---
layout:     post
title:      "php rar 扩展安装"
subtitle:   "如何安装php扩展"
date:       2017-07-25 12:00:00
author:     "Ikin"
catalog: php
tags:
    - php
    - rar
    - php扩展
---
最近写采集的时候要处理文件，之前一直是用命令执行符号来解压文件的。奈何不知道是系统原因还是怎么回事，用命令执行符号在php里运行`rar e` 解压一直被中断，找不到原因。没办法，只有安装rar扩展了，个人习惯很不愿意安装扩展，因为以后维护麻烦。PS：这篇文章也可以作为安装php扩展的教程

#### 下载说一下安装过程吧：
##### 1.下载php扩展包，网址为 http://pecl.php.net/package/rar;
##### 2.首先还是来说一下window版本的,这个容易,直接下载一个dll动态链接库包都放在php扩展包目录里面的,然后改一下配置文件重启就可以了.具体如下:
网址:http://pecl.php.net/package/rar/4.0.2/windows,已经在上面的网址中选择最新版本的rar扩展包,后面有个window DLL的图标点击进去下载,下载后把
里面的php_rar.dll这个文件放在你的php安装目录/php/ext/文件夹里面,然后再修改 php.ini ,在里面加上:`extension=php_rar.dll`

接下来说一下linux安装


##### 3.首先下载：
```
wget http://pecl.php.net/get/rar-4.0.0.tgz

tar -xvf rar-4.0.0.tgz

cd rar-4.0.0
```

##### 4.然后是编译,首页你要安装一个编译插件:
```
#Debianhttp://www.phpyrb.com/Admin-Article-edit.html
apt-get install libc-client-dev
#CentOS
yum install libc-client-devel
```
##### 5.运行phpize准备扩展
注释：phpize是用来扩展php扩展模块的，通过phpize可以建立php的外挂模块   
下面两步很关键,我就是这步没有搞好,所以一直不行,在官方文档里面,安装是直接运行 phpize,这是他包里面默认就有的,我照着他里面做就是不行,这个应该用你安装的php里面的phpize再编译,如果没有请修改你的php安装路径,那么命令应该如下,反正就是用你安装好的php里面的phpize来编辑,不要用包里面自带这的.命令如下:

```
/usr/local/php/bin/phpize
```
出现类似如下提示，继续

```
Configuring for:
PHP Api Version:         20151012
Zend Module Api No:      20151012
Zend Extension Api No:   320151012
```
##### 6.编译并且安装
这步是最关键的,就是告诉编译环境你的php路径,这步非常重要命令如下,如果你没有修改默认路径的话:

```
./configure --with-php-config=/usr/local/php/bin/php-config
make & make install
```
##### 7.修改配置
####### 上面一步执行完之后会在`/usr/local/php/lib/php/extensions/no-debug-non-zts-20151012`目录下生成rar.so文件

```
vi /usr/local/php/etc/php.ini
```
####### 在里面添加
```
extension=rar.so
```
然后保存退出，重启php-fpm（`service php-fpm restart`） 或者 lnmp













