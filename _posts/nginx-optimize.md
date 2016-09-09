---
layout: page
title:	
category: blog
description: 
---
# Preface
Apache vs nginx: [nginx-performance]

- Apache works by using a dedicated thread per client with blocking I/O.
- Nginx uses a single threaded non-blocking I/O mechnism to serve requests. As it uses non-blocking I/O, one single process can server too many connection requests.

引述 http://tengine.taobao.org/book/chapter_02.html 的话：
> 想想apache的常用工作方式（apache也有异步非阻塞版本，但因其与自带某些模块冲突，所以不常用），每个请求会独占一个工作线程，当并发数上到几千时，就同时有几千的线程在处理请求了。这对操作系统来说，是个不小的挑战，线程带来的内存占用非常大，线程的上下文切换带来的cpu开销很大，自然性能就上不去了，而这些开销完全是没有意义的。

# todo

## trim
http://tengine.taobao.org/document_cn/http_trim_filter_cn.html

## high
http://blog.xiayf.cn/2014/05/03/optimizing-nginx-and-php-fpm-for-high-traffic-sites/
http://www.oschina.net/translate/optimizing-nginx-for-high-traffic-loads

tcp:
http://jaseywang.me/2014/07/20/tcp-queue-%E7%9A%84%E4%B8%80%E4%BA%9B%E9%97%AE%E9%A2%98/

## backlog & nproc
http://www.t086.com/article/5182

## thread-pools 线程池
https://www.nginx.com/blog/thread-pools-boost-performance-9x/

# events

## epoll
Nginx uses *a non-blocking I/O with a single thread or process*. Hence the *single process* must identify which connection file is ready to be read or written to. For this, the operating system has three different methods. They are mentioned below.
 
	Select Method
	Poll Method
	Epoll Method(Most efficient)

> So nginx can use either select or poll method to identify which file(file descriptor) is ready to be read or written to. But both these methods are not efficient enough when there are too many number of connections. 
For example, if you want to serve say 10000 connections at a time. And only one of the them is ready to be read. 
To identify that one file descriptor to which is ready to be read, the process has to still scan through rest of the 9999 file descriptors (which is waste of resource).

Another method other than the select and Poll is called as epoll comes to rescue here. This is only available in Linux kernels which are later than 2.6. Epoll uses an efficient mechanism compared to poll and select.
 
> If you have 10000 connections made to your server, and 8000 among them are idle connections. Using poll and select is not that efficient because epoll will only give importance to connections that are active. Please note the fact that all three select, poll and epoll basically does the same thing, but using epoll is less cpu intensive when you have to serve thousands of connections. 

	events {
		use epoll;
	}

## worker

### worker_connections
define the maximum number of connections which can be simultaniously served by a single worker process. The default is 512 connections

	events {
		use epoll;
	    worker_connections 10240;
	}

### multi_accept
The option multi_accept makes the worker process accept all new connections instead of serving on at a time:

	multi_accept on;#default off, context: events

### file descriptors
Set  maximum number of files descriptors

	worker_rlimit_nofile 100000;
	worker_processes 8;
	events {
		worker_connections 1024;
		use epoll;
		multi_accept on;
	}

> So now my nginx web server is ready to serve around 8192 connections(8 worker process * 1024 connections ) at one time. 

# http

## buffers
Sets the number and size of the buffers used for reading a response from a disk.

	Syntax:	output_buffers number size;
	Default:	output_buffers 1 32k;
	Context:	http, server, location

## keepalive
context: http, server, location

### keepalive_disable(browser)
disable browser

	keepalive_disable msie6;//default: msie6 Syntax:	keepalive_disable none | browser ...;

### keepalive_timeout

	keepalive_timeout 75s;
	keepalive_timeout 0;//keepalive_disable all

### keepalive_requests
Sets the maximum number of requests that can be served through one keep-alive connection for a client

	keepalive_requests number;# default 100

### reset_timedout_connection
Keeping connections open for a client which is not responding is not a good idea, its always better to close that connection and free up memory associated with it. This can be done as shown below.	

This helps avoid keeping an already closed socket with filled buffers in a FIN_WAIT1 state for a long time.

	reset_timedout_connection on;

