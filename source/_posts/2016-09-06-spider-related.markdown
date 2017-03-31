---
layout: post
title: "spider related"
date: 2016-09-06 18:06:27 +0800
comments: true
tags: python spider notes
categories: tech
---

## 怎么部署
- scrapyd + supervisord + crontab + redis

## 可以用的一些lib
- [weibo的登录可以学下][1]
- [这个爬虫的架构可以学下，基于队列的master和jobber模式来实现分布式][2]
- [怒赞的一个爬虫框架，基于tornado，妈的，这个得好好学习][3]
- [去重的逻辑用下这个库来搞][4]
- [部署、控制相关的用这个][5]

## 分布式
- [基于redis的方案][6]

<!--more-->

## 参考的blog
- [这个家伙专门搞爬虫的样子][7]
- [scrapy的优劣][8]

## 入门
- [入门文章][9]

## 结合
- [与机器学习结合，值得参考][10]

## 行业资料
- [xitu][11]

## 行业需求
- [lagou]

## 其他
- 比如如何防止被ban掉
```
Here are some tips to keep in mind when dealing with these kinds of sites:
- rotate your user agent from a pool of well-known ones from browsers (google around to get a list of them)
- disable cookies (see COOKIES_ENABLED) as some sites may use cookies to spot bot behaviour
- use download delays (2 or higher). See DOWNLOAD_DELAY setting.
- if possible, use Google cache to fetch pages, instead of hitting the sites directly
- use a pool of rotating IPs. For example, the free Tor project or paid services like ProxyMesh
- use a highly distributed downloader that circumvents bans internally, so you can just focus on parsing clean pages. One example of such downloaders is Crawlera
```

[1]: https://github.com/yoyzhou/weibo_scrapy
[2]: https://github.com/chineking/cola
[3]: https://github.com/binux/pyspider
[4]: https://github.com/darkrho/scrapy-redis
[5]: https://github.com/ftao/dotcloud-scrapyd
[6]: https://pypi.python.org/pypi/scrapy-redis
[7]: http://blog.csdn.net/u012150179
[8]: http://blog.csdn.net/leoking01/article/details/41041455
[9]: http://www.jianshu.com/p/6663d5359736
[10]: http://city.shaform.com/blog/2016/02/28/scrapy.html
[11]: http://gold.xitu.io/entry/56a5f59ec4c9710053e74120?from=singlemessage&isappinstalled=0
[12]: http://www.lagou.com/jobs/list_%E7%88%AC%E8%99%AB?px=default&city=%E5%85%A8%E5%9B%BD#filterBox
