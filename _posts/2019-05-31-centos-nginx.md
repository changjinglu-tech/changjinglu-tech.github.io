---
layout: post
title:  "centOS中搭建nginx，并使用letsencrypt配置http/2.0（part 1）"
date:   2019-05-31 16:14:28 +0800--
categories: [配置]
tags:   [nginx, centOS]
---

前几天为了使自己的接口支持https，就想直接使用http/2.0，配置的过程中遇到一些小坑，写个文章记录一下，另外希望对刚开始配置的读者一些帮助和参考。
明确我们的最终目标，也就是标题：给自己的nginx配置http/2，为了实现这个目标，并不是我们服务器已经有nginx就可以了，你的nginx版本很有可能不符合http/2的要求。因为，nginx从1.9.5版本才开始默认支持http/2，并移除了`SPDY`模块。
如果你是刚开始搭建服务器环境，那么很简单，就直接安装1.9.5之后的稳定版本nginx就好。为了文章的完整性，再简单说一下nginx的安装。
在安装nginx之前，请确保安装了`g++`和`gcc`（可用`yum`进行安装）。

**先进入安装目录`usr/local`，分别安装`openssl`、`pcre`、`zlib`。(请选择最新稳定版本)：**

```
#下载：
$ wget http://www.openssl.org/source/openssl-1.1.0h.tar.gz

#解压：
$ tar -zxvf openssl-1.1.0h.tar.gz

#进入源码目录并配置：
$ cd openssl-1.1.0h
$ ./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl/conf

#编译安装
$ make && make install

#检查安装是否成功
$ /usr/local/openssl/bin/openssl version -a 

```
**用同样的方法安装好`pcre`和`zlib`。**

上面三个安装好之后，再进行nginx安装，步骤类似：

```shell
#解压
$ tar -zxvf nginx-1.8.0.tar.gz

#进入安装目录
$ cd nginx-1.8.0

#配置
$ ./configure \
--user=www \
--group=www \
--prefix=/usr/local/nginx \
--with-http_ssl_module \
--with-http_v2_module \
--with-openssl=/usr/local/openssl-1.1.0h \
--with-pcre=/usr/local/pcre-8.37 \
--with-zlib=/usr/local/zlib-1.2.11 \
--with-http_stub_status_module \
--with-threads
```
配置的时候要注意的地方有两点：
一个是要对应好openssl、pcre、zlib的源码路径和版本；
注意配置参数中的--with-http_v2_module，这是nginx1.9.5之后新增的模块，专门用来支持http/2。所以要想进行后面的http/2配置，这个参数绝对不能漏。
然后编译安装：

```
$ make && make install
```

启动：

```
$ /usr/local/nginx/sbin/nginx 

```
这样你的nginx就基本搭建完成了。可以直接访问公网IP或者你的域名查看nginx默认欢迎页。
下一节我将详细讲述如何使用letsencrypt配置http/2。






