---
layout: post
title: "good practice for storing time related objs in mysql with python"
date: 2016-08-14 12:45:18 +0800
comments: true
categories: scrapy twisted mysql
---
I recently wrote a spider for jobs with the [scrapy][1] framework, and stored all my items in mysql.

But here the problem comes. If you try to use timestamp type in mysql, several traps may be concerned.

First, how the timestamp being interpreted is affected by your connection timezone.If you do not specify, it may use the mysql server one.But who knows what is the default one.

Second, your mysql client in python layer may not support `time_zone` param, which is available in [mysql c api][2].Some libraries like `MySQL-python==1.2.5` just not support it.
So, if you want to change connection timezone, you may have to specify it explicitly on every sql query.

Third, your mysql database may not be controlled by you, so you would be better to change your logic code timezone.

As for me, i would just use `INT` for all my time related data, and represent them when necessary.

So, use it explicitly, or just don't use it.

Or, even better, see [discussions][3] on ST.In general,just set your db timezone to UTC, and use local timezone on your logical layers.

[1]: http://scrapy.org/
[2]: https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html
[3]: http://stackoverflow.com/questions/19023978/should-mysql-have-its-timezone-set-to-utc
