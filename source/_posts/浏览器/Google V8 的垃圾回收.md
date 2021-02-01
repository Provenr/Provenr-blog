---
title: Google V8 的垃圾回收
tags:
  - V8 引擎
  - 垃圾回收
categories:
  - 浏览器
date: 2021-01-22 23:36:49
modified: 2021-01-22 23:36:56
---

而 Google 研发的 V8 JavaScript 引擎通过采取一系列关键技术，大大提升了 JavaScript 的执行速度，关键技术包括 JIT 编译 （JIT Compile）、[垃圾回收（Garbage Collection）](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science))、内嵌缓存（Inline Cache）、隐藏类等

### 1、**什么是垃圾回收**

对运行的应用程序实施动态的内存管理。定期检查分配给应用程序的内存，确定哪些数据不再需要，并清除出来，以腾出内存空间。这个过程被称为垃圾回收。

#### 	解决什么问题

​		大幅简化程序的内存管理代码，降低程序员的负担，减少因长时间运转而带来的内存泄露问题。提高应用程序的响应能力。

### 2、**什么时候执行垃圾回收**

> 垃圾回收任务是在没有影响用户体验的情况下，在Chrome 的空闲时间内被执行。

​		Chrome 浏览器闲置状态判断是通过[任务调度器（Task Scheduler）](http://blog.chromium.org/2015/04/scheduling-tasks-intelligently-for_30.html)。任务调度器可以评估Chrome 什么时候可能空闲，以及预计会空闲多久。任务调度器优先 处理 延迟敏感的任务，处于闲置时，处理空闲任务（Idle Tasks）。

​		举个例子，屏幕更新速率为60 帧每秒（FPS），即Chrome 大概每16.6ms 来进行一帧更新。为当前帧执行输入和渲染的工作用时不到16.6ms，剩下的时间内， Chrome 浏览器处于闲置状态。

### 3、**深入了解V8 的垃圾回收引擎**

V8 采用了一个[分代（Generational）垃圾回收器](http://www.memorymanagement.org/glossary/g.html#term-generational-garbage-collection)，将内存堆分割为新生代（Young Generation）和老生代（Old Generation）。分别对新生代对象和老生代对象使用不同的垃圾回收算法来提升垃圾回收的效率

#### 新生代（Young Generation）

​	1、存活时间较短的对象

​	2、对象起初会被分配在新生代内存区（通常很小，只有1-8 MB，具体根据任务分配）。

​	3、使用[半空间（Semi-space）](http://www.memorymanagement.org/glossary/s.html#semi.space)分配策略，新对象最初分配在新生代的活跃半空间内。一旦半空间已满，一个Scavenge 操作将活跃对象移出到其他半空间中，被认为是长期驻存的对象，并被晋升为老生代。一旦活跃对象已被移出，则在旧的半空间中剩下的任何死亡对象被丢弃。

####老生代（Old Generation）

​	1、存活时间较长或常驻内存的对象

​	2、老生代中的垃圾回收采用[标记 - 清除（Mark-Sweep）](http://www.memorymanagement.org/glossary/m.html#term-mark-sweep)和 Mark-Compact 相结合的策略。

​	3、V8 使用了一种[增量标记的方式标记活跃对象](http://blog.chromium.org/2011/11/game-changer-for-interactive.html) ，将完整的标记拆分，垃圾回收与应用线程交替执行，每个标记步骤的持续时间低于 5ms。避免一次性全部标记，造成了浏览器一段时间无响应。

#### 总结

有四个主要的垃圾回收任务：

1. 新生代对象的 Scavenge，这通常是快速的；
2. 通过增量方式的标记步骤，依赖于需要标记的对象数量，时间可以任意长；
3. 完整垃圾回收，这可能需要很长的时间；
4. 带内存紧缩的完整垃圾回收，这也可能需要很长的时间，需要进行内存紧缩。

### 参考
1. [infoQ-Google-v8](https://www.infoq.cn/article/2015/08/google-v8)
2. [极客时间-图解谷歌 V8](https://time.geekbang.org/column/article/230845?utm_source=related_read&utm_medium=article&utm_term=related_read)
