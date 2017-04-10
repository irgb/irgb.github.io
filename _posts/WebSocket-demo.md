---
title: Implement Chatroom using javax.websocket and jetty. 
date: 2017-04-10
categories: web
tags: [java, websocket]
---

jetty 即实现了 jsr-356 中定义的 websocket 规范（javax.websocket），也实现了一套自己的 websocket，包名分别是 org.eclipse.jetty.websocket.api 和 org.eclipse.jetty.websocket.jsr356。

为了代码的可移植性，这里采用 javax.websocket。

### Reference

- [Jetty WebSocket](https://www.eclipse.org/jetty/documentation/9.4.x/ws-intro-api.html)
- [websocket heartbeat](http://django-websocket-redis.readthedocs.io/en/latest/heartbeats.html)