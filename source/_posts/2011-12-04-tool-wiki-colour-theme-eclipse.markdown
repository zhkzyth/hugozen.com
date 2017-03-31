---
layout: post
post: "[Tool wiki]怎么样在Eclipse下安装颜色主题"
date: 2011-12-04 13:42:46 +08:00
tags:
- tools
- tips
- code
categories: tech
---

背景
----
不知道大家在平时的开发过程中使用的是什么类型的工具的呢？而我就比较喜欢用Eclipse了，因为它开源，而且支持非常多的开发语言，外加非常优秀的插件，插剥方便，整个开发工具看起来是非常轻量的。

起因
----
程序员的眼睛对屏幕多会容易疲劳，而白屏我觉得就比较伤眼了，所以寻思在eclipse下有没办法可以把开发环境的背景由默认的白色，调整成类似vim那样的黑底，然后彩色的代码块呢，google了下，发现[Felix H. Dahlke][cool_guys]和 [Roger Dudler][cool_guys2]两位仁兄已经做了一个主题网站[Eclipse Color Themes][eclipsecolorthemes]，并一直在维护着。

安装方法
--------
1.先安装一个Eclipse Color Theme plugin，在Eclipse 3.5 (Galileo)下，选择Help-&gt;Install New Software,在弹出的窗口中，在work with输入框中填入http://eclipse-color-theme.github.com/update ，勾选出现的选框，一直next下去就ok了。而在Eclipse 3.6 (Helios)下，可以通过Help-\>Eclipse Marketplace，搜索Eclipse Color Theme进行安装。
<!-- more -->
<p style="text-align: center;"><img  src="/images/color-theme/1.png" alt="" width="300" height="173" /></a></p>

2.安装完后，可以在window-&gt;Preference下面，找到Window→Preferences→General→Appereance→Color Theme ，在里面就可以选择你喜欢的主题了。当然了，这个时候，你还需要上eclipsecolorthemes的网站去下载你喜欢的主题，你也可以上传自己创作的主题，这个就不详述了。

<p style="text-align: center;"><img  src="/images/color-theme/2.png" alt="" width="300" height="253" /></a></p>

3.还有其他的一些安装办法，第二种是导入\*.epf文件，具体方法可以看下这个[视频][video]。

安装后可能出现的问题
--------------------
其实这个不算是安装 Color Theme plugin的问题，但这个是在ubuntu下面很常见的一个问题，就是鼠标悬停在某个函数的上面的时候，出现的文档提示框背景是全黑的，在Ecipse下，比如我查看php的某个功能函数时，鼠标放到上面后弹出框背景是黑色的。

网上流传的思路是说这是因为系统主题默认背景是黑色的原因导致的，摘抄如下：

>可以通过在eclipse里直接可以设置，修改该颜色的配置选项位于：菜单栏 Window-&gt;preferences-&gt;C/C++-&gt;Editor 项目中的Appearance color options里面的Source hover background选项，取消勾选System Default，选择喜欢的颜色即可。

而我是在ubuntu11.04下的版本，这样修改后还是无果，因为是gnome的主题，可以直接在系统菜单位置，找到Appearance/Customize.../Colors下面进行修改，调整非黑色就好了。具体可以参考这篇文章[how-to-fix-black-tooltips-in-eclipse][fix-black]。

更进一步的讨论
--------------
1. 在stackOverflow上面有关于这个颜色主题的一些讨论，可能你会感兴趣，[传送门][stackoverflow]。

[video]:http://www.eclipsecolorthemes.org/?view=how-to-use
[stackoverflow]:http://stackoverflow.com/questions/96981/color-themes-for-eclipse/4815649#4815649
[fix-black]:askubuntu.com/questions/45001/how-to-fix-black-tooltips-in-eclipse
[cool_guys]:http://ubercode.de
[cool_guys2]:http://www.rogerdudler.com
[eclipsecolorthemes]:http://www.eclipsecolorthemes.org
