# GTK+介绍
这是一篇面向初学者的GTK+编程指南。该指南的所有代码都是用c语言写的。所有程序都在Linux环境下创建和测试。本GTK+编程指南适合初级和中级程序员。本指南采用的是GTK+2的版本。

## GTK+
*GTK+*是一个用户图形界面库。该库是由c语言编写而成的。GTK+库也被称作GIMP工具集。GTK+库最开始是为了开发GIMP图像处理软件而被开发出来的。自那以后，GTK+变成了Linux和BSD Unix下最受欢迎的工具集之一。今天，开源世界里很多gui软件都是由Qt和GTK+开发的。GTK+是一套面向对象的编程接口，它的面向对象系统由Glib Object系统提供--这也是GTK+库的基础。*Gobject*还可以为多种语言创建语言绑定。目前支持的语言包括c++，Python，Perl，Jaca，C#和其他的一些编程语言。

GTK+本身依赖于以下这些库:

- Glib
- Pango
- ATK
- GDK
- GdkPixbuf
- Cairo

*Glib*是一个通用基础库。它提供了各种数据类型，字符串库，错误报告，线程和其他很有用的编程功能。*Pango*是用来国际化的库。ATK则是accessibility toolkit;它会提供一些工具集用来帮助残疾人使用电脑。*GDK*是对基于底层图形系统的低级绘画和窗口函数的封装。在Linux上GDK介于X服务和GTK+库之间。它操纵着基本渲染，如绘图基元，光栅图形，光标，字体，以及窗口事件和拖放功能。*GdkPixbuf*库是一个用来载入图像和操作像素缓冲区的库。*Cairo*是一个2D图形学库，从GTK+的2.8版本开始就被包含在内了。

Gonme和XFec桌面环境就是由GTK+库写的。SWT和wxWidges这些著名的程序框架也是用的GTK+。还有一些非常优秀的软件例如FireFox和Inkscape也都是用GTK+写的。

## 编译GTK+应用
为了编译GTK+应用，我们需要一个非常方便的工具**pkg-config**。**pkg-config**会返回已经安装的库的元数据。当我们要使用某个特定的库的时候，它就会为我们提供需要的依赖库和头文件。**pkg-config**程序会从指定的元数据文件中检索相应的包的信息。

```
$ gcc -o simple simple.c `pkg-config --libs --cflags gtk+-2.0`
```
这行命令会编译一个简单的程序。源代码只包含一个文件--simple.c。

```
$ pkg-config --cflags gtk+-2.0 | xargs -n3
-pthread -I/usr/include/gtk-2.0 -I/usr/lib/x86_64-linux-gnu/gtk-2.0/include
-I/usr/include/atk-1.0 -I/usr/include/cairo -I/usr/include/gdk-pixbuf-2.0
-I/usr/include/pango-1.0 -I/usr/include/gio-unix-2.0/ -I/usr/include/freetype2
-I/usr/include/glib-2.0 -I/usr/lib/x86_64-linux-gnu/glib-2.0/include -I/usr/include/pixman-1
-I/usr/include/libpng12 -I/usr/include/harfbuzz
```

**--cflags**参数会打印出预处理信息和编译GTK+应用程序要求的编译选项，包括它们的依赖的编译选项。

```
$ pkg-config --libs gtk+-2.0 | xargs -n5
-lgtk-x11-2.0 -lgdk-x11-2.0 -latk-1.0 -lgio-2.0 -lpangoft2-1.0
-lpangocairo-1.0 -lgdk_pixbuf-2.0 -lcairo -lpango-1.0 -lfontconfig
-lgobject-2.0 -lglib-2.0 -lfreetype
```

**--libs**会列出需要的库。

## 版本
下面这个程序会打印GTK+和Glib的版本。
***
>**version.c**
``` c
#include <gtk/gtk.h>

int main(int argc, char *argv[]) {

    gtk_init(&argc, &argv);

    g_printf("GTK+ version: %d.%d.%d\n", gtk_major_version, 
        gtk_minor_version, gtk_micro_version);
    g_printf("Glib version: %d.%d.%d\n", glib_major_version,
        glib_minor_version, glib_micro_version);    
        
    return 0;
}
```
***

该程序使用了内建常量

```shell
$ ./version 
GTK+ version: 2.24.23
Glib version: 2.40.2
```
这就是version程序的输出。
