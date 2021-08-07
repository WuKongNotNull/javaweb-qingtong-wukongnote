# web概念

一种分布式的应用架构。客户端/服务端的通信模式 ，通过http协议进行通信的。

3个特性

1 信息表达  使用html技术表达信息

2 信息定位  使用统一资源定位技术（URL）（Uniform Resource Locator）

3 信息传输  使用http 技术实现

## html简介

特征

可以包含纯文本信息

可以包含多媒体信息

可以包含信息的展示格式

可以通过超链接连接其他信息



## url简介

统一资源定位技术（URL）（Uniform Resource Locator）

是一种编码的方式，为了快速找到网络中资源的位置。

3个部分

应用层协议  https http

主机ip地址或者域名  www.taobao.com

资源所在的路径/文件名   mobile/a.html



## http简介

超文本传输协议

http客户端 ,包含浏览器

http服务器



**http的请求格式**

请求方法 request method

method (crud)

​	get  获得服务器中文档（查询）

​	post  客户端发送信息给服务器（添加）

​	head  客户端和服务器之间交流的一些内部数据 。使用场景： 搜索引擎获得网页的标志信息；或者传递的认证            				信 息

​	put  修改服务器中信息

​	delete   删除服务器中的文档

请求头 request header

请求正文 request content



**http的响应格式**

http的版本、状态和描述

​	常见的状态码
​		200 响应成功

​		400 客户端发送错误的请求

​		404  文件不存在

​		405  服务器不支持客户端的请求方式

​		500 服务器内部的错误

响应头  response header

响应的正文 response content



**正文部分的MIME类型**

文件扩展名 	          MIME类型

.bin .exe .o .a .z        application/octet-stream

.pdf    						application/pdf

.zip							application/zip

.tar							application/x-tar

.gif 							image/gif

.jpg  .jpeg				image/jpeg

.htm  .html				text/html

.txt .text .java .h		text/plain

.mpg .mpeg				video/mpeg

.xml						application/xml

form 信息					multipart/form-data

