---
layout: page
title:
category: blog
description:
---
# Preface

# 错误与异常

## error
Go语言错误处理:
http://tonybai.com/2015/10/30/error-handling-in-go/
错误是值
https://www.360us.net/article/34.html

### debug mode

    PYTHONASYNCIODEBUG=1 python3 a.py

## 异常
可以在except语句块后面加一个else，当没有错误发生时，会自动执行else语句：

	try:
		print('try...')
		r = 10 / int('2')
		print('result:', r)
	except ValueError as e:
		print('ValueError:', e)
	except ZeroDivisionError as e:
		print('ZeroDivisionError:', e)
	except:
		print('no error!')
	finally:
		print('finally...')
	print('END')

### 记录异常错误

	import logging

    try:
    	10 / 0
    except Exception as e:
        logging.exception(e)

通过配置，logging还可以把错误记录到日志文件里，方便事后排查。


	logging.basicConfig(filename='example.log',level=logging.DEBUG)
	logging.basicConfig(filename=logname,
                            filemode='a',
                            format='%(asctime)s,%(msecs)d %(name)s %(levelname)s %(message)s',
                            datefmt='%H:%M:%S',
                            level=logging.DEBUG)

logging file path(default by current path):

	logging.getLoggerClass().root.handlers[0].baseFilename

### 抛出异常

	raise TypeError('invalid value: %s' % s)
	# 或
	except ValueError as e:
        print('ValueError!')
        raise

# 调试
调试包括print, assert, logging

## 用print

## assert 断言

	def foo(n):
		assert n != 0, 'n is zero!'
		return 10 / n

	foo(0)

如果断言失败，assert语句本身就会抛出AssertionError：

	AssertionError: n is zero!

Python解释器时可以用-O参数来关闭assert：

	$ python3 test.py

## pdb
pdb，让程序以单步方式运行，可以随时查看运行状态。

	s = '0'
	n = int(s)
	print(10 / n)

以参数-m pdb启动后，pdb定位到下一步要执行的代码-> s = '0'。输入命令l来查看代码：

	(Pdb) l
	  2  -> s = '0'
	  3     n = int(s)
	  4     print(10 / n)

单步命令：

	> n 下一步
	> s 进入函数

继续执行:

	> c 继续执行

打印命令：

	> p <var> 打印变量

退出:

	> q

## 设置断点
使用`-m pdb` 相当是在命令开始执行时就断点。我们可以通过`pdb.set_strace` 设置断点

	pdb.set_trace() # 运行到这里会自动暂停，并启动pdb

如果要比较爽地设置断点、单步执行，就需要一个支持调试功能的IDE。目前比较好的Python IDE有PyCharm.

## trace


	import traceback

	traceback.print_stack([file=sys.stderr]);
	#or
    stack_str = ''.join(traceback.format_stack())

## inspect

	from inspect import currentframe, getframeinfo
	frameinfo = getframeinfo(currentframe())
	print frameinfo.filename, frameinfo.lineno

# perf
python 性能分析
http://www.imooc.com/article/4170

In Python 3.5 (deadsnakes PPA):

	import timeit
	>>> min(timeit.repeat(lambda: {**x, **y}))
	0.4094954460160807
	>>> min(timeit.repeat(lambda: merge_two_dicts(x, y)))
	0.7881555100320838
	>>> min(timeit.repeat(lambda: {k: v for d in (x, y) for k, v in d.items()} ))
	1.4525277839857154
