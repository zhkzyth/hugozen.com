---
layout: post
title: "[GM]豆瓣图书插件"
date: 2011-02-18 21:55:26 +08:00
college: college douban code plugin
---
*Update: 本插件已经失效了，短期内不会再更新了......亲，不要用这个来学习前端或者有关后台的知识*

背景
----
在看了华科和[Light][]几位师兄做的一个插件，基于[GM][]和[FF][]，
在豆瓣看书的同时，可以显示自己学校图书馆有没库存的信息。觉得这样的插件蛮实用的，
而且实现上也不会太难，就自己尝试做了一个。下面的内容就来讲解下相关的思路，以及我在做这个插件的过程中一些收获，特别是正则表达式和编码转换的理解，算是对自己这几天的一个小小总结。

<p style="text-align: center;"><img class="aligncenter" src="http://farm6.static.flickr.com/5052/5457095388_7203ea04e0.jpg" alt="preview of douban_GDUT" width="500" height="183" /></p>
<p style="text-align: center;"><span style="color: #c0c0c0;">&lt;插件效果示意图&gt;<!--more--></span></p>

其实这个基于GM的脚本基本的思路可以用下面这张图来描述，下面将针对每个环节，具体讲下的一些实现原理和参考的资料。

<p style="text-align: center;"><a title="豆瓣插件流程图 by zhkzythorz, on Flickr" href="http://www.flickr.com/photos/53523179@N06/5457095332/"><img class="aligncenter" src="http://farm6.static.flickr.com/5095/5457095332_34da3f152a.jpg" alt="豆瓣插件流程图" width="461" height="376" /></a></p>

第一步：豆瓣====>华科
---------------------
在浏览器客户端这边，利用GM扩展程序本身提供的JQ库抽取豆瓣页面上的书籍信息，这里我们抽取两类，一个是书籍的isbn信息，另外一个就是标题信息。大家可能会很好奇为什么会有华科，这是因为学校图书馆不支持缺省’-’字符的isbn字符串查找，简单的说就是非常不智能，所以绕道华科的图书馆，借用他们的图书馆资源进行查找。

*步骤*

1. 豆瓣页面：页面DOM树下载完毕后，利用GM本身提供的jq库，获取书籍标题和isbn信息
2. 通过GM本身的GM_xmlhttpRequest异步调用，向PHP后台程序发出请求
3. 服务器获取信息后，调用fsockopen函数，发起第一次查询，读入华科图书馆的内容页信息
4. 利用强大的正则匹配，找到格式化的isbn，截取之，返回数据
5. 完成第一步

PS：这里面截取图书馆的信息的方法有非常多了，还可以利用[simplehtmldom][]这样的php类库，简化抽取的工作量和效率；或者利用php本身的扩展CURL进行读入工作，不过我都没试，以后有机会再尝试好了。

第二步：华科====>广工
---------------------
拿到格式化后的isbn，服务器再利用fsockopen发起第二次访问，不过这次的对象是广工的图书馆，因为单纯的使用正则匹配，所以遇到新的html页面，又要重新匹配过，难点是正则的表达式书写，在这个过程中，终于认识到了php数组和字符串函数的强大之处。

因为拿到isbn查询的时候，只能取到模糊搜索结果的页面，无法抽取一些具体的书籍信息。但这个时候匹配的结果只有一个，利用正则把书籍的&lt;a&gt;链接地址抽取出来，工作就完成了。

*步骤*

1.  服务器利用fsockopen发起第二次访问，获取模糊查找页面的内容
2.  正则匹配结果条目的&lt;a&gt;标签，抽取跳转地址
3.  完成


第三步：广工====>广工
----------------------
终于拿到具体书目的详细地址了，大家在看前面的功能截图时候应该会留意到，我们要抽取的信息大概有“馆藏地址”、“索引号”、“借阅状态”3种，正则抽取方面难度蛮大的，可能用之前介绍的类库来提取会更容易点。

*步骤*

1. 服务器利用fsockopen发起第三次访问，获取具体书籍页面的内容
2. 正则匹配相应条目
3. 调用Php的数组和字符串函数，生成相应的内容块
4. 写成json格式的字符串，返回给客户端
5. 到这里，工作基本完成了

第四步：广工====>豆瓣
------------------------
回到豆瓣后，其实只是完成一个异步调用的过程，但这个时候通过返回的json格式字符串，js利用eval函数可以生成对应的对象，然后通过遍历对象的属性，生成html字符串，动态插入到页面中去，并且套用豆瓣页面本身的css格式。

