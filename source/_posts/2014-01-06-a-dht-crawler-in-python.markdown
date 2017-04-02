---
layout: post
title: "How to write a dht crawler in python"
date: 2014-01-06 15:36
comments: true
tags: ['dht', 'net', 'bt', 'python']
categories: tech
---

I recently write [a dht crawler][1] in python.

What a [dht][2]?well,you can think of it as a protocol as we treated the bt.
And i am not gona tell you that i can't understand it before i read [the source code in python][4] that implement it.
LOL...It is the truth, the paper sometimes is just hard to read.

I call it the "bt-share", meaning that it provide the seeds search service for people. <!--more-->

It composite three parts:the web interface,the dht protocol crawler,and the bt downloader.

The basic constructure is shown below:
<img src="/images/dht/mdht.png" alt="dht part structure"/>

It is inspired by the [dht-crawler][3] written in erlang.I draw a pic of its structrure.
<img src="/images/dht/dhtcrawler.png" alt="dhtcrawler structure"/>


[1]: https://github.com/zhkzyth/BT-Share
[2]: http://www.bittorrent.org/beps/bep_0005.html
[3]: https://github.com/kevinlynx/dhtcrawler
[4]: https://github.com/gsko/mdht