## IO

### sendfile

	sendfile on;
	tcp_nopush on;

### log
To decrease log IO 

	location ~* '^(js|img|css|htm)/'{
		access_log off;
	}

### file
*open_file_cache*
Please note the fact that the inactive switch tells the time after which a cache entry will be removed if inactive.

	open_file_cache max=1000 inactive=20s;# only cache this 1000 of file entries. Old inactive entries are automatically flushed out

	open_file_cache_valid 30s;
	open_file_cache_min_uses 2;
	open_file_cache_errors on;

The open file cache is a caching system for metadata operations (file mtime, file existence etc), not for file content

1. Open File Descriptors
1. File Descriptor modification time, their size etc.
1. Error status like no permission, no file etc

Other options:

1. Validity of a cache entry (open_file_cache_valid).
1. Minimum number of times the cache entry has to be accessed before the inactive number of seconds, so that it stays in cache (open_file_cache_min_uses)
1. Cache errors while searching a file (open_file_cache_errors)

## compress
There are many compress algorithm: gzip, deflate, sdch

Take an extra care of what to compress, when to compress, and the level of compression. Also we need to have a mechanism where we can disable compression for older web browsers that does not support this feature.

	gzip on;
	gzip_min_length  1100; # The length is determined only from the “Content-Length” response header field.
	gzip_types	text/plain application/x-javascript application/json text/xml text/css;
	gzip_vary on;		# Enables or disables inserting the “Vary: Accept-Encoding” response header field
	gzip_comp_level  6;# range from 1 to 9, default: 1,  too much compression does not make a substantial difference,

> gzip_http_version 1.0; Sets the minimum HTTP version of a request required to compress a response. default: 1.1
> It costs a little CPU, and it's irrelevant for nginx performance

# General setting
Refer to: https://seravo.fi/2013/optimizing-web-server-performance-with-nginx-and-php

Set process count as cpu cores is a good start:

	# grep -c ^processor /proc/cpuinfo
	worker_processes 2;

## connect

### keepalive
A huge keepalive in turn makes the server keep all connections open ready for consecutive requests:

	keepalive_requests 100000;

For upstream, use keepalive:

	upstream memcached_backend {
		server 127.0.0.1:11211;
		server 10.0.0.2:11211;
		keepalive 32;
	}

# fastcgi

## timeout

	fastcg_connect_timeout = 1s
	fastcg_response_timeout = 3s

# Cache

## fastcgi_cache in nginx
Refer to:
https://seravo.fi/2013/optimizing-web-server-performance-with-nginx-and-php

	server {
	[...]
	  set $cache_uri $request_uri;

	  # POST requests and urls with a query string should always go to PHP
	  if ($request_method = POST) {
		set $cache_uri 'null cache';
	  }
	  if ($query_string != "") {
		set $cache_uri 'null cache';
	  }

	  # Don't cache uris containing the following segments
	  if ($request_uri ~* "/wp-admin/") {
		set $cache_uri 'null cache';
	  }

	  # Don't use the cache for logged in users or recent commenters
	  if ($http_cookie ~* "comment_author|wordpress_[a-f0-9]+|wp-postpass|wordpress_logged_in") {
		set $cache_uri 'null cache';
	  }

	  location ~ \.php$ {
		set $skip_cache 1;
		if ($cache_uri != "null cache") {
		  add_header X-Cache-Debug "$cache_uri $cookie_nocache $arg_nocache$arg_comment $http_pragma $http_authorization";
		  set $skip_cache 0;
		}
		fastcgi_cache_bypass $skip_cache;
		fastcgi_cache microcache;
		fastcgi_cache_key $scheme$host$request_uri$request_method;
		fastcgi_cache_valid any 8m;
		fastcgi_cache_use_stale updating;
		fastcgi_cache_bypass $http_pragma;
		fastcgi_cache_use_stale updating error timeout invalid_header http_500;

## memc
使用memc-nginx和srcache-nginx构建高效透明的缓存机制 by taobao design

http://blog.linezing.com/?p=919

# Reference
- [nginx-performance]: http://www.slashroot.in/nginx-web-server-performance-tuning-how-to-do-it
