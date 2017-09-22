###### Original Link : [http://guacamole.incubator.apache.org/doc/0.9.13-incubating/gug/guacamole-architecture.html](http://guacamole.incubator.apache.org/doc/0.9.13-incubating/gug/guacamole-architecture.html)

---

Guacamole并不是一个自包含的web应用,他是由许多部分组成.web应用实际上是为了简单而简单的，大部分繁重的工作都是由低级别的组件完成的.

![](/assets/import.png)

用户们通过他们的浏览器连接到guacamole服务器。这个guacamole客户端，是JavaScript编写,在guacamole服务器中通过一个webSever向用户提供服务.一旦加载,这个客户端通过Guacamole协议回连至服务端.

web应用部署到guacamole服务端读取guacamole协议，并转发到本地的guacamole代理--guacd.这个代理实际上解释了guacamole协议的内容，并代表用户连接到任意数量的远程桌面服务器

Guacamole协议与guacd一起提供了协议不可知论:Guacamole客户端和web应用程序都不需要知道远程桌面协议的实际使用情况。

> Guacamole协议

web应用一直不懂任何远程桌面协议。它不包含对VNC或RDP或其他由Guacamole堆栈支持的协议的支持。它实际上只理解了Guacamole协议，它是用于远程显示呈现和事件传输的协议。虽然具有这些属性的协议自然具有与远程桌面协议相同的功能，但是远程桌面协议和Guacamole协议背后的设计原则是不同的:Guacamole协议并不打算实现特定桌面环境的特性。

作为一个远程显示和交互协议，Guacamole实现了一组现有的远程桌面协议，添加对特定的远程桌面协议\(如RDP\)的支持，从而涉及到在远程桌面协议和Guacamole协议之间编写一个“转换”的中间层。实现这样的翻译与实现任何原生客户机没有什么不同，只是这个特定的实现呈现给远程显示而不是本地显示。

处理此转换的中间层是guacd。

> Guacd

Guacd是支持动态加载远程桌面协议\(称为'客户端组件'\)与从web应用受到命令并将其连接到远程桌面的Guacamole的核心。

Guacd是一个守护进程，随着Guacamole一并安装，并运行在后台。监听web应用上TCP的连接，Guacd也不懂任何远程桌面协议。但是，更确切地说，仅仅实现了Guacamole协议，以确定哪些协议支持需要加载，哪些参数必须传递给它。一旦一个客户端插件被加载，他就会独立运行，并在客户端插件终止之前，始终对他自己与web应用之间的通信拥有完全的控制权。

gucad与客户端插件都依赖一个公共的库，libguac,他使得通过Guacamole协议使通信更加简单和抽象。

> web应用

用户实际与之交互的Guacamole部分是web应用程序。

这个Web应用，正如前面提到的，不会实现任何远程桌面协议。他依靠于guacd,只实现一个简单的web接口和身份验证层。

我们选择使用java去实现web应用的服务端，但是这并没有原因代表他不能靠其他语言去实现\(也就是可以用其他服务器端的语言去实现，如node\)。实际上，Guacamole目的是一个API，我们支持这个.

> RealMint

Guacamole现在是一个通用的远程桌面网关，但情况并非总是如此。Guacamole最初是一个纯文本的Telnet客户机，用JavaScript编写，叫做RealMint\( "RealMint" 是 "terminal"的一个相同字母异序词\)，它主要是作为一个演示而编写的，虽然它的目的是有用的，但它的主要原因是它是纯粹的JavaScript。

RealMint使用的通道是用PHP编写的。相比Guacamole的HTTP通道,RealMint使用的通道是一个简单的长轮询，效率低下。RealMint有一个不错的键盘实现，现在被用在了Guacamole的键盘实现中，这就是RealMint的特点和可用性的程度。考虑到它只是一个遗留协议的实现，而且还存在其他几个JavaScript终端模拟器，其中大多数都是稳定的，因此项目被删除了。

> VNC客户端

一旦开发人员了解了HTML5 canvas标记，并看到它已经在Firefox和Chrome中实现，那么工作就开始在一个概念验证的JavaScript VNC客户机上。

