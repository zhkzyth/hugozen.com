---
layout: post
title: "reading note of the totor library for python"
date: 2016-09-06 09:05:06 +0800
comments: true
tags: python reading note totor library
categories: tech
---

## 背景
在ziz的时候，cto的技术选型不合理，对使用技术缺乏必要的了解，采用了旧和大而笨重的中间件。因为`tcelery`的核心开发者不再维护代码，所以需要重新选择更加合适的技术方案。

## 阅读
原版的[tcelery][2]有几点问题：
```
- 对于redis的backend，存在race condition的情况，具体要参考下[git issue][1]
- 对于超时的处理，如果worker跑这个任务运行的运行时间超过规定时间，客户端的连接就会卡死
- 代码时间有点旧了，作者mher也没有继续维护和做codereview的工作
```

后来认识了一个提交pull request的童鞋，因为作者没有维护采纳修改的缘故，他做了一版新的工具，[totoro][3]。现在的项目就是基于这个来开发的。

ps: segmentfault有专门[一个问题][4]来讨论这个bug的，也可以去看看解决方案（其实是我自问自答啦）。

[1]: https://github.com/Strawhatfy/tornado-celery/commit/520cf105441d0296be73f5c6931abefec7d5c6e0
[2]: https://github.com/mher/tornado-celery
[3]: https://github.com/Strawhatfy/totoro
[4]: https://segmentfault.com/q/1010000003886676/a-1020000003910702