*步骤*

1. 客户端在接受到返回信息后，调用js脚本
2. 利用eval()函数生成对象，遍历产生对应的html内容
3. 插入到页面中
4. 整个工作到此完成

第五步：关于之后的反思
-----------------------
1.  做的过程中遇到的困难
    -  一开始对curl、fopen、fsockopen等方法不了解，
        摸索了半天，最终还是一位做论坛的前辈指点，用fsockopen函数发起服务端的访问，解决了内容提取的难题。
    -  正则表达式写得不够强壮，在测试某些非常旧的书的时候，出现内容获取错误，或测试时间过长，需要不断验证自己的表达式，耗费了相当多的时间。
    -  不懂应用一些成熟的类库，增加了自己的工作量。
    -  最后生成的跳转链接有问题，没意识到utf8和gb2312格式对URIencode转换的效果是不一样的，对自己的问题的界定不够清晰，导致很多时候看的资料不是要解决的问题需要的……囧，说得好绕口……
    -  对[json][]格式不熟悉，反复考量了半天才想出构造字符串的方法生成对象……
    -  严重缺乏想象力和创造力……
2.  一些值得关注的事情
    -  在做这个的过程中，看了满多的东西，特别是关于书籍管理在web2.
       0时代的一些变革，关于这些有趣的思考可以关注豆瓣读书、libraything。你是否意识到到了书籍在网上不再是按照某个主题被聚合在一起了呢？
       他们有更多组合的可能，你其实有机会去实现某些有趣的组合。
    -  其实有很多人在挖掘图书馆资源方面已经做了很多的努力，比如说这个[插件小组][plugin_group]，还有06年就开始有的一个叫做[libraryLookup][]的项目，可以输入你的图书馆信息，然后生成bookmarketlet，挂在浏览器的标签栏上，你就可以通过弹窗查看馆藏信息了。
    -  GM很强大，关于js的很多想象都可以借助它来完成，比如……
3.  最后的最后
    -  谢谢芬姐第一个试用，并给出最大的反馈
    -  谢谢聪的js建议，改动了前辈的一些代码
    -  其实这个php的后台程序可能没有太多的挑战，但我去做的原因可能是因为觉得这样的程序和脚本居然可以借助GM这样的扩展，做到获取不同地方书籍信息同步，节省多余的查找操作......我觉得自己是被这样的想法震撼了，所以才决定去做。

相关资源链接
-------------
- 开始你自己的GM脚本开发：[Get Started With Greasemonkey][get_started_with_greasemonkey]
- 如果你喜欢豆瓣，又懂技术的话，又喜欢读书社区的话，[豆瓣插件小组][plugin_group]可能会比较合适
- 在线正则匹配：基于js的[regexpa][]。不过注意PHP是perl和posix风格的，两者有区别的
- Douban_GDUT脚本和后台PHP代码地址：[douban_GDUT][]

安装方法
--------
1.  安装[firefox][]
2.  安装[GreaseMonkey][greasemonkey]扩展程序
3.  安装基于GM的[js脚本][gmscript]
4.  好了，打开你喜欢的豆瓣，看看有哪些书可以借阅的:-D

*PS:版本现在还不稳定，大家帮忙测试下，或者写个更好的后台，把效率提高起来，现在基本访问的速度都要3-4秒或更久，有明显的延迟=.=*

[Light]:http://lightory.net
[GM]:http://www.kenengba.com/post/754.html
[FF]:http://en.wikipedia.org/wiki/Mozilla_Firefox
[simplehtmldom]:http://simplehtmldom.sourceforge.net "simpleHtmlDom"
[json]:http://www.gosoa.com.cn/Json%E6%A0%BC%E5%BC%8F%E5%92%8C%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B+%E4%BB%8B%E7%BB%8D
[plugin_group]:http://www.douban.com/group/topic/7520655/
[douban_plugin_group]:http://www.douban.com/group/plugin
[libraryLookup]:http://www.douban.com/group/topic/7520655
[get_started_with_greasemonkey]:http://www.webmonkey.com/2010/02/get_started_with_greasemonkey/
[firefox]:http://firefox.com.cn
[greasemonkey]:https://addons.mozilla.org/zh-CN/firefox/addon/greasemonkey/?id=748
[gmscript]:http://userscripts.org/scripts/show/97212
[douban_GDUT]:https://github.com/zhkzyth/douban_GDUT
[regexpa]:http://regexpal.com/
