Original Link:[http://guacamole.incubator.apache.org/doc/0.9.13-incubating/gug/custom-protocols.html](http://guacamole.incubator.apache.org/doc/0.9.13-incubating/gug/custom-protocols.html)

---

Guacamole对多个远程桌面协议的支持是通过guacd动态加载的插件提供的。 Guacamole API已经设计了这样的协议支持很容易创建，特别是当一个C库提供了一个基本的客户端实现时。

在这个教程中,我们将实现一个简单的客户端,他通过使用Guacamole协议呈现一个弹跳的球,在完成这个教程并安装结果后,你可以在Guacamole的配置中添加一个使用ball协议的连接,任何使用这个连接的人都能看到一个弹跳的球.

这个实例客户端插件在实际上并不能作为一个客户端,但是这并不重要.Guacamole客户端只是一个远程显示,这个客户端插件只是作为一个简单的应用去连接这个显示,就好像Guacamole客户端自身的VNC与RDP插件去连接这个远程显示.

本教程的每一步都是去实践一个新概念,同时也朝着一个弹跳的球的目标前进,在教程每一步之后,你就会有一个可构建的和可工作的客户端插件.

---

本教程将使用GNU Automake构建系统，它是用于libguac、guacd等的Guacamole使用的构建系统。将有四个文件涉及:

* `configure.ac`

由GNU Automake使用，生成配置脚本，最终用于生成生成的Makefile，该文件将在构建时使用。

* `Makefile.am`

使用GNU Automake和configure脚本生成生成的Makefile，该文件将在构建时使用。

* `src/ball.c`

定义弹跳球“客户端”的代码主体。

* `src/ball.h`



118/5000



一个头文件，定义了代表弹跳球状态的结构\(一旦有必要这样做\)。



