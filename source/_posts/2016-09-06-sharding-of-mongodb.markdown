---
layout: post
title: "sharding of mongodb"
date: 2016-09-06 18:21:09 +0800
comments: true
categories: mongodb sharding notes
---
## blog教程
- [Mongodb集群配置(sharding with replica set)][1]
- [MongoDB副本集学习][2]
- [Mongodb分片实践][3]
- [mongoDB 选择一个合适的片键, 官方的slideshare,还不错][4]
- [mongodb的內部實現][5]
- [MongoDB 的内部构造 From MongoDB The Definitive Guide][6]
- [mongodb nosqlfans的索引][7]
- [mongo分布式图文教程，整个blog系列的文章都很不错][8]
- [为什么你不应该使用 MongoDB][9]

## 分片概念
集合可以被分片,一个片可以包含多个区间,一个区间可以包含多个块,一个块可以定大小.所有这些概念都是逻辑上的,真实的物理存储并不存在这样的一一对应关系.如下图:
```
     分片1----
               |
               |
           +++++++++++
           +  |------ 块1   +     区间1
           +  |------ 块2   +
           +++++++++++
               |
               |
           +++++++++++
           +  |------ 块3   +     区间2
           +  |------ 块4   +
           +++++++++++
               |
               |
           +++++++++++
           +  |------ 块5   +     区间3
           +  |------ 块6   +
           +++++++++++

          逻辑概念与物理概念上并不一致
```
<!--more-->

## 片键选择问题
- 分布式中,容易出现数据重复,即除了片键以外,很难保证唯一性.
- <深入学习mongodb>中给了个推论,"除非在_id"上分片,否则能够创建出不唯一的_id值.
- [演讲][http://www.mongodb.com/presentations/sharding-overview]
- [演讲二][http://www.mongodb.com/presentations/sharding-best-practices-advanced-session]

## Geo location
- [性能][http://liubin.org/2013/02/26/geo_data_type_and_performance_in_mongodb]

## 建立索引
- [索引的建立，好坏，场景][http://www.itokit.com/2011/1207/72718.html]

## 源码阅读进度
- 看了下整个架构
    - 基于c++的一个项目，采用的库有boost……
    - 多线程模型，通过锁机制来完成任务的调度
    - 事件循环采用最传统select机制
        - 每个客户端一个threading，更优化的可能地方在内部的连接线程池，可以加快响应速度
    - [参考][http://blog.csdn.net/daizhj/article/category/803114]

## 经验记录
- 传统的master、slave模式已经被弃用，mongo推荐采用副本集，即replica set。
    - 它的使用规则还是非常的简易。
        - 主从可以互相切换，当然也支持不切换的。
        - 第三方调用默认读写都走主，但可以调配副本提供读。这样就能有效分流读写的压力。
        - 副本集的提出主要是为了数据备份。
    - mongos的提出是为了sharding的，它也是mongodb里面的节点类型之一。（PS:mongodb有许多节点类型，单个数据库实例算一种，提供查询的config也是一种等等）
        - sharding是为了把大量的数据从单台机分离出来，切分数据到不同的机子上去。避免内存被撑爆了。
        - 未完待续

[1]: http://blog.csdn.net/zhangzhaokun/article/details/6269514
[2]: http://www.cnblogs.com/magialmoon/p/3261849.html
[3]: http://blog.csdn.net/ctf_htj/article/details/17077723
[4]: http://www.slideshare.net/mongodb/choosing-a-shard-key
[5]: http://blog.nosqlfan.com/html/2079.html
[6]: http://blog.nosqlfan.com/html/740.html
[7]: http://blog.nosqlfan.com/html/3548.html
[8]: http://www.lanceyan.com/tech/mongodb/mongodb_cluster_1.html
[9]: http://www.oschina.net/translate/why-you-should-never-use-mongodb?lang=chs&page=3
