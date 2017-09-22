###### Original Link : [http://guacamole.incubator.apache.org/doc/0.9.13-incubating/gug/guacamole-architecture.html](http://guacamole.incubator.apache.org/doc/0.9.13-incubating/gug/guacamole-architecture.html)

---

Guacamole并不是一个自包含的web应用,他是由许多部分组成.web应用实际上是为了简单而简单的，大部分繁重的工作都是由低级别的组件完成的.

![](/assets/import.png)

用户们通过他们的浏览器连接到guacamole服务器。这个guacamole客户端，是JavaScript编写,在guacamole服务器中通过一个webSever向用户提供服务.一旦加载,这个客户端通过Guacamole协议回连至服务端.

web应用部署到guacamole服务端读取guacamole协议，并转发到本地的guacamole代理--guacd.这个代理实际上解释了guacamole协议的内容，并代表用户连接到任意数量的远程桌面服务器

Guacamole协议与guacd一起提供了协议不可知论:Guacamole客户端和web应用程序都不需要知道远程桌面协议的实际使用情况。

> Guacamole协议

web应用一直不懂任何远程桌面协议。它不包含对VNC或RDP或其他由Guacamole堆栈支持的协议的支持。它实际上只理解了Guacamole协议，它是用于远程显示呈现和事件传输的协议。



