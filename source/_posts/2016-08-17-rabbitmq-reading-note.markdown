---
layout: post
title: "rabbitmq reading note"
date: 2016-08-17 13:43:35 +0800
comments: true
tags:
- rabbitmq
- note
- tech
categories: tech
---

### 背景
之前在game workshop的时候，有做这方面的技术调研，了解了下这方面的技术细节。不过阅读的内容都存在Evernote里面，趁着翻阅的时候，把资料再掏出来。

### blog
- [AMQP的基础概念][1]
- [网易系列分析（重点看下队列积压的处理）][2]
- [AMPQ协议中文版][3]
- [学习笔记（部署方案值得借鉴）][4]
- [基于HAPROXY的分流+全局镜像队列的方式，简单粗暴][5]
- [rabbitmq集群方案中文版][6]
- [Rabbitmq集群高可用测试（集群方案比较简单）][7]
- [\[Erlang 0086\] RabbitMQ 集群: 从零开始][8]
- [\[Erlang 0085\] RabbitMQ 集群: data and metadata （rabbitmq的设计理念超棒）][9]
  - RabbitMQ in Action 里面提到RabbitMQ两个设计目标,需要自己琢磨一下
    - allowing consumers and producers to keep running in the event one Rabbit node dies
    - linearly scaling messaging throughput by adding more nodes.
  - 在集群中的一个Rabbit节点当掉的时候允许Consumer和producer继续运行;以及通过增加节点的线性扩展方式增加消息吞吐能力.
  - RabbitMQ会提供一些机制比如把exchange queue设置为durable,persistent mode设置为2,等等来尽可能保证消息不丢失,但是这种保证是有限的.即使你该做的都做了,RabbitMQ节点当掉还是有可能出现消息丢失的情况.这是因为RabbitMQ并没有在节点之间做数据复制(Data Replicate).没有特殊的配置(后面会提到Mirrored Queue),一个队列的数据只会出现在Queue所属的节点.
- [Highly Available Queues][10]

### 集群方案
- [rabbitmq的集群方案有几个点][11]
  - node可以选择disk或者ram模式，disk模式存储元数据（比如exchange、queue、channel）等。而ram节点不存储任何数据到disk上面（如果配置了queue数据为永久性的话，则还是会存？？）。
  - 元数据是会在节点间不断的同步的，所以文档建议可以采用一个disk节点，多个ram节点的方式来达到一个比较好的性能。
  - rabbitmq对client的要求是，client可以选择连接到一个proxy，proxy再分发到集群内部的任意一个节点。但是这个工作必须是我们自己动手搞，不过可以借用一些开源的工具实现，比如HAPROXY、Nginx。
  - queue内部的数据，分发的模式好像有两种，一种是完全的同步，一种按需同步。完全同步就是所有节点的队列数据都是一样的，按需索取是当连接某个node的queue被消费的时候，再从disk节点处获取数据。官网的推荐做法是按需同步，这样主要是为了横向扩展的时候，可以支持更多的并发连接，以及减少集群内部的网络通讯，降低消息分发的延迟。

  <!-- more -->

  - 当然这里面有一些风险需要注意，比如集群节点的管理，要时刻注意disk node必须是最后一个关掉，以保证消息、元数据的完整性（但我觉得不用完全同步的话，不太可能。就算完全同步，也会存在信息丢失，只是这个概率就很低了吧。总感觉可以通过log日志，向mysql那类学习。顶多就回档，来个两步提交之类的方法？？）
  - 镜像队列：如果为一个队列建立多个镜像，前者称为主节点，后者称为从节点。如果主节点有问题，那么RabbitMQ会从从节点中选择最早同步的一个作为新的主节点，以保证尽量不丢失消息，然而原主节点中同步之前的消息还是会丢失。
  - 原文有几个比较经典的说法：
    - All data/state required for the operation of a RabbitMQ broker is replicated across all nodes, for reliability and scaling, with full ACID properties. An exception to this are message queues, which by default reside on the node that created them, though they are visible and reachable from all nodes. To replicate queues across nodes in a cluster, see the documentation on high availability (note that you will need a working cluster first).
    - In general, this aspect of managing the connection to nodes within a cluster is beyond the scope of RabbitMQ itself, and we recommend the use of other technologies designed specifically to solve these problems.
    - A node can be a disk node or a RAM node. (Note: disk and disc are used interchangeably. Configuration syntax or status messages normally use disc.) RAM nodes keep their state only in memory (with the exception of queue contents, which can reside on disc if the queue is persistent or too big to fit in memory). Disk nodes keep state in memory and on disk. As RAM nodes don't have to write to disk as much as disk nodes, they can perform better. However, note that since the queue data is always stored on disc, the performance improvements will affect only resources management (e.g. adding/removing queues, exchanges, or vhosts), but not publishing or consuming speed. Because state is replicated across all nodes in the cluster, it is sufficient (but not recommended) to have just one disk node within a cluster, to store the state of the cluster safely.

