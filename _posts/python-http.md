---
layout: page
title:
category: blog
description:
---
# Preface

# url

	urllib.parse.urlsplit(url)
	urllib.parse.urlparse(url)
		.scheme
		.path
		.query
		.host

## parse_url

	import urllib.parse
	urllib.parse.parse_qs('a=1&b=2');
		{'a': ['1'], 'b': ['2']}
	urllib.parse.parse_qsl('a=1&b=2&b=3')
		[('a', '1'), ('b', '2'), ('b', '3')]
	urllib.parse.parse_qs('a=1&b=2',unique_key=True);# custom unique_key

	urllib.parse.urlencode({'a':1})
	'a=1'
	urllib.parse.urlencode({'a':[1,2]}, doseq=True)
	'a=1&a=2'


# urllib2
urllib2 is deprecated

## proxy

	import urllib
	import urllib2
	url = 'http://weibo.cn'
	data = urllib.urlencode({'k':'v'})
	opener = urllib2.build_opener(urllib2.ProxyHandler({'http':'http://ip:port'}))
	urllib2.install_opener(opener)
	req = urllib2.Request(url, data)
	try:
		response = urllib2.urlopen(req,timeout=2)
		the_page = response.read()
		print the_page.decode('utf-8')
	except:
		pass

# Request
http://docs.python-requests.org/en/latest/index.html

## Non Block
If you are concerned about the use of blocking IO, there are lots of projects out there that combine Requests with one of Python’s asynchronicity frameworks. Two excellent examples are grequests and requests-futures.

## install

	easy_install requests //2.7
	pip3 install requests
	import requests

## request

	>>> r = requests.get('https://127.0.0.1:8000/a.php')
	>>> r = requests.post("http://httpbin.org/post", data = {"key":"value"})
	>>> r = requests.post("http://httpbin.org/post", data = (("key","value",),))
	>>> r = requests.put("http://httpbin.org/put", data = {"key":"value"})
	>>> r = requests.delete("http://httpbin.org/delete")
	>>> r = requests.head("http://httpbin.org/get")
	>>> r = requests.options("http://httpbin.org/get")

### params in url
Passing Parameters In URLs

	>>> r = requests.get("http://httpbin.org/get", params={'key':'val'})
	>>> print(r.url)
		http://httpbin.org/get?key=val

	>>> r = requests.get("http://httpbin.org/get", params="key=val&key2=val2"})


### post data(non-raw)

	>>> print(s.post('http://hilo.sinaapp.com/header.php', data={'a':1}).text)

### post json(raw)

Instead of encoding the dict yourself, you can also pass it directly using the json parameter (added in version 2.4.2) and it will be encoded automatically:

	>>> payload = {'some': 'data'}
	>>> r = requests.post(url, json=payload)

	json='a=1&b=2'
	 ["raw"]=>
	  string(9) ""a=1&b=2""

>>> print(s.post('http://hilo.sinaapp.com/header.php', json='a=1&b=2').text)

### post a Multipart-Encoded File

	>>> files = {'file': open('report.xls', 'rb')}
	>>> r = requests.post(url, files=files)

You can set the filename, content_type and headers explicitly:

	>>> files = {'file': ('report.xls', open('report.xls', 'rb'), 'application/vnd.ms-excel', {'Expires': '0'})}
	>>> r = requests.post(url, files=files)

If you want, you can send strings to be received as files:

	>>> files = {'file': ('report.csv', 'some,data,to,send\nanother,row,to,send\n')}
	>>> r = requests.post(url, files=files)

### post data

	data=urllib.parse.parse_qs('a=1&b=1')
	>>> r = requests.post("http://httpbin.org/post", data = data)

## proxy
代理

    proxies = {
      "http": "http://10.10.1.10:3128",
      "https": "http://10.10.1.10:1080",
    }

    requests.get("http://example.org", proxies=proxies)

你也可以通过环境变量 HTTP_PROXY 和 HTTPS_PROXY 来配置代理。

    $ export HTTP_PROXY="http://10.10.1.10:3128"
    $ export HTTPS_PROXY="http://10.10.1.10:1080"

若你的代理需要使用HTTP Basic Auth，可以使用 http://user:password@host/ 语法：

    proxies = {
        "http": "http://user:pass@10.10.1.10:3128/",
    }

要为某个特定的连接方式或者主机设置代理，使用 scheme://hostname 作为 key， 它会针对指定的主机和连接方式进行匹配。

    proxies = {'http://10.20.1.128': 'http://10.10.1.10:5323'}

### SOCKS
除了基本的 HTTP 代理，Request 还支持 SOCKS 协议的代理。这是一个可选功能，若要使用， 你需要安装第三方库。

    $ pip install requests[socks]

安装好依赖以后，使用 SOCKS 代理和使用 HTTP 代理一样简单：

    proxies = {
        'http': 'socks5://user:pass@host:port',
        'https': 'socks5://user:pass@host:port'
    }

