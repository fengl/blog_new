---
title: apache模块开发（一）：快速体验
date: 2016-09-19 10:54:24
tags: [apache, httpd, module]
---

# 开发环境

操作系统：centos 6

apache版本：2.2.31

# 源码安装apache httpd

- 下载源码
- 解压缩
- 进入目录
- ./configure
- make
- sudo make install
默认会安装到/usr/local/apache2下面

# 测试安装结果

- sudo /usr/local/apache2/bin/apachectl start
- curl "http://localhost"
- 如果出现`<html><body><h1>It works!</h1></body></html>`说明安装成功


# 开发自己的模块

apache允许用户开发自己的模块，以处理特殊的用户逻辑，细节我们后面再聊。

我们现在要做的就是开发一个模块，当通过http请求的时候，返回简单的“Hello”就可以了。

## 生成工程文件

apache安装后，在安装目录的bin目录下有个文件叫apxs，用于创建、编译、安装apache的自定义模块。

首先，用它来创建一个工程：
1. 进入你的home目录：`cd` 
2. 创建一个代码目录：`mkdir code`
3. 进入code目录：`cd code`
4. 生成工程：`/usr/local/apache2/bin/apxs -g -n hello`
5. 进入hello目录，发现有三个文件，分别是：
	- Makefile
	- mod\_hello.c
	- modules.mk

## 编译工程

编译工程可以直接在hello目录下面输入`make`命令，也可以用刚才提到的apxs文件执行，我们采用简单的方式：

1. 确认自己在hello目录下
2. `make`
3. `sudo make install`

新生成的文件会被安装到`/usr/local/apache2/modules/`目录下面，为mod\_hello.so

## 配置模块

apache如何知道我们新创建的模块呢？这就需要在配置文件中进行相关配置：

1. 打开文件`/usr/local/apache2/conf/httpd.conf`
2. 在文件最后追回如下内容：

		LoadModule hello_module modules/mod_hello.so
		<Location /hello>
			SetHandler hello
		</Location>

3. 重新启动apache
		
		sudo /usr/local/apache2/bin/apachectl stop
		sudo /usr/local/apache2/bin/apachectl start

## 验证成果

1. `curl "http://localhost/hello"`
2. 会看到`The sample page from mo_hello.c`
3. 成功！

# 总结

1. 我们通过源码安装了apache服务器。
2. 快速创建了一个hello模块。
3. 加载hello模块，并对其进行了验证。

更多精彩，后续更新~

