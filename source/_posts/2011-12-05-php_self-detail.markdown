---
layout: post
title: "[translate]php_self vs path_info vs script_name vs request_uri"
date: 2011-12-05 18:39:39 +08:00
tags: php code tips
categories: tech
---

背景
----
php的几个$_SERVER变量都有自己的应用场景的，但manual那里写的太抽象了，最后在stackoverflow那里看到，通过浅显的例子，生动的比较了PHP_SELF、PATH_INFO、SCRIPT_NAME和REQUEST_URI的不同之处，简单翻译在下面，算是mark下来。

正文
----
一些展示这些变量不同之处的实际例子：

1.PHP_SELF跟SCRIPT_NAME只有在请求的url是如下格式的时候才会不同：
<!-- -->
    http://example.com/test.php/foo/bar
    [PHP_SELF] =/test.php/foo/bar
    [SCRIPT_NAME] =/test.php/

(看来PATH_INFO只有在这种在请求文件后再追加路径的情形下才发挥了自己作用，而这个时候$_SERVER['PATH_INFO']才有值)注意：在低于5.0版本的php中，这会有些许不同。

<!--more-->
2.当请求的链接中带有查询语句的时候，REQUEST_URI和SCRIPT_NAME会不一样：
<!-- -->
    http://example.com/test.php/foo/bar
    http://example.com/test.php?foo=bar
    [SCRIPT_NAME] => /test.php
    [REQUEST_URI] => /test.php?foo=bar</pre>
3.当服务器重写规则起作用的时候(比如我们开启了APACHE的mod_rewrite模块后)，REQUEST_URI和SCRIPT_NAME是不一样的:
<!-- -->
    http://example.com/test.php/foo/bar
    http://example.com/test.php
    [REQUEST_URI] => /test.php
    [SCRIPT_NAME] => /test2.php

4.当用脚本处理HTTP错误的时候，REQUEST_URI和SCRIPT_NAME会不一样。使用Apache命令 ErrorDocument 404 /404errror.php
<!-- -->
    http://example.com/test.php/foo/bar
    http://example.com/test.php
    [REQUEST_URI] => /test.php
    [SCRIPT_NAME] => /404error.php

在IIS服务器下，使用自定义的错误页
    http://example.com/test.php
    [SCRIPT_NAME] => /404error.php
    [REQUEST_URI] => /404error.php?404;http://example.com/test.php

原文链接:[传送门][door]
[door]:http://stackoverflow.com/questions/279966/php-self-vs-path-info-vs-script-name-vs-request-uri/326331#326331
