---
layout: post
title: "things i learn from scrapy"
date: 2016-08-14 16:37:36 +0800
comments: true
categories: scrapy notes python
---
Early this month, i used [scrapy][2] to write spiders.And I won't say that i use almost 2-3 hours to read the docs again, so i can get familiar with it for how it working.

But i just had written same code one year ago.

It's just amazing how people often forget what they have done.And i think to write down the architecture of it would be a great thing for me, or for a better me. =)

So, how a spider work?

Let's take a look at [scrapy][2] one.

![scrapy architecture][1]

There are 4 parts for [Scrapy][2] and a core engine in it.

In general, when you crawl a website, a index url is needed.

First, start the core `Scrapy Engin`, which is built on [`Twisted`][2] framework.It connect the 4 components, and transfer messages in specific directions.

Second, we send the index url to `Downloader`, which download the page.

Then, the `Spiders` come in, grape all the things you want, like link, images, content and so on.If the spider got new links, it just sends them to the `Scheduler` part, preparing for the next new crawling task.

By sending the new links to `Scheduler`, the `Spiders` part also send stuffs like content to `Item Pipeline`.In the `Item Pipeline` part, raw content is being refactored and stored in db like mysql.

So, what [scrapy][2] teaches me here?

<!-- more -->

One, try to split different parts to do only one things.

Second, use middleware to extend the flexibility of your program.

Third, a architecture photo means a lot than words.

[1]: http://doc.scrapy.org/en/latest/_images/scrapy_architecture.png
[2]: http://doc.scrapy.org/
