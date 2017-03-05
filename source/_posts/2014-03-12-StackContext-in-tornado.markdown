---
layout: post
title: "StackContext in Tornado"
date: 2014-03-12 01:10
comments: true
categories: tornado python sourcecode
---

## 背景

最近一直在读tornado的代码，大概是看懂了骨架。关于结构的部分，google一下应该会有比较多的分析，我主要想讲下在tornado/stack_context.py文件里面的StackContext类的作用。

## StackContext是什么

StackContext从字面意义来说，翻译过来，就是“堆栈上下文”。拆开来看，就是用“堆栈”这种数据结构，保存“上下文”。

<!-- more -->

## 为什么提出StackContext这个概念

在tornado的mailing list里面，motor库的作者有说到[这么一点][1]。大概的意思是说，在把函数扔到异步调用里面的时候（[挖坑准备下一篇讲Futures][2]），如果这个callback本身会抛错的话，抛出的异常本身信息是不正确的。而为了修补这个“bug”，于是就引入了StackContext这个概念。

## StackContext用在什么地方

StackCotnext的用法，我一开始看了下[官网的简介][3]，不是很理解。

后来，在tornado的test目录下，有个stack_context_test.py文件，里面写了一整套的测试用例，可以结合来看。你可以看到作者的代码，能清楚的认识到它的作用。

主要作用就是上文提到的保存函数发生的上下文信息。

## 更多的参考资料

- 现代魔法学院有[一系列的文章][4]，可以帮助了解tornado的整体架构
- [我的tornado源码阅读][5]
- [Tornado 的 stack context][6]
- [异步调用中的上下文控制Tornado stack context][7]


PS：在阅读代码的过程中，google了很多其他的资料，这里就不放出来了。大概的意思是，代码还是要自己读，而且主动点去查阅不懂的东西。然后呢，不要怕耗费时间，花点心思是能看懂的。我怎么会告诉你，为了搞懂StackContext类的作用，我基本把整个tornado撸了一遍，耗时超长哦...XD

[1]: https://groups.google.com/forum/#!topic/python-tornado/S12qMWXt9h0
[2]: http://docs.python.org/3/library/concurrency.html
[3]: http://www.tornadoweb.org/en/branch2.3/stack_context.html
[4]: http://www.nowamagic.net/academy/detail/13321002
[5]: https://github.com/zhkzyth/tornado-reading-notes
[6]: http://wangxu.me/blog/p/758
[7]: http://zouyesheng.com/context-in-async-env.html
