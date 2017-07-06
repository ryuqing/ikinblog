---
layout:     post
title:      "laravel 消息队列详解"
subtitle:   " \"laravel队列qu meue\""
date:       2017-07-04 12:00:00
author:     "Ikin"
catalog: laravel
tags:
    - Laravel,queue
---

###一、介绍
Laravel 的队列服务为不同的队列后端系统，比如 Beanstalk，Amazon SQS，Redis，甚至是关系型数据库，提供了一套统一的 API 。队列允许你将一个耗时的任务进行延迟处理，例如像 e-mail 发送。这能让应用程序对页面的请求有更快的响应。

队列的配置文件被保存在 config/queue.php 中。在这个文件内你可以找到包含在 Laravel 中的每一种队列驱动的连接配置。队列驱动包括数据库、Beanstalkd、IronMQ、Amazon SQS、Redis 以及 synchronous 驱动（ 本地使用 ）。 队列驱动也可以配置为 null，这样就表示丢弃队列任务。

###二、驱动的设置
为了测试观察，建议将驱动设置为database，本文主要以mysql为驱动  
生成队列驱动表     `php artisan queue:table`  
生成存储失败任务表  `php artisan queue:failed-table`  
运行数据迁移       `php artisan migrate`

###三、配置
* .env配置  

```
DB_CONNECTION = mysql_lxg_v1_tool （此配置为你的数据连接名，注意是数据库连接名)

QUEUE_DRIVER = database （默认值是sync，注意这个参数值，有些时候你发现你的队列怎么都不走异步，请查看是否有更改这个参数）
```
* queue.php配置  
当然.env里的文件配置也可以在queue里面进行修改

```
默认驱动设置：  
'default' => env('QUEUE_DRIVER', 'sync') 

任务失败队列存储设置：  
'failed' => [
    'database' => env('DB_CONNECTION', 'mysql'),
    'table' => 'manage_failed_jobs',
],
```

###四、测试
* 事件文件

```
<?php
namespace App\Events;
class TestEvent
{
    /**
     * @var string
     */
    public $keywords;
    /**
     * AfterSearchEvent constructor.
     *
     * @param $keywords
     * @param $userID
     */
    public function __construct($keywords)
    {
        $this->keywords = $keywords;
    }
}
```
* 监听文件

```
<?php
namespace App\Listeners;

use App\Events\TestEvent;
use App\Exceptions\CreateFailedException;
use Illuminate\Contracts\Queue\ShouldQueue;
class TestListener implements ShouldQueue
{
    /**
     * Handle the event.
     * @param TestEvent $event
     */
    public function handle(TestEvent $event)
    {
        $message = $event->keywords;

//        throw new CreateFailedException('eee');

        app('log')->info($message);
    }
}
```
* 当然别忘了配置对应的事件和监听

* 开始测试  
php artisan queue:work --tries=1 --daemon
在代码的任意一个地方触发刚刚我们写的事件 `event(new TestEvent('开始测试了'));`

###错误模拟
事情不可能一番风顺，因为实际生产环境中监听大部分会发外部网络请求（如短信，邮件）所以你的监听可能会出现错误，这里抛出一个异常来模拟错误。或者你可以把你的日志文件权限改为只读。
注意代码里面 `throw new CreateFailedException('eee')`

注意观察数据表，会有一条失败的任务写入

###php artisan queue:work 命令参数详解


