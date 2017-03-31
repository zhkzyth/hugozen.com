---
layout: post
title: "no silver bullet"
date: 2016-08-18 10:08:47 +0800
comments: true
tags:
- test
- software
- silver-bullet
categories: tech
---

### 背景
研究测试开发相关资料做的一些总结，备用~

### 文章
- [我的TDD实践：可测试性驱动开发（上）][1]
- [“单元测试要做多细？”][2]
- [Mocks Aren't Stubs][3]
- [Richard Feynman, 挑战者号, 软件工程][4]

### 从rails身上学测试
- [系列文章][5]
- 单元测试针对model，按理不应该包含数据库操作，只是测试model的业务逻辑，验证规则之类的。
- 功能测试，rails的是针对单个controller，测试action用的，这个肯定需要配合数据库。
- 集成测试用来测试多个controller的协同工作，主要测试工作流程，业务流程。一个流程会涉及多个controller，肯定需要数据库的配合。
  - 集成测试，可以定期做，也可以经常性的做。
  - 其实测试的主要作用的验证系统，验证代码，是否满足需求。
  - 如果代码有变更，系统有变更，或者业务有变更，肯定需要跑测试，涉及到的测试都需要跑。
  - 如果什么都没有变化，就不太需要跑了吧。
- [官方文档][6]

<!-- more -->

### python模拟工具
- [pyMox][7]
     - 感受
          - 语法初看起来难懂，懂了之后觉得好亲切
          - 文档齐全，使用简单，居家旅行必备
          - 不过看完stub和mock的介绍，感觉这个框架没分清stub和mock的区别
- fudge
  - [文章][8]介绍了下，感觉还可以，不过还没用过

- 更多
  - [stack上面的推荐][9]
  - [多个mock库的对比][10]

### python测试框架
- 原生的unittest还不错
- nosetest在测试理念上更加工程化
- [入门文档][11]

### 书籍
- .NET单元测试的艺术
  - 每个单元测试最好只有一个mock对象，多个桩对象

### 敏捷软件开发
- [use github grow self][12]


[1]: http://blog.zhaojie.me/2009/10/testability-driven-development-1.html
[2]: http://coolshell.cn/articles/8209.html
[3]: http://martinfowler.com/articles/mocksArentStubs.html
[4]: http://coolshell.cn/articles/1654.html
[5]: http://virusswb.blog.51cto.com/blog/115214/1075385
[6]: http://guides.rubyonrails.org/testing.html
[7]: https://code.google.com/p/pymox/wiki/MoxDocumentation
[8]: http://foofish.net/blog/72/unittest
[9]: http://stackoverflow.com/questions/98053/what-is-your-favorite-python-mocking-library
[10]: http://garybernhardt.github.io/python-mock-comparison/
[11]: https://nose.readthedocs.org/en/latest/index.html
[12]: http://www.phodal.com/blog/use-github-grow-self/
