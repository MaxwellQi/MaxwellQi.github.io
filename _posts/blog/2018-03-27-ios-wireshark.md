---
layout: post
title: 利用wireShark抓取iphone手机上的网络通信包
description: 利用WireShark过滤手机上的网络通信包，分析其上面的
category: blog
tag: ios
---
## 说明

以前利用工具抓取网络请求时都是使用`Charles`，然后越来越觉得`Charles`有些卡，并且只能过滤`HTTP`协议。但是`Charles`过滤的网络请求数据显示的非常清晰。 后来就换着使用`WireShark`来抓取手机上的网络请求。

`WireShark`功能非常强大，它能过滤几乎所有的协议，并且也很少出现卡顿的情况，体验起来确实比`Charles`要好。在介绍使用`WireShark`之前，我们首先要了解一些常见的协议：HTTP,TCP,UDP等。


## HTTP协议



## 参考
* [HTTP协议](https://hit-alibaba.github.io/interview/basic/network/HTTP.html)
* [iOS中的Socket编程](https://juejin.im/post/5958e81ef265da6c2d2c5f7f)