## Custom Headers
For example, we didn’t specify our user-agent in the previous example:

	>>> url = 'https://api.github.com/some/endpoint'
	>>> headers = {'user-agent': 'my-app/0.0.1'}

	>>> r = requests.get(url, headers=headers)


## Response Content

### response text

	>>> r.text
	u'[{"repository":{"open_issues":0,"url":"https://github.com/...
	>>> r.encoding
	'utf-8'
	>>> r.encoding = 'ISO-8859-1'

### response binary content("bytes")

	>>> r.content
	b'[{"repository":{"open_issues":0,"url":"https://github.com/...

	 //to create an image from binary data returned by a request, you can use the following code:
	>>> from PIL import Image
	>>> from StringIO import StringIO
	>>> i = Image.open(StringIO(r.content))

### Response json

	r.json()['key']

### Raw Response Content

In the rare case that you’d like to get the raw socket response from the server, you can access r.raw. If you want to do this, make sure you set stream=True in your initial request. Once you do, you can do this:

	>>> r = requests.get('https://api.github.com/events', stream=True)
	>>> r.raw
	<requests.packages.urllib3.response.HTTPResponse object at 0x101194810>
	>>> r.raw.read(10)
	'\x1f\x8b\x08\x00\x00\x00\x00\x00\x00\x03'

In general, however, you should use a pattern like this to save what is being streamed to a file:

	with open(filename, 'wb') as fd:
		for chunk in r.iter_content(chunk_size):
			fd.write(chunk)

### response status_code

	>>> r.status_code
	200

	>>> r.status_code == requests.codes.ok
	True

### Response Headers
We can view the server’s response headers using a Python dictionary:

	>>> r.headers
	{
		'content-encoding': 'gzip',
		'transfer-encoding': 'chunked',
		'connection': 'close',
		'content-type': 'application/json'
	}

The dictionary is special, though: it’s made just for HTTP headers. According to RFC 7230, HTTP Header names are case-insensitive.

	>>> r.headers['Content-Type']
	'application/json'

	>>> r.headers.get('content-type')
	'application/json'

### Cookie
If a response contains some Cookies, you can quickly access them:

	>>> r.cookies['example_cookie_name']
	'example_cookie_value'

To send your own cookies to the server, you can use the cookies parameter:

	>>> r = requests.get(url, cookies={'key':'value'})
	>>> r = requests.get(url, cookies='key=value')

	>>> r1 = requests.post('http://www.yourapp.com/login')
	>>> r2 = requests.post('http://www.yourapp.com/somepage',cookies=r1.cookies)

keep cookie

	>>> s.get('https://www.google.com', cookies = {'cookieKey':'cookieValue'})

### Redirection and History
By default Requests will perform location redirection for all verbs except HEAD.

The Response.history list contains the Response objects that were created in order to complete the request. The list is sorted from the oldest to the most recent response.

	>>> r = requests.get('http://github.com')
	>>> r.url
	'https://github.com/'
	>>> r.status_code
	200
	>>> r.history
	[<Response [301]>]

If you’re using GET, OPTIONS, POST, PUT, PATCH or DELETE, you can disable redirection handling with the allow_redirects parameter:

	>>> r = requests.get('http://github.com', allow_redirects=False)
	>>> r.status_code
	301
	>>> r.history
	[]

If you’re using HEAD, you can enable redirection as well:

	>>> r = requests.head('http://github.com', allow_redirects=True)

## Timeouts
You can tell Requests to stop waiting for a response after a given number of seconds with the timeout parameter:

	>>> requests.get('http://github.com', timeout=0.001)
	Traceback (most recent call last):
	  File "<stdin>", line 1, in <module>
	requests.exceptions.Timeout: HTTPConnectionPool(host='github.com', port=80): Request timed out. (timeout=0.001)

## Session
Use a session object instead, it'll persist cookies and send them back to the server:

	with requests.Session() as s:
		r = s.get(URL1)
		r = s.post(URL2, params="username and password data payload")

取设headers:

	s = requests.Session()
	s.auth = ('user', 'pass')
	s.headers.update({'x-test': 'true'})
	// both 'x-test' and 'x-test2' are sent
	s.get('http://httpbin.org/headers', headers={'x-test2': 'true'})

	# 如何构造？？help(*cookie_jar)
	s.cookies.set_cookie('a=1;b=2;domain/')? tetst

如果你要手动为会话添加 cookie，就是用 [Cookie utility] 函数 来操纵 Session.cookies。
[Cookie utility]: http://docs.python-requests.org/zh_CN/latest/api.html#api-cookies

## Session Cookie

	from six.moves import http_cookiejar as cookielib

	session.cookies = cookielib.LWPCookieJar(COOKIE_FILE)
	session.cookies.load(ignore_discard=True, ignore_expires=True)

	session.get(url)...
	session.cookies.save(COOKIE_FILE, ignore_discard=True, ignore_expires=True)
