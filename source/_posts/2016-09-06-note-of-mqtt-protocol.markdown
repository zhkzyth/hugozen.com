---
layout: post
title: "note of mqtt protocol"
date: 2016-09-06 22:17:43 +0800
comments: true
tags: mqtt reading-note
categories: tech
---

## blog
```
- [SSL/TLS配置（证书生成需要注意CN不能乱填）][1]
- [mqtt协议详解][2]
```

##  JPush参考发现的一些点：
```
- 推送信息的保存时间长短（10天？）
- 单设备多用户
    - server端记录设备的id，多用户通过别名其它机制来做逻辑的映射
    - 但极光的做法是单个设备和别名一对一，不同用户登录，别名会被覆盖
    - 提供有限时长过的记录保存
- [极光推送的很多策略值得参考，API设计的也不错][3]
```

<!--more-->

rabbitmqtt插件形式：
```
         ------------------------
         |  mqtt_sup        |  (supervisor)
          ------------------------
            /                    \
           /                      \
          /                        \
         /                          \
-------------------------------------\
|  rabbitmq_mqtt_collector|\
 ------------------------------------- \
       (worker)                      \----------------------------------
                                          | rabbit_mqtt_client_sup|
                                          -----------------------------------
                                                  (supervisor)
                                                       |
                                                       |
                                                       |
                                                  -------------------------------------------
                                                  | rabbit_mqtt_connection_sup|
                                                   ------------------------------------------
                                                       (supervisor)
                                             /    /  \                                        \
                                            /    /    \                                        \
                                           /    /      \                                        \
                                                       -----------------------------          -------------------------------
                                                       | rabbit_mqtt_reader|         | keepalive_supervisor|
                                                        ----------------------------          --------------------------------
```

## 协议原理图
![protocol img](http://ww2.sinaimg.cn/large/65e4f1e6gw1f7k8axb3g9j217m0v3798.jpg)


[1]: http://www.embedded101.com/Blogs/PaoloPatierno/tabid/106/entryid/366/MQTT-over-SSL-TLS-with-the-M2Mqtt-library-and-the-Mosquitto-broker.aspx
[2]: http://www.blogjava.net/yongboy/archive/2014/02/10/409689.html
[3]: http://docs.jpush.cn/pages/viewpage.action?pageId=3309643
