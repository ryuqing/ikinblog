---
layout:     post
title:      "laravel 消息队列详解"
subtitle:   " \"laravel队列qumeue\""
date:       2017-07-04 12:00:00
author:     "Ikin"
catalog: laravel
tags:
    - Laravel
    - queue
    - 消息队列
---

### 一、介绍
Laravel 的队列服务为不同的队列后端系统，比如 Beanstalk，Amazon SQS，Redis，甚至是关系型数据库，提供了一套统一的 API 。队列允许你将一个耗时的任务进行延迟处理，例如像 e-mail 发送。这能让应用程序对页面的请求有更快的响应。

队列的配置文件被保存在 config/queue.php 中。在这个文件内你可以找到包含在 Laravel 中的每一种队列驱动的连接配置。队列驱动包括数据库、Beanstalkd、IronMQ、Amazon SQS、Redis 以及 synchronous 驱动（ 本地使用 ）。 队列驱动也可以配置为 null，这样就表示丢弃队列任务。

### 二、驱动的设置
为了测试观察，建议将驱动设置为database，本文主要以mysql为驱动  
生成队列驱动表     `php artisan queue:table`  
生成存储失败任务表  `php artisan queue:failed-table`  
运行数据迁移       `php artisan migrate`

### 三、配置
##### .env配置  

```
DB_CONNECTION = mysql_lxg_v1_tool （此配置为你的数据连接名，注意是数据库连接名)

QUEUE_DRIVER = database （默认值是sync(`同步`)，注意这个参数值，有些时候你发现你的队列怎么都不走异步，请查看是否有更改这个参数）
```
##### queue.php配置  
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

### 四、测试
##### 事件文件

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
##### 监听文件

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
##### 当然别忘了配置对应的事件和监听

##### 开始测试  
php artisan queue:work --tries=1 --daemon
在代码的任意一个地方触发刚刚我们写的事件 `event(new TestEvent('开始测试了'));`

##### 错误模拟
事情不可能一番风顺，因为实际生产环境中监听大部分会发外部网络请求（如短信，邮件）所以你的监听可能会出现错误，这里抛出一个异常来模拟错误。或者你可以把你的日志文件权限改为只读。
注意代码里面 `throw new CreateFailedException('eee')`

注意观察数据表，会有一条失败的任务写入

### 五、队列命令及参数

##### queue:listen 和 queue:work –daemon 的区别  
queue:work 默认只执行一次队列请求, 当请求执行完成后就终止;
queue:listen 监听队列请求, 只要运行着, 就能一直接受请求, 除非手动终止;
queue:work –daemon 同 listen 一样, 只要运行着, 就能一直接受请求, 不一样的地方是在这个运行模式下, 当新的请求到来的时候, 不重新加载整个框架, 而是直接 fire 动作.
能看出来, queue:work –daemon 是最高级的, 一般推荐使用这个来处理队列监听.

注意: 使用 queue:work –daemon , 当更新代码的时候, 需要停止, 然后重新启动, 这样才能把修改的代码应用上.


##### 参数解释
```
 --queue[=QUEUE]           The queue to listen on   监听的队列
      --daemon             Run the worker in daemon mode (Deprecated)  以后台进程的方式运行
      --once               Only process the next job on the queue      
      --delay[=DELAY]      如果一个任务失败了，那么它会延迟几秒后再重新执行。此时间的缺省值为「0」，也就是说不延迟。通常这不是一个好选择，比如遭遇网络不稳定，此时一旦失败，如果不延迟立刻重试，多半还是会失败。建议设置为「1」
      --force              Force the worker to run even in maintenance mode
      --memory[=MEMORY]    The memory limit in megabytes [default: "128"]
      --sleep[=SLEEP]      Number of seconds to sleep when no job is available [default: "3"]
      --timeout[=TIMEOUT]  The number of seconds a child process can run [default: "60"]
      --tries[=TRIES]      这个参数一定要设置 如果一个任务失败了，那么重试几次。此次数的缺省值为「0」，不过它的含义可不是不重试，而是不断重试。某些时候，如果问题比较严重，不断重试就等同于死循环。建议设置为「3」。

* 最终生产环境命令配置： `php artisan queue:work --delay=1 --sleep=1 --tries=3 --daemon`
```

### 六、遇见的坑
1. 没有配置 --tries, 结果可想而知，怎么都没出现不了错误任务
2. 用写日志来测试监听, 例如生成了某个日志文件然后把日志文件删除了，没有新建一个日志文件写日志。但是程序显示执行成功了，没有失败任务。  
原因解释：（后台队列 worker 在处理每个任务时不重启框架，如果修改了代码,在后台队列中是无效的,必须重启队列）
