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

一个头文件，定义了代表弹跳球状态的结构\(一旦有必要这样做\)。



所有的源文件都将在src子目录下，与C项目一样，在根目录下构建文件。主要的src /球。c和与构建相关的配置。交流和Makefile。am文件将首先被创建，每个连续的步骤在这些文件上迭代，使用src / ball。当需要的时候加上h。在每个步骤之后，您可以通过运行make来构建/重建插件，然后通过运行make install和ldconfig来安装它\(这样guacd可以找到插件\):

```
$
make
  CC       src/ball.lo
  CCLD     libguac-client-ball.la
#
make install
make[1]: Entering directory '/home/user/libguac-client-ball'
 /usr/bin/mkdir -p '/usr/local/lib'
 /bin/sh ./libtool   --mode=install /usr/bin/install -c   libguac-client-ball.la '/usr/local/lib'
...
----------------------------------------------------------------------
Libraries have been installed in:
   /usr/local/lib

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the '-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the 'LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the 'LD_RUN_PATH' environment variable
     during linking
   - use the '-Wl,-rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to '/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------
make[1]: Nothing to be done for 'install-data-am'.
make[1]: Leaving directory '/home/user/libguac-client-ball'
#
ldconfig
```

在第一次调用之前，您需要运行configure脚本，它首先需要使用autoreconf生成:

```
$
autoreconf -fi
libtoolize: putting auxiliary files in '.'.
libtoolize: copying file './ltmain.sh'
libtoolize: putting macros in AC_CONFIG_MACRO_DIRS, 'm4'.
libtoolize: copying file 'm4/libtool.m4'
libtoolize: copying file 'm4/ltoptions.m4'
libtoolize: copying file 'm4/ltsugar.m4'
libtoolize: copying file 'm4/ltversion.m4'
libtoolize: copying file 'm4/lt~obsolete.m4'
configure.ac:10: installing './compile'
configure.ac:4: installing './missing'
Makefile.am: installing './depcomp'
$
./configure
checking for a BSD-compatible install... /usr/bin/install -c
checking whether build environment is sane... yes
...
configure: creating ./config.status
config.status: creating Makefile
config.status: executing depfiles commands
config.status: executing libtool commands
$
```



