# import

wirte a `hilo.py`:

	def add(a, b):
		return a+b

Then under interact python envirment:

	import hilo
	#from . import hilo
	print hilo.add(1,2)
	add = hilo.add
	print add(1,2)

	help(hilo) # What did you see?
	help(hilo.add)
	help(add)

import `ex47.game`, would find two file:

	ex47/game.py
	ex47/__init__.py

请注意，每一个包目录下面都会有一个__init__.py的文件，这个文件是必须存在的，否则，Python就把这个目录当成普通目录，而不是一个包。__init__.py可以是空文件，也可以有Python代码，因为__init__.py本身就是一个模块，而它的模块名就是mycompany。

## import level

	import urllib; # 只引入urllib
	import urllib.*; # 引入 urllib.* 及urllib

## __name__
命令行执行模块时，`__name__` = `__main__` , import 导入时，它等于模块名

	if __name__=='__main__':
		print(__name__)

## path
此PATH 与 SHELL PATH 是独立的

### module path

	import a_module
	print a_module.__file__

### sys.path

	>>> import sys
	>>> sys.path.append('path')
	>>> print(sys.path)
	['', '/usr/local/Cellar/python3/3.5.0/Frameworks/Python.framework/Versions/3.5/lib/python35.zip', '/usr/local/Cellar/python3/3.5.0/Frameworks/Python.framework/Versions/3.5/lib/python3.5', '/usr/local/Cellar/python3/3.5.0/Frameworks/Python.framework/Versions/3.5/lib/python3.5/plat-darwin', '/usr/local/Cellar/python3/3.5.0/Frameworks/Python.framework/Versions/3.5/lib/python3.5/lib-dynload', '/usr/local/lib/python3.5/site-packages']
	 '/usr/local/lib/python3.5/site-packages'
	 /usr/local/lib/python3.5/site-packages/pip/_vendor/requests/cookies.py

### PYTHONPATH
> PYTHONPATH Augment the default search path for module files. The format is the same as the shell’s PATH

相当于: sys.path.append()

	export PYTHONPATH=.

# scope
模块内变量作用scope

1. public:
	正常的函数和变量名是公开的（public），可以被直接引用，比如：abc，x123，PI等；

1. 特殊变量`__xxx__`： 可以被直接引用，但是有特殊用途
	__author__，__name__就是特殊变量
	__doc__ 文档注释也可以用特殊变量

3. `_xxx和__xxx`
	这样的函数或变量就是非公开的（private），不应该被直接引用，比如_abc，__abc等；

> 之所以我们说，private函数和变量“不应该”被直接引用，而不是“不能”被直接引用，是因为Python并没有一种方法可以完全限制访问private函数或变量

## 类中的私有属性

    class Demo(object):
        def __p(self): pass
        _b = 1
        __c = 1

    print(Demo.__dict__.keys())
    d=Demo()
    print(d._Demo__p)
    print(d._Demo__c)

对于`__x`, 这样的私有属性, 为了禁止访问私有属性, 它会将它改名为`_Demo__x`(禁止不了的!)

    (['_Demo__p', '_b', '_Demo__c'])

## `__all__`
> 参考: http://python-china.org/t/725
Python 靠一套需要大家自觉遵守的”约定“下工作。 比如下划线开头的应该对外部不可见。all 则不是人为约定, 而是机器约定

1. 同样，__all__ 也是对于模块公开接口的一种约定，比起下划线，__all__ 提供了暴露接口用的”白名单“。
2. 一些不以下划线开头的变量（比如从其他地方 import 到当前模块的成员）可以同样被排除出去。

    import os
    import sys
    __all__ = ["process_xxx"]  # 排除了 `os` 和 `sys`

    def process_xxx():
        pass  # omit

### 控制 from xxx import * 的行为
用 from xxx import * 的写法的，就只会导入 `__all__` 列出的成员

### 为 lint 工具提供辅助
编写一个库的时候，经常会在 `__init__.py` 中暴露整个包的 API，而这些 API 的实现可能是在包中其他模块中定义的。如果我们仅仅这样写：

    from foo.bar import Spam, Egg

一些代码检查工具，如 pyflakes 就会报错，认为 Spam 和 Egg 是 import 了又没被使用的变量。当然一个可行的方法是把这个警告压掉：

    from foo.bar import Spam, Egg  # noqa

但是更好的方法是显式定义 __all__，这样代码检查工具会理解这层意思，就不再报 unused variables 的警告：

    from foo.bar import Spam, Egg
    __all__ = ["Spam", "Egg"]

### limit
1. `__all__` must be list
2. `__all__` must be static(not dynamic)
3. `__all__` must be under import
