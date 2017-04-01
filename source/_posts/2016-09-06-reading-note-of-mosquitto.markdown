---
layout: post
title: "reading note of mosquitto"
date: 2016-09-06 21:57:22 +0800
comments: true
tags:
- mosquitto
- reading
- note
- tech
categories: tech
---

## 背景
之前在游戏公司的时候，需要自己搭建一套推送服务，顺道研究了下一些开源实现。mosquito的代码库，代码量少，而且也写得比较好懂，对推送协议的实现也是比较ok的，所以就撸了一番。虽然后来没用上，但是还是把当时的一些想法记录下来。

## 缺点
```
- 基于poll的事件模型，没有epoll的性能好
- 内存分配策略简单，来一个生成一个，存在优化空间
    - 比如改用初始2倍，到达一定数量后，以后每次改用增加额定数量的算法
- 数据结构过于简单
    - 常用结构为单向链表，查找耗时为O（n），存在性能问题
- 内存占用过大
    - 没有使用数据库，数据都堆在内存里面，可能无法应付大数据量
```

<!--more-->

## 其他人的观点
```
- poll -> epoll ，用以支持更高的并发；
- 改为单线程版本，降低锁开销，目前锁开销还是非常大的。目测可以改为单进程版本，类似redis，精心维护的话应该能达到不错的效果；
- 网络数据读写使用一次尽量多读的方式，避免多次进入系统调用；
- 内存操作优化。不free，留着下次用；
- 考虑使用spwan-fcgi的形式或者内置一次启动多个实例监听同一个端口。这样能更好的发挥机器性能，达到更高的性能；
```

## 性能
```
- [ES2 中型][http://aws.amazon.com/ec2/instance-types/] 支持100k的并发连接
```

## 其他
```
- https://lists.launchpad.net/mosquitto-users/maillist.html 邮件列表
- http://www.blogjava.net/yongboy/archive/2014/02/09/409630.html MQTT协议学习笔记
- http://chenzhenianqing.cn/articles/985.html   mosquitto的讲解，超赞！
```

## 改进方案
```
- redis （http://redisbook.readthedocs.org/en/latest/index.html）
    - 参考redis的sds处理，用空间换时间
- HTTPSQS （http://goaheadtw.iteye.com/blog/1669682）
    - 消息队列的实现，比较短小
```

## 挖了一个小坑
- [更详细的讲解][https://github.com/zhkzyth/better-mosquitto]
