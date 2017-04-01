layout: post
title: "notes of learning erlang"
date: 2016-09-06 18:35:38 +0800
comments: true
tags:
- erlang
- notes
- tech
categories: tech
---

## 背景
之前因为要想实现一个基于[DHT网络的站][3]，就找了一些代码实现，发现一个前辈写的erlang实现，好奇也看了起来，顺便学了下erlang。

## 感悟
- erlang 太有趣啦，通过模拟进程创建，看到内存和CPU资源是怎么被吃掉的，給人很直觀的感覺哇~而且erlang同时支持事务和热代码替换，这两项特性碉堡了啊！！！
- erlang的并发编程模式,其实我觉得写起来还是读起来都非常的通俗,上手也不难,但有些约定有点反人类,或者语法有点多,约定俗成的东西还要靠看书来回忆…
- erlang的并发编程模式,有两个关键的地方:server端本身的loop无限循环;提供对外进行调用的rpc接口. 一开始我对rpc的概念是不清楚的,现在可好了,原来它是把客户端发送过来的请求,做了一个转发.
- 那转发给谁呢?  我猜如果是BS架构的话,rpc就转发给对应的server"进程",然后receive轮询,等待对应的pid返回处理结果,rpc再转发回去<!--more-->
- erlang对网络编程的语言层面的支持非常漂亮，对字节的处理都很舒服，隐藏了很多细节
- erlang三大件
```
 面向消息的编程范式（函数式编程风格）
 OTP
     行为接口(behaviour interface)
         gen_server
             [init/1, handle_call/3, handle_cast/2, handle_info/2, terminate/2, code_change/3]
         application
             ….
         ….
     行为实现(behaviour implementation)
         应用程序的逻辑就堆在这里面
     行为容器(behaviour container)
 节点式程序模型
     程序树
     容错
     分布式
     热切换
```
## 资料
- [Erlang OTP 设计原理文档][1]
- [[推荐] Rabbitmq的网络层要点浅析][2]

[1]: http://erlang.shiningray.cn/otp-design-principles/index.html
[2]: http://tech.techweb.com.cn/thread-438919-1-1.html
[3]: https://github.com/zhkzyth/BT-Share
