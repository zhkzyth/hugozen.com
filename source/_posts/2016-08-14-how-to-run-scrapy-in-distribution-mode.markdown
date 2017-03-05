---
layout: post
title: "how to run scrapy in distribution mode"
date: 2016-08-14 17:08:10 +0800
comments: true
categories: scrapy python redis cached
---
From previous post, i wrote things that i learned from [Scrapy][1] architecture.

At this one, I try to explain how to run Scrapy in distribution mode.Or even better, try to write your own spiders code to support that.

So i once say that, Scrapy has 4 parts to get things work.But why 4 parts, you may wonder?

Let's just forget about Scrapy, and use `worker + consumer` [pattern][3] to get a deeper understanding of how spider works.

Spiders are the `worker`.They try to crawl pages, producing more and more links to crawl, which we call tasks, right? All the spiders just crawl the page, get new links and store them in one place, and store the target content in another place.And go on crawling.

Then who is the `consumer`, and what dose the "places" mean?

Well, to `consumer`, you can choose any things that can get content from "places" and do stuff on it, like the `Item Pipeline` part of Scrapy. Or writ your code to consume.

<!--more-->

What's the "places"? It can be mysql table, a FIFO queue based on [Redis][5], or others things that has lock support.The idea is to explore all our content to workers and consumers, so they can share the same content.With lock support, we can ensure that, no race condition would happen here.

So, is there any libraries can help?

If you are using the Scrapy framework, [scrapy-redis][4] would be a nice one to watch.I use [rediscrapy][5] in my project.

If not, try to understand what is the pattern of `worker + consumer`, and write your own one.

And finally, here are the articles i once read:
- [ scrapy-redis分布式爬虫原理分析][7]

[1]: http://scrapy.org/
[2]: https://www.instagram.com/?hl=en
[3]: https://en.wikipedia.org/wiki/Producer%E2%80%93consumer_problem
[4]: https://pypi.python.org/pypi/scrapy-redis
[5]: http://redis.io/
[6]: https://github.com/chrox/ecolect/tree/master/rediscrapy
[7]: http://blog.csdn.net/zzk1995/article/details/51786558
