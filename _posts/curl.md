---
layout: page
title: curl manual
category: blog
description:
---

# Usage

	-F,--form
	-D- Dump the header to the file listed, or stdout when - is passed, like this.
	-o/dev/null Send the body to the file listed. Here, we discard the body so we only see the headers.
	-s Silent (no progress bar)
    -m seconds
        timeout

# header
curl 默认发送post数据是: application/x-www.form-urlencoded(不同于在form 表单中设置: enctype="multipart/form-data"), 如果是`text/plain`, post 数据就被存放于 HTTP_RAW_POST_DATA.

	#send post as HTTP_RAW_POST_DATA (file_get_contents(php://input)):
	//$GLOBALS["HTTP_RAW_POST_DATA"];
	-H 'Content-Type: text/plain' -d 'a=1&b=2'
	-X PUT/POST/GET/DELETE

## -I

	-I, --head
	  (HTTP/FTP/FILE)  Fetch  the  HTTP-header only!

# upload

	curl 'http://localhost:8000/up.php'  -F 'pic=@img/a.png'
	curl 'http://localhost:8000/up.php'  -F 'pic=@img/a.png' -F 'var=value'
	curl -F "file=@localfile;filename=nameinpost" url.com
	curl -F "file=@localfile;filename=nameinpost;type=text/html" url.com
	curl 'http://localhost:8000/up.php' -H 'Content-Type: multipart/form-data; boundary=W' -d $'--W\r\nContent-Disposition: form-data; name="pic"; filename="a.png"\r\nContent-Type: image/png\r\n\r\ndata\r\n--W\r\nContent-Disposition: form-data; name="var"\r\n\r\nvalue\r\n--W--\r\n'

# Cookie

	-c/--cookie-jar <file> 操作结束后把cookie写入到这个文件中
	-b/--cookie <name=string/file> cookie字符串或文件读取位置
	-j/--junk-session-cookies
		this option will make it discard all "session cookies".

	curl -c a.cookie -b a.cookie curl
	curl -b a.cookie -c a.cookie http://127.0.0.1:8080/a.php

# proxy

## interface

    curl --interface eth0

## via socks5
Socks5 takes precedence over -x:

> This option overrides any previous use of -x, --proxy, as they are mutually exclusive.

	--socks5 <host[:port]>
	--socks5 127.0.0.1:1080

## via proxy
global:

	export http_proxy=http://your.proxy.server:port/

cmd:

	-x, --proxy <[protocol://][user:password@]proxyhost[:port]>

# debug

	-v verbose
	-s silent

	--trace <file>
	--trace-ascii -
		Enables a full trace dump of all incoming and outgoing data(without hex)

## format

	-w --write-out <format>

Example

	curl -w "TCP handshake: %{time_connect}, SSL handshake: %{time_appconnect}\n" -so /dev/null https://www.alipay.com

# Reference
http://blog.51yip.com/linux/1049.html
