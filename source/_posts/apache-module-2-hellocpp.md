---
title: apache模块开发（二）：拥抱c++
date: 2016-09-20 19:26:46
tags: [apache, httpd, module]
---

[上一篇](http://alimap.org/2016/09/19/apache-module-1-quickstart/)中我们快速开发了个一hello模块，当我们通过http请求 http://localhost/hello 的时候，会返回一个简单的字符串。

从快速开发的角度来讲，我们就可以基于上一篇的工程来加入我们自己的逻辑了。但是，我想说的是但后，apache是用C语言写的，其提供的工具apxs默认也只能编译C的代码。可我更喜欢用C++来写我的处理逻辑，怎么办？如何支持呢？

网上有很多方法，有对apxs加参数的，有自己写Makefile的，都可以实现。但是，我还是觉得有些麻烦，不会写Makefile啊啊啊……

于是，我想了个办法，相对简单一点，用cmake，试验成功！

下面就跟大家共享一下。

1. 用apxs创建工程：`apxs -g -n hellocpp`
2. 进入hellocpp目录：`cd hellocpp`
3. 将mod\_hellocpp.c 修改为mod\_hellocpp.cpp
4. 创建两个c++文件：`touch Hello.h Hello.cpp`
5. 修改Hello.h文件内容为：
	
		#ifndef _HELLO_H_
		#define _HELLO_H_
		#include <string>
		class CHello
		{
		public:
			std::string sayHello();
		};
		#endif
6. 个性Hello.cpp的文件内容为：
		
		#include "Hello.h"
	
		std::string CHello::sayHello()
		{
	    	return "My C plus plus hello.";
		}
7. 修改mod\_hellocpp.cpp中的函数hellocpp\_handle内容为利用c++的类函数替换原来的字符串，当然文件头要加入`#include "Hello.h"`：
		
		static int hellocpp_handler(request_rec *r)
		{
	    	if (strcmp(r->handler, "hellocpp")) {
	       		return DECLINED;
	    	}
	    	r->content_type = "text/html";
	
	    	if (!r->header_only)
	    	{
	        	CHello hello;
	        	ap_rputs(hello.sayHello().c_str(), r);
	        	ap_rputs("\n", r);
	    	}
	    	return OK;
		}
8. 修改module的声明，在声明的前面加入extern “C”:
	extern "C" module AP_MODULE_DECLARE_DATA hellocpp_module = {

好，至此，代码已经完成，有c++的类并在模块中进行了调用。

接下来就做的就是将mod\_hellocpp.cpp Hello.h 和 Hello.cpp编译成so文件。

如上所说，我们要用cmake来编译，就需要先写CMakelists.txt文件：
		
		PROJECT(hellocpp)
		SET(CMAKE_C_FLAGS "-Wall -std=c++0x -Wno-deprecated -shared -fPIC")
		SET(CMAKE_CXX_FLAGS "-Wall -std=c++0x -Wno-deprecated -shared -fPIC")
		SET(CMAKE_CXX_COMPILER "g++")
		AUX_SOURCE_DIRECTORY(. SRC_LIST)
		INCLUDE_DIRECTORIES(/usr/local/apache2/include)
		INCLUDE_DIRECTORIES(/usr/include/apr-1)
		LINK_DIRECTORIES(/usr/local/lib)
		ADD_LIBRARY(mod_hellocpp SHARED ${SRC_LIST})
		TARGET_LINK_LIBRARIES(mod_hellocpp apr-1)

就快完成了，现在让我们编译试试：

1. `mkdir build`
2. `cd build`
3. `cmake ..`
4. `make`

如果这时候，在目录中有mod\_hellocpp.so生成，就说成功了。

验证一下：

1. 将mod\_hellocpp.so拷贝至/usr/local/apache2/modules/
2. 在/usr/local/apache2/conf/httpd.conf文件最后增加如下内容：
		
		LoadModule hellocpp_module    modules/mod_hellocpp.so
		<Location /hellocpp>
	    	SetHandler hellocpp
		</Location>
3. 重启apahce: `/usr/local/apache2/bin/apachectl restart`
4. 验证： `curl "http://localhost/hellocpp"`
5. 不出意外，应该看到`My C plus plus hello.`

剩下的就自己发挥吧~
