---
layout: post
title: "Mysql Wiki Set Names Utf8"
date: 2011-12-11 18:40:41 +08:00
tags:
- mysql
- php
- code
- tips
categories: tech
---

背景
----
最近在看opencart的代码，看到mysql类的时候，不太理解`query="set names utf8"`具体的意义是什么，那时也没太注意。后来自己写了个简单的数据类从mysql抽取数据出来的时候，显示在页面的都是???号，也就是大家常说的乱码了。

想起之前的set names操作，于是就在类的__construct方法里面，加入了`mysql_query(“SET NAMES 'utf8'”,$this->connection);`，然后一切都好了。

why?
----
那为什么set names 'utf8'会出现这样的效果？在我从数据库读取和写入数据的时候，中间的编码转换过程到底是什么样的?......这些问题涌上心头，还是动手写写测试，查查手册吧～

####我的系统参数
- Ubuntu11.04
- Mysql 5.1.54(mysql>SELECT VERSION()查看)
- PHP 5.3.5（php -n -v 查看）

####set names utf8是什么意思
在CSDN上面有一篇[很经典的文章][csdn]，讲到这个问题，原来`mysql_query("set names 'utf8'")`会导致mysql数据库的几个默认编码值发生变化，分别是character_set_client、character_set_connection，还有character_set_results。

那么，为什么这3个值到底是什么，为什么会影响字符的编码呢？下面基于各位前辈的资料，综合自己的理解，来讲下mysql的字符集问题。
<!--more-->

常规字符集和校对
--------------------
1.MySQL5.1的特性
    - 使用多种字符集(character_set)来存储字符串（比较常见的有utf8、gbk2312...... ）
	- 使用多种校对规则(charset_collation)来比较字符串
    - 在同一台服务器、同一个数据库 或甚至在同一个表中使用不同字符集或校对规则来混合字符串
    - 允许定义任何级别的字符集和校对规则

2.Mysql中的字符集和校对规则字符集变量
在命令行登录mysql后，查看到这些变量的运行值：
    zhkzyth@ubuntu:~$ mysql -u root -p
    Enter password:
    //一些欢迎和版本信息
    mysql>show variables like “character_set%”

<img class="size-full wp-image-453 aligncenter" title="1" src="/images/mysql/2.png" alt="" width="466" height="216" />

可以看到，系统内部字符集变量有很多，这里，先简单说下各个变量的涵义，后面会展开讲述各个变量的具体涵义和作用。
    character_set_client ：这是设置客户端发送查询使用的字符集
    character_set_connection ：这是设置服务器需要将收到的查询串转换成的字符集
    character_set_database：这是设置数据库使用的字符集
    character_set_filesystem：这是查询本地文件时候，文件名需要转换的字符集
    character_set_results ：这是设置服务器要将结果数据转换到的字符集，转换后才发送给客户端
    character_set_server：这是设置服务器使用的字符集
    character_set_system：这是设置系统使用的字符集
    character_set_dir ：这个是mysql查找字符集的位置
各变量详细介绍：[传送门][variables]

而通过下面的命令，则可以查看到系统的collation字符集变量都有哪些。
    zhkzyth@ubuntu:~$ mysql -u root -p
    Enter password:
    //一些欢迎和版本信息
    mysql>show variables like “collation_%”

<img class="size-medium wp-image-454 aligncenter" title="2" src="/images/mysql/3.png" alt="" width="300" height="109" />

由图中我们可以看到，系统默认的校对规则字符集有三个，我们后面会来看看这些变量的意义。

Mysql的默认字符集和校对
----------------------
在Mysql中，字符集和校对规则有4个级别的默认设置：服务器级、数据库级、表级和连接级，下面详细讲述每个级别的涵义。

####服务级
在MySQL按照如下方法确定服务器字符集和服务器校对规则：
当服务器启动时根据有效的选项设置
在服务器级别，确定方法很简单。当启动mysqld时，根据使用的初始选项设置来确定服务器字符集和校对规则。 在unix下，比如我的ubuntu的系统，就可以在”/etc/mysql/ my.cnf”或”~/.my.conf”下进行字符集的设置。
在[client]下，加入`default-character-set=utf8`，保存重启Mysql服务就可以进行修改了。

<img class="size-full wp-image-456 aligncenter" title="3" src="/images/mysql/4.png" alt="" width="631" height="247" />

