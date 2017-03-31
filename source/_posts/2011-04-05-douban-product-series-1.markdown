---
layout: post
title: "豆瓣系列专题[一]"
date: 2011-04-05 20:06:26 +08:00
tags:
- douban
- product
categories: product
---

背景
----
从大一开始认识豆娘到现在，发现蛮喜欢她的，做了两年多的知识记录，回过头来看，内心满是踏实的感觉。泡了两年，豆娘也是一路风雨兼程。一直想给这么个产品写自己的体验，见证豆娘的美丽和成熟，不过每每下笔，又怕人笑幼稚，好吧，就酱紫，不许笑，难得有机会去写这么一个系列的体验。(如果你对豆瓣不太了解的话，先看看[豆瓣的发展历程][douban_history]，这样对豆瓣会清晰点。我对豆瓣的理解是，她是一个以书、影、音分享为主的知识型社区，以兴趣聚合同好。当然阿北的想法我是无法猜测的......)
<!--more-->

<div class="align-center"><iframe id="xmindshare_embedviewer" src="http://xmind.net/share/_embed/zhkzyth/fun-watercress-111111111122222222223333333333444444444450/" frameborder="0" scrolling="no" width="600px" height="300px"></iframe></div>

我说广场
------------
我说广场是豆瓣书、影、音资源的一个流通场所，在小站、阿尔法城出现之前，它承担着用户互动、信息流通等重任。

我觉得比较有趣的地方，是豆瓣对用户之间的互动所采取的策略。
<p  class="align-center"><img class="aligncenter" title="douban image" src="http://farm6.static.flickr.com/5064/5593178518_c1d958c1fe.jpg" alt="douban image" width="453" height="279" /></p>
<p style="text-align: center;"><span style="color: #c0c0c0;">&lt;图1-1&gt;</span></p>

在图1_1里面我们可以看到，用户发布相片和推荐网址这两种行为，用户的互动性是不同的。基于豆瓣资源的推荐，豆瓣鼓励用户去消费，而不是简单的互动，所以这里是没有“回应”动作的。

但作为一个社区来讲，这样不太合理。无论是用户的行为，还是用户的资源推荐，其实作为社区中的我们都会有好奇心，都有互动的愿望，应该鼓励用户的互动行为，“回应”是很好的动作，但如果有其他的互动动作会不会更好呢?比如facebook的用户互动行为。

<p style="text-align: center;"><img class="aligncenter" title="facebook content" src="http://farm6.static.flickr.com/5310/5593178542_9e9ce7b45b_z.jpg" alt="facebook content" width="453" height="279" /></p>
<p style="text-align: center;"><span style="color: #c0c0c0;">facebook的“赞”、“评论”、“分享”</span></p>

我关注的人和关注我的人
-----------------------
<p style="text-align: center;"><span style="font-size: medium;"><strong><img class="aligncenter" title="douban followers page" src="http://farm6.static.flickr.com/5264/5593171848_438e4f612a_z.jpg" alt="douban followers page" width="453" height="279" /></strong></span></p>
<p style="text-align: center;"><strong> </strong><span style="color: #c0c0c0;">&lt;图2-1&gt;</span></p>

我关注和关注我的人都是豆瓣里面比较被人忽视的两个页面，但对于我来说，我觉得这两个页面蛮重要的。

观察过我同学用豆瓣的行为，和我自己使用的经验，这个页面的作用我觉得有两个：

1. 个人通信录
2. 管理自己的信息来源

但其实，我会有这样的需求，我会想了解我关注的人，他，都关注了谁。显然，图2_1无法满足这样的需求。让我们看看twitter是怎么做的？

<p style="text-align: center;"><img class="aligncenter" title="twitter followers page" src="http://farm6.static.flickr.com/5063/5592581003_dc8120f1ec.jpg" alt="twitter followers page" width="453" height="279" /></p>
<p style="text-align: center;"><span style="color: #c0c0c0;">&lt;图2-2&gt;</span></p>

在follow页面，无论是我follow的，还是follow我的，twitter都会显示有效的用户信息，即*用户的description*、*名字*、*头像*
，去掉干扰的信息(比如国内围脖比较喜欢的年龄、性别、跟随者个数这样的无效信息)。在信息的隐藏方面，当用户有意愿想去进一步了解对方的时候，点击一下对方的按钮，就会在右部窗口打开用户的详细信息。

可以看到，这样的页面有助于用户去方便快捷地了解到follower对象的信息，帮助用户获得优质的信息来源。

豆瓣的消息提醒机制
------------------
豆瓣社区的用户之间的互动不是实时的，而且豆瓣也缺乏这样的实时的沟通机制。

另外，用户是基于兴趣聚合起来，而不是像facebook那样的熟人网络构建起来的，互动性相对较弱。

所以，用户在这样的社区里面，会有种虚无的存在感。

我想到的一些解决方案，可以参考twitter和国内的一些围脖网站的做法。利用Ajax技术，异步调回最新的数据信息，利用声音提醒之类的，提示用户豆瓣主页有信息，用户可以不用刷新，减少体验的痛感。

<p style="text-align: center;"><img class="aligncenter" title="twitter ajax" src="http://farm6.static.flickr.com/5266/5592581043_e782590bd6_z.jpg" alt="twitter ajax" width="477" height="116" /></p>
<p style="text-align: center;"><span style="color: #c0c0c0;">&lt;图3-1&gt;</span></p>

豆列
----
我喜欢豆娘一个很重要很重要的原因是她有豆列，可以帮我更好的做知识整理。但反观豆列的操作，我觉得可以有改进的余地，一个页面可以完成的操作，分成3个页面去做实在让人费解。虽然豆娘用不方便表达了自己希望优质的愿望，但对于有心想加书的人来说，真是件苦事。

[douban_history]:http://www.douban.com/group/topic/1024317
