---
title: Implement Chatroom using javax.websocket and jetty. 
date: 2017-04-10
categories: web
tags: [java, websocket]
---

jetty 即实现了 jsr-356 中定义的 websocket 规范（javax.websocket），也实现了一套自己的 websocket，包名分别是 org.eclipse.jetty.websocket.api 和 org.eclipse.jetty.websocket.jsr356。

为了代码的可移植性，这里采用 javax.websocket。

### 实现 websocket 处理类
定义一个 websocket 处理类非常简单，只需要在 POJO 上添加一些注解即可：

```java
import javax.websocket.*;
import javax.websocket.server.ServerEndpoint;
import java.io.IOException;

@ServerEndpoint("/chat")
public class ChatWebSocket {
    @OnOpen
    public void onOpen(Session session) throws IOException {
        System.out.println("WebSocket opened: " + session.getId());
    	 // do something ...
    }

    @OnMessage
    public void onTextMessage(String message, Session session) {
        System.out.println("receive text message : " + message);
    	 // do something ...
    }

    @OnError
    public void onError(Session session, Throwable throwable) {
        System.out.println("Error happened.");
	     // do something ...
    }

    @OnClose
    public void onClose(CloseReason reason, Session session) {
        System.out.println("Websocket clsoed： " + reason.getReasonPhrase());
        //do something ...
    }
}
```

用 @ServerEndpoint 注解的 POJO 就是一个 websocket 处理类。servlet 容器启动时会自动加载这些类。@ServerEndpoint 可以包含一个路径参数，这个路径下的所有 websocket 连接都会交给这个类来处理。@OnOpen，@OnMessage，@OnError，@OnClose 都是 websocket 不同事件发生时的回调函数。从字面意思很容易判断其含义。

### Chatroom source code

```shell
git clone git@github.com:irgb/websocket-chatroom-demo.git
git checkout v1.0
mvn jetty:run
```

在浏览器中打开 [http://localhost:8080](http://localhost:8080) 即可打开聊天界面，打开多个窗口，即可实现聊天：
![](https://i.imgsafe.org/b9e25318b2.png)


### Reference

- [An Introduction to WebSockets](http://blog.teamtreehouse.com/an-introduction-to-websockets)
- [websocket brief tutorial](https://www.tutorialspoint.com/html5/html5_websocket.htm)
- [Jetty WebSocket](https://www.eclipse.org/jetty/documentation/9.4.x/ws-intro-api.html)
- [websocket heartbeat](http://django-websocket-redis.readthedocs.io/en/latest/heartbeats.html)