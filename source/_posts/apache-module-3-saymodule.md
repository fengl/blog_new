---
title: Apache模块开发（三）：聊聊模块化
date: 2016-12-08 20:20:41
tags: [Apache, httpd, module]
---

Apache模块开发的第三篇，我们来聊聊Apache模块开发中的模块化。

<!-- more -->

[Apache模块开发（一）：快速体验](http://alimap.org/2016/09/19/apache-module-1-quickstart/)

[Apache模块开发（二）：拥抱C++](http://alimap.org/2016/09/20/apache-module-2-hellocpp/)

[Apache模块开发（三）：聊聊模块化](http://alimap.org/2016/12/18/apache-module-3-saymodule/)


Apache具有很好的扩展性，而其扩展性的实现最重要的就归功于其模块化的设计。用户可以根据自己的需求，开发Apache模块，对Apache进行功能扩展。

Apache模块化的内容，可以写很多很多，在现阶段，我们先解决一些最直观的问题。
在这一篇里我们要了解一下最直接问题：Apache是怎么知道并加载我们开发的模块的。

更具体来说，一个典型的http请求，Apache是如何知道需要调用我们的模块来处理的呢？
这里，我们以[第一篇](http://alimap.org/2016/09/19/apache-module-1-quickstart/)的代码为例进行说明。

### 1. 配置文件 

如果本系列的[第一篇](http://alimap.org/2016/09/19/apache-module-1-quickstart/)中所述，在我们开发完自己的模块并生成so文件后，把so文件拷贝到Apache的安装目录下modules目录后，还不行。还需要在配置文件httpd.conf中增加如下的代码：

```
LoadModule hello_module modules/mod_hello.so
<Location /hello>
	SetHandler hello
</Location>
```

其中，第一行`LoadModule hello_module modules/mod_hello.so
`就是告诉Apache，我开发了一个模块，位置放在了modules目录下面，名字就叫mod\_hello.so。Apache在启动的时候，读到这行指令，就是通过其mod\_so模块来加载我们开发的这个so文件，以备后续使用。至于，如何通过mod\_so加载的，我们找时间再聊。


`Location`这三行配置文件，告诉Apache，如果有请求url类似`http://ip/hello?`这样的请求，统统交给Handler hello来处理。

这个Hander叫hello的处理程序，就是我们开发的模块，不信请求看我们代码中有这样的描述：

```
static int hello_handler(request_rec *r)
{
    if (strcmp(r->handler, "hello")) {
        return DECLINED;
    }
    r->content_type = "text/html";

    if (!r->header_only)
        ap_rputs("The sample page from mod_hello.c\n", r);
    return OK;
}
```


这其中的`if (strcmp(r->handler, "hello")) {`就是说，如果handler不是hello,我不管，请让我处理handler为hello的请求吧！

### 2. 模块结构

前面我们介绍了通过配置文件来连接用户的http请求以及我们的代码这一概略的处理流程。但是，Apache是怎么加载我们开发的模块（so文件）的呢？怎么就能够通过统一的方法，加载第三方编写的代码，以处理个性化的用户需求的呢？换句话讲，Apache怎么就认识我们写的代码是符合他的模块结构呢？怎么就知道哪个函数能处理用户请求呢？肯定不是任意的so文件都可以当做Apache模块加载的。


这里面靠的就是module结构的定义，我们在hello.c文件中定义如下变量：

```
module AP_MODULE_DECLARE_DATA hello_module = {
   STANDARD20_MODULE_STUFF,
   NULL,                  /* create per-dir    config structures */
   NULL,                  /* merge  per-dir    config structures */
   NULL,                  /* create per-server config structures */
   NULL,                  /* merge  per-server config structures */
   NULL,                  /* table of config file commands       */
   hello_register_hooks  /* register hooks                      */
};
```

这个变量就是我们开发的模块能够被识别、被调用的关键。

这里，我们定义的变量叫做hello_module，继续回到前面我们提到的配置文件中的这一行：

```
LoadModule hello_module modules/mod_hello.so
```

通过配置文件，我们告诉Apache，我们开发的模块中定义的module变量叫hello_module,这个变量你要到`modules/mod_hello.so`这个文件中去读取。
	
怎么样，是不是就串起来了？

### 3. 小结
稍稍的总结一下：

1. Apache的模块化是通过配置文件动态加载so的。
2. Apache与so的交互入口靠的是module结构体，我们开发的模块必须要定义一个类型为module的变量
3. 这个变量的名字，也是通过配置文件告诉Apache的

更详细的东东，找时间再聊聊~