### 坑
- 关于配置。
setcookie要配对，rabbitmqctl也要跟着一样，不然一直报“node down”的错误信息，但是这个其实是cookie不配对导致的。

### 问题
- durable queue的问题。
关于client连接server端，建立durable queue之后，重连其它server导致的信息丢失问题，好像不存在，这个要再考察下。

### 性能
- [rabbitmq和redis做后端通讯组件的性能对比（redis性能比rabbitmq性能高出一大截）][12]
- [官方的一些测试][13]
- [淘宝对rabbitmq的测试wiki][14]

### 其他
  - rabbitmq有集群方案，有3种集群模式可以选择
  - 存储层主要是利用erlang自带的数据库Mnesia，本身是支持分布式的架构
  - 对mqtt协议的支持是通过第三方插件的形式来完成的
  - 从文档来看，rabbitmq支持CAP三种的多种情况。比如支持AP，放弃C。因为在P的情况下，数据是独立开，每个节点的数据以它们分区主节点的数据为准，并继续提供服务，并不停止运行。而最终的一致性，需要人工操作恢复。
  - 插件开发参考文档
      - [AMQP-基本概念篇][15]
      - [MQTT协议笔记之连接和心跳][16]
      - [AMQP Client for Erlang][17]
      - [AMQP 0-9-1 Quick Reference][18]
      - [AMQP 0-9-1 Complete Reference Guide][19]

[1]: http://log.medcl.net/item/2010/12/rabbitmq-introduction-and-use-of-python-client-how-to-u…
[2]: http://backend.blog.163.com/blog/static/202294126201322215551999/
[3]: http://blog.csdn.net/zhangxinrun/article/details/6546479
[4]: http://dc0127.blog.163.com/blog/static/11217896201322394228794/
[5]: http://www.tuicool.com/articles/YbYvIj
[6]: http://blog.chinaunix.net/uid-22312037-id-3479210.html
[7]: http://www.cnblogs.com/flat_peach/archive/2013/04/07/3004008.html
[8]: http://www.cnblogs.com/me-sa/archive/2012/11/12/2766700.html
[9]: http://www.cnblogs.com/me-sa/archive/2012/11/11/2765539.html
[10]: http://www.rabbitmq.com/ha.html
[11]: https://www.rabbitmq.com/clustering.html#auto-config
[12]: http://floss.zoomquiet.io/data/20110714104018/index.html
[13]: http://www.rabbitmq.com/blog/2012/04/17/rabbitmq-performance-measurements-part-1/
[14]: http://mysql.taobao.org/index.php/Rabbitmq
[15]: http://inter12.iteye.com/blog/1596608
[16]: http://www.blogjava.net/yongboy/archive/2014/02/09/409630.html
[17]: http://www.rabbitmq.com/releases/rabbitmq-erlang-client/v3.2.3/doc/
[18]: https://www.rabbitmq.com/amqp-0-9-1-quickref.html
[19]: https://www.rabbitmq.com/amqp-0-9-1-reference.html
