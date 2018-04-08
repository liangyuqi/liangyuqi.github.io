---
layout:     post
title:      基于websocket／node的在线聊天
subtitle:   
date:       2018-1-20
author:     Liangyuqi
header-img: img/post-bg-10.jpg
catalog: true
tags:
    - websocket
---

## 什么是websocket

Websocket是一个持久化的协议，相对于HTTP这种非持久的协议来比，
HTTP的生命周期通过 Request 来界定，也就是一个 Request 一个 Response ，那么在 HTTP1.0 中，这次HTTP请求就结束了。

在HTTP1.1中进行了改进，使得有一个keep-alive，也就是说，在一个HTTP连接中，可以发送多个Request，接收多个Response。但是请记住 Request = Response ， 在HTTP中永远是这样，也就是说一个request只能有一个response。而且这个response也是被动的，不能主动发起。

Websocket是基于HTTP协议的，或者说借用了HTTP的协议来完成一部分握手。

