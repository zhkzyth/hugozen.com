---
layout: post
title: "jq-sourceCode"
date: 2012-11-20 23:30
comments: true
categories: js jq code reading notes
---
我想没什么比阅读john Resig的JQ库更让人觉得亲切的了。

从一开始接触js，到做一个小小的demo，最后做一些面向单页app的应用，jq库这个熟悉而又陌生的脚手架一直陪伴着我们。

而我总是会有一种阅读的冲动，每次遇到不懂的东西，我想起的第一个念头，不是怎么用，而是，它怎么实现的。于是，你就开始折腾各种框架了，不是么？

那种拨开眼前迷雾的感觉，那种看得真切的感觉，是前所未有的快乐，也许，那就叫做思考的乐趣吧。
<!--more-->

##为什么阅读
当然了，这个世界上，js库多的是，jq并不是最优秀的那个。我阅读的原因是：
- API文档详细清晰
- JQ写得很优雅（可以参考资料那一节的书籍推）
- JQ库使用简单，社区人多，万一读不懂可以请教前辈
- JQ库特有的对浏览器兼容，可以了解到很多浏览器的兼容问题
- 大部分的库都类似的，可以精读一个，以通其他
- 项目中大量运用到这个库，是项目的基础组件（这个倒是很实际的XD）

##怎么阅读
- 阅读源码，写注释
- 看官网的API
- 写demo,大量的demo!
- 写源码分析
- 看别人的分析
    - [nuysoft jQuery1.6.1源码分析系列][nuysoft]
- 交流，Talk
    - team内部的ppt分享

##还可以怎么做
- 不读JQ库，从实践中体悟
    大一的时候想着要通读一遍jq源码，最后没坚持下来，原因是，原来我连用的都没用过这个库=.=。
    能在具体的项目中去大量使用这个库，并尝试去理解它的设计思想也可以。世界上还有其他很好的库的，
    而且实现起来，也不会有jq那样的晦涩难懂...
- 读一些小型的库
    163邮箱的harry大四的时候也尝试看jq，不过他更推荐看一些小型的库帮助自己去理解一些js的运用。
    确实，小型库小巧，但能让你看懂，并运用在自己的项目中。
    - [jRaiser][]
    - [pj][]
    - [jsFrame][]
- 看设计模式
    - [js Patterns][]
    - [Javascript Patterns][]
    - [Tom大叔讲设计模式][tomPatterns]
- 做其他有趣的应用
    是的,你是热爱编程才做这一行的,对么XD

##参考资料
- Helper tools
    - [jQuery Source viewer][jsv]
    - [jQuery Deconstructed][]


[jRaiser]: https://code.google.com/p/jraiser/
[pj]: http://users4.jabry.com/pengju/
[jsFrame]: http://bbs.51js.com/thread-88331-1-1.html
[jsv]: http://james.padolsey.com/jquery/
[js Patterns]: http://shichuan.github.com/javascript-patterns/
[Javascript Patterns]:http://book.douban.com/subject/5252901/
[tomPatterns]: http://www.cnblogs.com/TomXu/archive/2012/07/26/2581268.html
[jQuery Deconstructed]: http://www.keyframesandcode.com/resources/javascript/deconstructed/jquery/
[nuysoft]: http://www.cnblogs.com/nuysoft/archive/2011/11/14/2248023.html
