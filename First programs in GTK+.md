# 第一个GTK+程序
在这个部分的GTK+编程指南中，我们会创建我们的第一个GTK+程序。我们会使一个窗口在屏幕中间，显示图标和标题栏，显示一个小小的提示框，并为按钮组件创建一个mnemonic。

## 简单的例子
我们的第一个例子显示了一个简单的窗口
>simple.c
```c
#include <gtk/gtk.h>

int main(int argc, char *argv[]) {

  GtkWidget *window;

  gtk_init(&argc, &argv);

  window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
  gtk_widget_show(window);
  
  g_signal_connect(window, "destroy",
      G_CALLBACK(gtk_main_quit), NULL);  

  gtk_main();

  return 0;
}
```
这个例子会在屏幕上显示一个简单的窗口。
```c
GtkWidget *window;
```
所有的GTK+的widget来源于**GtkWidget** 这个基础类。它管理着widget的生命周期，状态和样式。
```c
gtk_init(&argc, &argv);
```
**gtk_init()**函数会初始化GTK+库和解析一些标准命令行选项。这个函数应当在使用任何其他的GTK函数之前被调用。
```c
window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
```
**gtk_window_new()**函数据创建一个新的**GtkWindow**，这个窗口是一个能包含其他组件(widget)的顶级窗口。这个窗口的类型是**GTK_WINDOW_TOPLEVEL**。顶层窗口拥有一个标题栏和边框，这些都是由窗口管理器管理的。
```c
gtk_widget_show(window);
```
gtk_widget_show()标志着要显示的组件。任何没有被show的组件(widget)都不会出现在屏幕上。
```c
g_signal_connect(window, "destroy",
    G_CALLBACK(gtk_main_quit), NULL); 
```
**g_signal_connect()**函数会为一个特定的对象将一个回调函数和信号连接起来。窗口默认不会对**destory**信号作出反应。为了实现终止应用程序的功能我们必须显式的将**destory**和内置的可以结束应用程序的**gtk_mian_quit()**函数
```c
gtk_main()
```
这句代码会进入GTK+的主循环。从这个点开始，应用程序会保持运行并等待事件发生。
```
$ gcc -o simple simple.c `pkg-config --libs --cflags gtk+-2.0`
```
这条语句展示了怎么来编译这个例子。
![](http://zetcode.com/img/gui/cgtk/simple.png) 
*图片:Simple*


## 使窗口居中
如果我们没有自己放置窗口(位置)，窗口管理器会为我们放置它。在下一个例子中，我们会使窗口居中。
>center.c
```c
#include <gtk/gtk.h>

int main(int argc, char *argv[]) {
    
  GtkWidget *window;

  gtk_init(&argc, &argv);

  window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
  gtk_window_set_title(GTK_WINDOW(window), "Center");
  gtk_window_set_default_size(GTK_WINDOW(window), 230, 150);
  gtk_window_set_position(GTK_WINDOW(window), GTK_WIN_POS_CENTER);
  gtk_widget_show(window);

  g_signal_connect(G_OBJECT(window), "destroy",
      G_CALLBACK(gtk_main_quit), NULL);

  gtk_main();

  return 0;
}
```
在这个例子中，我们使窗口居中，设置了一个标题，并指定了窗口的尺寸。
```c
gtk_window_set_title(GTK_WINDOW(window), "Center");
```
**gtk_window_set_title()**函数会为窗口设置一个标题。如果我们自己没有设置标题，GTK+会用源文件的名字来作为标题。
`gtk_window_set_default_size(GTK_WINDOW(window), 230, 150);
`
这个**gtk_window_set_default_size()**函数将窗口大小设置为230×150。注意这里我们说的是客户端区域，不包括窗口管理器提供的哪些附加物。
```c
gtk_window_set_position(GTK_WINDOW(window), GTK_WIN_POS_CENTER);
```
将**GTK_WIN_POS_CENTER**常量作为参数传入**gtk_window_set_position()**中可以使窗口在屏幕上居中。

## 应用程序图标
在下一个例子中，我们会展示应用程序的图标。大多数窗口管理器会在标题栏的左上角和任务栏上显示图标。
>inco.c
```c
#include <gtk/gtk.h>

GdkPixbuf *create_pixbuf(const gchar * filename) {
    
   GdkPixbuf *pixbuf;
   GError *error = NULL;
   pixbuf = gdk_pixbuf_new_from_file(filename, &error);
   
   if (!pixbuf) {
       
      fprintf(stderr, "%s\n", error->message);
      g_error_free(error);
   }

   return pixbuf;
}

int main(int argc, char *argv[]) {
    
  GtkWidget *window;
  GdkPixbuf *icon;

  gtk_init(&argc, &argv);

  window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
  gtk_window_set_title(GTK_WINDOW(window), "Icon");
  gtk_window_set_default_size(GTK_WINDOW(window), 230, 150);
  gtk_window_set_position(GTK_WINDOW(window), GTK_WIN_POS_CENTER);
  
  icon = create_pixbuf("web.png");  
  gtk_window_set_icon(GTK_WINDOW(window), icon);
  
  gtk_widget_show(window);

  g_signal_connect(G_OBJECT(window), "destroy",
      G_CALLBACK(gtk_main_quit), NULL);
      
  g_object_unref(icon);    

  gtk_main();

  return 0;
}
```
这个示例代码会显示一个应用程序的图标。
```c
pixbuf = gdk_pixbuf_new_from_file(filename, &error);
```
**gdk_pixbuf_new_from_file()**函数会创建一个有图标的应用程序
```c
pixbuf = gdk_pixbuf_new_from_file(filename, &error);
```
**gdk_pixbuf_new_from_file()**函数会通过从文件中载入图像来创建一个新的pixbuf。函数会自动检测文件的格式。如果函数返回NULL，则会自动设置一个error。
```c
if (!pixbuf) {
    
    fprintf(stderr, "%s\n", error->message);
    g_error_free(error);
}
```
如果图标加载失败则会输出一条error信息。
```c
icon = create_pixbuf("web.png");  
gtk_window_set_icon(GTK_WINDOW(window), icon);
```
**gtk_window_set_icon()**函数会在窗口上显示图标。而**create_pixbuf()**则会从PNG文件中创建一个**GdkPixbuf**。
```c
g_object_unref(icon);
```
**g_object_unref()**函数会减少pixbuf对象的引用计数。当它的引用计数为0时，这个对象的任务就结束了(也就是说，它的空间会被释放)
![](http://zetcode.com/img/gui/cgtk/icon.png) 
*图片: Icon*

## Tooltip
消息提示框是一个很小的矩形窗口，用来给出一个对象的简要信息。它通常是一个GUI组件;通常是一个应用的帮助系统的组成部分。
```c
#include <gtk/gtk.h>

int main(int argc, char *argv[]) {

  GtkWidget *window;
  GtkWidget *button;
  GtkWidget *halign;

  gtk_init(&argc, &argv);

  window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
  gtk_window_set_title(GTK_WINDOW(window), "Tooltip");
  gtk_window_set_default_size(GTK_WINDOW(window), 300, 200);
  gtk_container_set_border_width(GTK_CONTAINER(window), 15);
  
  button = gtk_button_new_with_label("Button");
  gtk_widget_set_tooltip_text(button, "Button widget");
  
  halign = gtk_alignment_new(0, 0, 0, 0);
  gtk_container_add(GTK_CONTAINER(halign), button);
  gtk_container_add(GTK_CONTAINER(window), halign);  

  gtk_widget_show_all(window);
  
  g_signal_connect(G_OBJECT(window), "destroy",
      G_CALLBACK(gtk_main_quit), NULL);  

  gtk_main();

  return 0;
}
```
这个例子简单展示了在一个按钮组件上显示消息提示框的例子。
```c
gtk_container_set_border_width(GTK_CONTAINER(window), 15);
```
**gtk_container_set_border_width()**函数设置了到窗口边缘的边界空白。
```c
gtk_widget_set_tooltip_text(button, "Button widget");
```
**gtk_widget_set_tooltip_text()**函数会为给出的组件设置基本的消息提示框。
```c
halign = gtk_alignment_new(0, 0, 0, 0);
gtk_container_add(GTK_CONTAINER(halign), button);
```
**GtkAlignment**是一个基础容器可以用来让它的子组件与窗口边线对齐。在我们的这个例子里，按钮被放置到了窗口的左上角。函数的第一对参数是xalign和yalign。 xalign为0代表左对齐，yalign为0代码顶端对齐。第三个和第四个参数则是缩放比例值。如果两个参数的值都为0则代表组件在两个方向上都没有扩展。
```c
gtk_container_add(GTK_CONTAINER(window), halign);
```
这里**GtkAlignment**被设置为窗口的主容器。
```c
gtk_widget_show_all(window);
```
当我们处理多个组件时，相比每次单个的去显示所有组件，使用**gtk_widget_show_all()**函数来显示容器上的所有组件显然更简单。在我们的这个例子里，窗口和按钮被同时显示出来。
![](http://zetcode.com/img/gui/cgtk/tooltip.png) 
*图片:Tooltip*

## Mnemonic
快捷键的内容，不想翻译了，先空着吧...
