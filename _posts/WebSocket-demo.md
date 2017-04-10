---
title: Implement Chatroom using javax.websocket and jetty. 
date: 2017-04-10
categories: web
tags: [java, websocket]
---

jetty 即实现了 jsr-356 中定义的 websocket 规范（javax.websocket），也实现了一套自己的 websocket，包名分别是 org.eclipse.jetty.websocket.api 和 org.eclipse.jetty.websocket.jsr356。

为了代码的可移植性，这里采用 javax.websocket。

定义一个 websocket 处理类非常简单，只需要在POJO上添加一些注解即可，servlet 容器会根据这些注解：
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
        try {
            String username = session.getQueryString();
            if (users.containsKey(username) && users.get(username) == session) {
                users.remove(username);
                for (String user : users.keySet()) {
                    users.get(user).getBasicRemote().sendText(username + " leaved this room.");
                }
            }
            if (session.isOpen()) {
                session.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
### Reference

- [Jetty WebSocket](https://www.eclipse.org/jetty/documentation/9.4.x/ws-intro-api.html)
- [websocket heartbeat](http://django-websocket-redis.readthedocs.io/en/latest/heartbeats.html)