---
layout: page
title:
category: blog
description:
---
# Preface

# StringIO和BytesIO

## File-like Object
像open()函数返回的这种有个read()方法的对象，在Python中统称为file-like Object。除了file外，还可以是内存的字节流，网络流，自定义流等等。file-like Object不要求从特定类继承，只要写个read()方法就行。

- StringIO/BytesIO 就是在内存中创建的file-like Object，常用作临时缓冲。

## StringIO
StringIO顾名思义就是在内存中读写str。

要把str写入StringIO，我们需要先创建一个StringIO，然后，像文件一样写入即可：

	>>> from io import StringIO
	>>> f = StringIO()
	>>> f.write('hello')
	5
	>>> f.write(' ')
	1
	>>> f.write('world!')
	6
	>>> print(f.getvalue())
	hello world!

### init

	>>> from io import StringIO
	>>> f = StringIO('Hello!\nHi!\nGoodbye!')

### read

	f.read()
	f.readline()
	f.readlines()

## BytesIO
StringIO操作的只能是str，如果要操作二进制数据，就需要使用BytesIO。

	>>> from io import BytesIO
	>>> f = BytesIO()
	>>> f.write('中文'.encode('utf-8'))
	6

	>>> print(f.getvalue())
	b'\xe4\xb8\xad\xe6\x96\x87'

string 与 bytes 相互转换

	>>> '123€20'.encode('utf-8')
	b'123\xe2\x82\xac20'
	>>> b'\xe2\x82\xac20'.decode('utf-8')
	'€20'
	>>> '123'.encode('utf-8')

或者：

	>>> bytes([0, 1, 97])
	b'\x00\x01a'

# url

	from urllib import urlopen

readline:

	urlp = urlopen(url);
	while(line = urlp.readline()){

	}

readlines:

	for line in urlopen(url).readlines():
		print line.strip()

# pipe

    for l in sys.stdin.readlines():
        sys.stdout.write(l[::-1])

# argv

	from sys import argv # import module "sys" and objects: argv
	script, arg1, arg2 = argv

	import sys;
	sys.argv

# input

	str = input()
	str = input("Input some string:")

## stdin file
参考  python-io.md

	import fileinput
	for line in fileinput.input():
		print(line)