重启后，结果如下:
    mysql> show variables like "character_set%"
    -> ;
    +--------------------------+----------------------------+
    | Variable_name | Value |
    +--------------------------+----------------------------+
    | character_set_client | utf8 |
    | character_set_connection | utf8 |
    | character_set_database | latin1 |
    | character_set_filesystem | binary |
    | character_set_results | utf8 |
    | character_set_server | latin1 |
    | character_set_system | utf8 |
    | character_sets_dir | /usr/share/mysql/charsets/ |
    +--------------------------+----------------------------+
    8 rows in set (0.10 sec)

*这里有几点要注意*

1.使`--default-character-set X`设置字符集，而没有指定`—default-collation`的话，collation会使用`--default-character-set X`对应的校对规则字符集。比如我设了X是utf8，那么collation就会自动设置成utf8对应的校对集utf8_general_ci。
    mysql> show variables like "collation%";
    +----------------------+-------------------+
    | Variable_name | Value |
    +----------------------+-------------------+
    | collation_connection | utf8_general_ci |
    | collation_database | latin1_swedish_ci |
    | collation_server | latin1_swedish_ci |
    +----------------------+-------------------+
    3 rows in set (0.00 secs)
2.你也可以在字符集后面为校对规则添加default-collation，指定你想要的校对规则字符集。
如果没有指定一个字符集，那就与`--default-character-set=latin1`相同。
    当前的服务器字符集和校对规则可以用作character_set_server和collation_server系统变量的值。在运行时能够改变这些变量的值。
    SET NAMES 'charset_name'
    SET CHARACTER SET charset_name

    SET NAMES 'x' 等效于
    SET character_set_client = x;
    SET character_set_results = x;
    SET character_set_connection = x;

    SET CHARACTER SET 'x' 等效于
    SET character_set_client = x;
    SET character_set_results = x;
    SET collation_connection = @@collation_database; --数据库的collation
    (ps:上述命令现在已经不建议使用，请看到后面的描述。）

####数据库级
1.数据库级的字符集和校对
每一个数据库有一个数据库字符集和一个数据库校对规则，它不能够为空。`CREATE DATABASE`和`ALTER DATABASE`语句有一个可选的子句来指定数据库字符集和校对规则：
    CREATE DATABASE db_name
    [[DEFAULT] CHARACTER SET charset_name]
    [[DEFAULT] COLLATE collation_name]
    ALTER DATABASE db_name
    [[DEFAULT] CHARACTER SET charset_name]
    [[DEFAULT] COLLATE collation_name]

例如：
    CREATE DATABASE db_name
    DEFAULT CHARACTER SET latin1 COLLATE latin1_swedish_ci;
MySQL这样选择数据库字符集和数据库校对规则：

- 如果指定了CHARACTER SET X和COLLATE Y，那么采用字符集X和校对规则Y。
- 如果指定了CHARACTER SET X而没有指定COLLATE Y，那么采用CHARACTER SET X和CHARACTER SET X的默认校对规则。
- 否则，采用服务器字符集和服务器校对规则。

如果在CREATE TABLE语句中没有指定表字符集和校对规则，则使用数据库字符集和校对规则作为默认值。

2.表字符集和校对

每一个表有一个表字符集和一个校对规则，它不能为空。为指定表字符集和校对规则，CREATE TABLE 和ALTER TABLE语句有一个可选的子句：

    CREATE TABLE tbl_name (column_list)
    [DEFAULT CHARACTER SET charset_name [COLLATE collation_name]]

    ALTER TABLE tbl_name
    [DEFAULT CHARACTER SET charset_name] [COLLATE collation_name]

例如：
    CREATE TABLE t1 ( ... )
    DEFAULT CHARACTER SET latin1 COLLATE latin1_danish_ci;

MySQL按照下面的方式选择表字符集和 校对规则：

- 如果指定了CHARACTER SET X和COLLATE Y，那么采用CHARACTER SET X和COLLATE Y。
- 如果指定了CHARACTER SET X而没有指定COLLATE Y，那么采用CHARACTER SET X和CHARACTER SET X的默认校对规则。
- 否则，使用数据库字符集和校对规则作为默认值。

如果在列定义中没有指定列字符集和校对规则，则默认使用表字符集和校对规则。表字符集和校对规则是MySQL的扩展;在标准SQL中没有。

3.列字符集和校对
    每一个“字符”列（即，CHAR、VARCHAR或TEXT类型的列）有一个列字符集和一个列校对规则，它不能为空。列定义语法有一个可选子句来指定列字符集和校对规则：
    col_name {CHAR | VARCHAR | TEXT} (col_length)
    [CHARACTER SET charset_name [COLLATE collation_name]]

例如：
    CREATE TABLE Table1
    (
    column1 VARCHAR(5) CHARACTER SET latin1 COLLATE latin1_german1_ci
    );

MySQL按照下面的方式选择列字符集和校对规则：

- 如果指定了CHARACTER SET X和COLLATE Y，那么采用CHARACTER SET X和COLLATE Y。
- 如果指定了CHARACTER SET X而没有指定COLLATE Y，那么采用CHARACTER SET X和CHARACTER SET X的默认校对规则。
- 否则，采用表字符集和服务器校对规则。

CHARACTER SET和COLLATE子句是标准的SQL。

举例子
------

*示例1：表和列定义*
    CREATE TABLE t1
    (
    c1 CHAR(10) CHARACTER SET latin1 COLLATE latin1_german1_ci
    ) DEFAULT CHARACTER SET latin2 COLLATE latin2_bin;
 在这里我们有一个列使用latin1字符集和latin1_german1_ci校对规则。是显式的定义，因此简单明了。需要注意的是，在一个latin2表中存储一个latin1列不会存在问题。

*示例2：表和列定义*
    CREATE TABLE t1
    (
    c1 CHAR(10) CHARACTER SET latin1
    ) DEFAULT CHARACTER SET latin1 COLLATE latin1_danish_ci;
 这次我们有一个列使用latin1字符集和一个默认校对规则。尽管它显得自然，默认校对规则却不是表级。相反，因为latin1的默认校对规则总是 latin1_swedish_ci，列c1有一个校对规则latin1_swedish_ci（而不是latin1_danish_ci）。

*示例3：表和列定义*
    CREATE TABLE t1
    (
    c1 CHAR(10)
    ) DEFAULT CHARACTER SET latin1 COLLATE latin1_danish_ci;
我们有一个列使用一个默认字符集和一个默认校对规则。在这种情况下，MySQL查找表级别来确定列字符集和 校对规则。因此，列c1的字符集是latin1，它的 校对规则是latin1_danish_ci。

*示例4：数据库、表和列定义*
在mysql下：
    CREATE DATABASE d1
    DEFAULT CHARACTER SET latin2 COLLATE latin2_czech_ci;
    USE d1;
    CREATE TABLE t1
    (
    c1 CHAR(10)
    );
我们创建了一个没有指定字符集和校对规则的列。我们也没有指定表级字符集和校对规则。在这种情况下，MySQL查找数据库级的相关设置。（数据库的设置变为表的设置，其后变为列的设置。）因此，列c1的字符集为是latin2，它的 校对规则是latin2_czech_ci。


连接级的连接字符集和校对
------------------------
一些字符集和校对规则系统变量与客户端和服务器的交互有关。
考虑什么是一个“连接”：它是连接服务器时所作的事情。客户端发送SQL语句，例如查询，通过连接发送到服务 器。服务器通过连接发送响应给客户端，例如结果集。我所理解的交互过程，不一定对，大致如下图：

<img class="size-large wp-image-455 aligncenter" title="mysql" src="/images/mysql/1.png" alt="" width="574" height="352" />

对于客户端连接，这样会导致一些关于连接的字符集和 校对规则的问题，这些问题均能够通过系统变量来解决：

1.  在图中圆圈1的位置，表示当查询离开客户端后，服务器使用character_set_client变量作为客户端发送的查询中使用的字符集。

2. 图中2的位置表示，服务器接收到查询后，使用character_set_connection和collation_connection系统变量， 将客户端发送的查询从character_set_client系统变量转换到character_set_connection 。

3.  图中3、4、5的位置表示，当服务器收到数据请求后，Mysql从数据库中提取数据，这个时候，就会根据数据库，以及表、列本身的编码集进行编码转换。比如，character_set_client为utf8，而table1表的字符集为latin1，列没有设置默认字符集，那么数据库就会把存入的数据做utf8转latin1的操作。

4. 图中6的位置表示，当服务器发送结果集或返回错误信息到客户端之前，使用 character_set_results变量指示服务器返回查询结果到客户端使用的字符集。包括结果数据， 例如列值和结果元数据（如列名）。
沿用上面的例子，假如character_set_results为utf8，则从table1抽取出来的数据，会做编码转换，从latin1转换成utf8。

5. 图中6箭头指向connection位置，表示character_set_results字符集转character_set_connection的操作。后续过程就跟刚才的请求过程是一致的。

设置技巧
--------
编码的问题是很恼人的一件事情，但只要弄懂，并多折腾实践下还是蛮好理解的。而有些技巧我们可以在平时的开发过程中使用到的：

1. 设置Mysql配置文件
    这个在讲配置的时候有讲到具体的操作方法，可以回看上面的文字。

2. 在创建数据库的时候使用utf8，其他的表和列自然就可以默认继承下来了。 也可以尝试把整个database的字符集改成utf8，但在做这个操作前要记得备份，并把其他非utf8的表格做编码转换工作。比较危险，不建议马上尝试=.=.....不过，可以参考下这篇教程(http://www.bluebox.net/news/2009/07/mysql_encoding)

3. 如果你是用php的，可以在数据链接成功后，接着写个查询语句：
<!---->
    //假设$this指向你数据类的实例
    $this->link=mysql_connect($host,$usr,$password)；
    mysql_query(“SET NAMES UTF8”,$this->link);

但笔者不建议你这样做，原因如下：

这个方法已经被PHP官方声明放弃使用，而改用mysql_set_charset ( string $charset [, resource $link_identifier ] ) 。大概原因是因为mysql_set_charset除了起到改变那三个变量的值外，还修改了其他一些关键的部分，关于关键部分的具体的信息大家可以看看<a title="topic.csdn.net" href="http://topic.csdn.net/u/20090201/00/2e1db365-6e68-4dbd-956f-d2ce81183a8a.html?seed=686853995" target="_blank">这篇文章</a>。
    //假设$this指向你数据类的实例
    $this->link=mysql_connect($host,$usr,$password)；
    mysql_set_charset(“utf8”,$this->link);

回看本文一开提出的问题
----------------------
那么，当我们在Mysql运行时，在mysql命令下输入 以下命令的时候，为什么就能解决乱码问题呢？

    //假设$this指向你数据类的实例
    $this->link=mysql_connect($host,$usr,$password)；
    mysql_set_charset(“utf8”,$this->link);

原来，在我的数据库中，表的默认字符集编码是utf8，而connection是latin1的，results是latin1，client是utf8。
从数据库中拿出utf8编码的数据之后，转成latin1编码的results；再经过connection后，由于是相同的编码latin1,所以不用转换；最后到达client的时候，再转成utf8编码格式。

大致过程：utf8->latin1->latin1->utf8，最后浏览器自动判断编码。而由于latin1对中文支持存在问题（具体原因待查），导致了中文字符出现乱码。所以，解决方案就是，把connection、results的编码改成utf8，最好4个地方都统一起来。

总结
----
总的来说，编码问题无非就两个地方，一个是你怎么存储数据，一个是你怎么传输数据，在上面的示意图中，只要把各个环节的编码集统一起来，基本就不会有问题的了。

在这个过程中，一定要多查手册，google各种你能找到的资源，然后动手实践去判断一些事情，最好能再深入挖掘下，最后写篇文章总结下，mark下进度。

TODO
----
1. 了解PHP的源代码，底层的C实现，知道PHP底层是怎么调用mysql的接口实现一系列的交互操作的。
    - 相关资料：
        - TIPI项目 www.php-internal.com/book/
        - 《PHP扩展开发及内核应用》目录 http://phpbook.sinaapp.com/preface.html
2. 编译Mysql的源码，也是底层的C实现，从实现的角度来探讨`character_set%`等导致的乱码问题，以及可能的解决方案。
    - 相关资料：
        - OurMySQL  http://ourmysql.com
3. 熟悉Mysql的手册，特别是在命令行下命令，以及相关的优化技术，常见问题如应对字符编码乱码，安全问题等。
    - 相关资料：
        - MySQL 5.1参考手册  http://dev.mysql.com/doc/refman/5.1/zh/preface.html
4. 继续完善这篇文章，或单独开一个系列、栏目，如校对的概念、latin1编码与utf8的区别、如何把非utf8转换成utf8格式、怎么转移大批量的数据最划算等等话题.......

5. 把页面的样式再调整下，太难看了.....

参考资料
--------
1. <a title="http://blog.csdn.net/antswallow/article/details/4987216 " href="http://blog.csdn.net/antswallow/article/details/4987216%20" target="_blank">MYSQL编码问题的汇总</a>

2. <a title="charset-syntax" href="http://dev.mysql.com/doc/refman/5.0/en/charset-syntax.html" target="_blank">Specifying Character Sets and Collation</a>

3. <span style="font-family: DejaVu Serif Condensed,serif;"><a href="http://dev.mysql.com/doc/refman/5.0/en/server-system-variables.html">server-system-variables</a></span>

4. <a title="字符集编码底层实现" href="http://topic.csdn.net/u/20090201/00/2e1db365-6e68-4dbd-956f-d2ce81183a8a.html?seed=686853995" target="_blank">4.CSDN关于字符集编码底层实现的讨论</a>



[csdn]:http://topic.csdn.net/u/20090201/00/2e1db365-6e68-4dbd-956f-d2ce81183a8a.html?seed=686853995
[variables]:http://dev.mysql.com/doc/refman/5.0/en/server-system-variables.html
