---
layout:     post
title:      "NODEJS学习笔记"
date:       2018-02-07 12:00:00
author:     "Ikin"
catalog: nodejs
tags:
    - Node.js
---

> 哈哈哈，这只是一篇学习笔记。人事充满惊喜，离开了工作时间一年有余的工作环境，准备迎接新的挑战。想着以后的工作可能会用的NODE 遂先学习学习。

### 概念思想
* 简单的说 Node.js 就是运行在服务端的 JavaScript。Node.js 是一个基于Chrome JavaScript 运行时建立的一个平台。
Node.js是一个事件驱动I/O服务端JavaScript环境，基于Google的V8引擎，V8引擎执行Javascript的速度非常快，性能非常好。

### 回调函数
> Node.js 异步编程的直接体现就是回调。异步编程依托于回调来实现，但不能说使用了回调后程序就异步化了。回调函数在完成任务后就会被调用，Node 使用了大量的回调函数，Node 所有 API 都支持回调函数。

创建 main.js 文件, 代码如下：

```
var fs = require("fs");
var data = fs.readFileSync('input.txt');
console.log(data.toString());
console.log("程序执行结束!");
```

以上代码执行结果如下：

```
$ node main.js  
text-content
程序执行结束!
```

### 事件循环
> Node.js 使用事件驱动模型，当web server接收到请求，就把它关闭然后进行处理，然后去服务下一个web请求。在事件驱动模型中，会生成一个主循环来监听事件，当检测到事件时触发回调函数。