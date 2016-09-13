---
layout: page
title:	Learn Python
category: blog
description:
---
# Preface

# python3
deep:
http://www.diveintopython3.net/
http://py.windrunner.info/magic/magic.html

# shell

	cat a.py | python

# Help

	$ pydoc <name>
	$ pydoc pydoc
	$ pydoc open
	$ pydoc file

## in python

	> help(file)
	var=1
	> help(var)

## reload vs import
 多次重复使用import语句(其实是`__import__`)时，*不会重新加载执行* 被指定的模块，只是把对该模块的内存地址给引用到本地变量环境。

	import sys
 	print(id(sys))
	import sys
 	print(id(sys))

reload 对已经加载的模块进行重新加载(不含子模块)，一般用于原模块有变化等特殊情况，reload前该模块必须已经import过。

	from importlib import reload
	reload(sys);	# 因为setdefaultencoding函数在被系统调用后被删除了，所以通过import引用进来时其实已经没有了，所以必须reload
	sys.setdefaultencoding('utf8')  ##调用setdefaultencoding函数

# install

## pip3

	easy_install requests //2.7
	pip3 install requests
	import requests
