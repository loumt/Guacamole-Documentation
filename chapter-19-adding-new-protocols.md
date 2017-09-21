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

这个过程与从git构建guacamole -服务器的过程几乎相同，同文档章节[ “Building guacamole-server”](http://guacamole.incubator.apache.org/doc/0.9.13-incubating/gug/installing-guacamole.html#building-guacamole-server)



> 重点

libguac库是guacamole服务器的一部分，这是该项目的一个必需依赖项。 你必须先安装 _libguac, guacd, etc. _通过章节_ _[_building and installing guacamole-server_](http://guacamole.incubator.apache.org/doc/0.9.13-incubating/gug/installing-guacamole.html#building-guacamole-server)_._ 如果guacamole-server没有被安装,或者libguac因此不存在, 这`configure` 脚本会发生一个异常表明that it could not find libguac:

```
$
./configure
checking for a BSD-compatible install... /usr/bin/install -c
checking whether build environment is sane... yes
...
checking for guac_client_stream_png in -lguac... no
configure: error: "libguac is required for communication via "
                   "the Guacamole protocol"
$
```

你需要去安装guacamole-server 然后重新运行 `configure`.

---

规格最小的客户端:

为了实现最基本的客户端插件，需要做的工作也很少。我们从`src/ball.c开始,包含客户端插件所需的绝对最小值:`\#include 

```
<
guacamole/client.h
>


#include 
<
stdlib.h
>


/* Client plugin arguments (empty) */
const char* TUTORIAL_ARGS[] = { NULL };

int guac_client_init(guac_client* client) {

    /* This example does not implement any arguments */
    client-
>
args = TUTORIAL_ARGS;

    return 0;

}
```

注意这个文件的结构。只有一个函数`guac_client_init,这个是所有Guacamole客户端的入口点,`就好像一个典型的C项目在启动时都会去执行main函数,当一个新的连接出现或者一个已存在的协议被调用时，guacd会加载这个guacamole插件，这个guacamole插件就会去执行`guac_client_init这个函数。`

`guac_client_init`这个函数接受一个初始化了的`guac_client。`这个初始化过程的一部分涉及到声明连接用户可以指定的参数列表.

虽然我们不会在本教程中使用参数，因此上面指定的参数只是一个空列表，典型的客户端插件实现将注册定义远程桌面连接及其行为的参数。这种参数的例子可以在Guacamole out-of-the-box的协议的连接参数中看到\(参看： Configuring Connections\)

给guac\_client\_init的guac\_client实例将由启动连接的用户共享，以及通过屏幕共享连接到连接的任何用户。它一直存在直到连接显示关闭，或者直到所有用户离开连接为止。

这个项目是与GNU Automake一起构建的,我们配置一个`configure.ac 来描述项目的名字与项目所需要的一些参数。这这种情况下，这项目是` "libguac-client-ball"，它依赖于guacd和所有客户端插件使用的“libguac”库:

```
# Project information
AC_PREREQ([2.61])
AC_INIT([libguac-client-ball], [0.1.0])
AM_INIT_AUTOMAKE([-Wall -Werror foreign subdir-objects])
AM_SILENT_RULES([yes])

AC_CONFIG_MACRO_DIRS([m4])

# Check for required build tools
AC_PROG_CC
AC_PROG_CC_C99
AC_PROG_LIBTOOL

# Check for libguac
AC_CHECK_LIB([guac], [guac_client_stream_png],,
      AC_MSG_ERROR("libguac is required for communication via "
                   "the Guacamole protocol"))

AC_CONFIG_FILES([Makefile])
AC_OUTPUT
```

我们同时也需要一个`Makefile.am`来描述哪些文件需要被构建和怎样何时去构建 libguac-client-ball

```
AUTOMAKE_OPTIONS = foreign
ACLOCAL_AMFLAGS = -I m4
AM_CFLAGS = -Werror -Wall -pedantic

lib_LTLIBRARIES = libguac-client-ball.la

# All source files of libguac-client-ball
libguac_client_ball_la_SOURCES = src/ball.c

# libtool versioning information
libguac_client_ball_la_LDFLAGS = -version-info 0:0:0
```

在本教程的其余部分，GNU Automake文件将基本保持不变。

一旦创建了以上所有文件，就会有一个功能正常的客户端插件,它还没有做任何事情，而且任何连接都是非常短暂的\(因为服务器发送的任何数据都将导致客户机断开连接，假设连接已经停止响应\)，但它在技术上确实有效。

