---
layout: post
title: "notes of zhifubao pay"
date: 2016-09-06 18:11:56 +0800
comments: true
tags: zhifubao pay notes
categories: tech
---

## blog参考资料
- [参考一][1]
- [获取支付token的代码][2]
- [如何生成rsa格式的公钥、私钥][3]
- [异步通知的文档][4]
- [支付宝回调验签 + 如何处理支付宝提供的公钥][5]
- [即时到账接口开发流程][6]

## 接口参考
- [查询交易记录的接口][7]，但不知道能不能这么用？<!--more-->

## 代码参考
- [淘宝自己的sdk][8]
- [退款接口参考][9]

## 出问题可以参考的资料
- [真的出问题啦=。=][10]

## 开发流程
- 生成rsa公钥、私钥
- 上传我们的公钥到阿里云服务器（[帮助][11]）
- 下载阿里给的对应支付宝公钥，保存起来

[1]: http://chloerei.com/2013/08/01/alipay-payment-in-ruby/
[2]: http://www.cnblogs.com/xiaojingjing/p/3791616.html?utm_source=tuicool
[3]: http://m.blog.csdn.net/blog/cheyiliu/42487113
[4]: https://cshall.alipay.com/support/help_detail.htm?help_id=476935
[5]: http://www.cnblogs.com/luojianqun/p/3560176.html
[6]: https://cshall.alipay.com/support/help_detail.htm?help_id=476935
[7]: http://open.taobao.com/apidoc/api.htm?path=cid:10238-apiId:11100
[8]: https://github.com/mytharcher/alipay-php-sdk
[9]: http://download.alipay.com/public/api/base/alipaydirect.zip
[10]: http://blog.csdn.net/happyrabbit456/article/details/42102921
[11]: https://cshall.alipay.com/enterprise/help_detail.htm?help_id=473890&keyword=%C8%E7%BA%CE%C9%FA%B3%C9RSA%C3%DC%D4%BF&sToken=&from=search
