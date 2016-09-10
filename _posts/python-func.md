---
layout: page
title:
category: blog
description:
---
# Preface

函数式编程

# function
var 默认:

- outside-exist (global)
- non-exists (local)

强制global:

	def func(s1,s2=None):
		global X;		#放在使用前
		print global_var
		func.count++
		local_var=4
		retrun s1,s2

	s1,s2, _ = func("-%s-" % 'Hello','-%s-' % 'Hilo' 'jack')
	print s1,s2

output:

	-Hello- -Hilojack-

## bind

	import functools
	nday = functools.partial(func,param1, params2...)

## static var

	def foo():
		foo.counter += 1
		print "Counter is %d" % foo.counter
	foo.counter = 0

or:

	def foo():
		try:
			foo.counter += 1
		except AttributeError:
			foo.counter = 1

终极(see decorator):

	def static_vars(**kwargs):
        def decorate(func):
            for k,v in kwargs.items():
                setattr(func, k, v)
            return func
        return decorate; 函数定义后执行

	@static_vars(counter=0)
	def foo():
        print("Counter is %d" % foo.counter)
        foo.counter += 1


## tuple args
可变参数 定义：

	def func(*tuple_args):
		s1,s2 = tuple_args

使用

	>>> nums = [1, 2, 3]
	>>> nums = (1, 2, 3)
	>>> calc(nums[0], nums[1], nums[2])
	>>> calc(*nums)

## keyword args
关键字参数

	def person(name, age, **kw):
		print('name:', name, 'age:', age, 'other:', kw)

	>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
	>>> person('Jack', 24, city=extra['city'], job=extra['job'])
	name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}

当然，上面复杂的调用可以用简化的写法：

	>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
	>>> person('Jack', 24, **extra)
	name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}

## named args
如果要限制关键字参数的名字，就可以用命名关键字参数，例如，只接收city和job作为关键字参数。这种方式定义的函数如下：

	def person(name, age, *, city='bj', job):
		print(name, age, city, job)

`*`后面的参数被视为命名关键字参数, 传参时必须带参数名
`*`前面的参数可带可不带参数名

	>>> person('Jack', 24, job='Engineer')

## 各种参数
定义一个函数，包含上述若干种参数：

	def f1(a, b, c=0, *args, **kw):
		print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

	def f2(a, b, c=0, *, d, **kw):
		print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)

# lambda function

	func = lambda x, y: x + y
	func = (lambda x, y: x + y)
	func(1,2)

lambda 不能显式使用return :

	# error
	func = lambda x, y: return x + y

## 闭包返回函数的绑定变量
我们来看一个例子：

	def count():
		fs = []
		for i in range(1, 4):
			def f():
				 return i*i
			fs.append(f)
		return fs

	f1, f2, f3 = count()

在上面的例子中，每次循环，都创建了一个新的函数，然后，把创建的3个函数都返回了。

你可能认为调用f1()，f2()和f3()结果应该是1，4，9，但实际结果是：

	>>> f1()
	9
	>>> f2()
	9
	>>> f3()
	9

全部都是9！原因就在于返回的函数引用了变量i，但它并非立刻执行。等到3个函数都返回时，它们所引用的变量i已经变成了3，因此最终结果为9。

返回闭包时牢记的一点就是：返回函数*不要引用任何循环变量，或者后续会发生变化的变量*。

如果一定要引用循环变量怎么办？方法是*再创建一个函数，用该函数的参数绑定循环变量当前的值*，无论该循环变量后续如何更改，已绑定到函数参数的值不变：

	def count():
		def f(j):
			def g():
				return j*j
			return g
		fs = []
		for i in range(1, 4):
			fs.append(f(i)) # f(i)立刻被执行，因此i的当前值被传入f()
		return fs


# map reduce
http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014317852443934a86aa5bb5ea47fbbd5f35282b331335000

Python内建了map-reduce/filter/all/any 实现函数式编程

## map
我们先看map。map()函数接收两个参数，一个是函数，一个是Iterable，map将传入的函数依次作用到序列的每个元素，并把结果作为新的Iterator返回。

	>>> def f(x):
	...     return x * x
	...
	>>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])

	# map 针对一个序列
	print map(lambda x: x*2, [4, 5, 6])

	# map 针对多个序列
	print map(lambda x, y: x + y, [1, 2, 3], [4, 5, 6])

map()传入的第一个参数是f，即函数对象本身。由于结果r是一个Iterator，Iterator是惰性序列，因此通过list()函数让它把整个序列都计算出来并返回一个list。

	>>> list(map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
	['1', '2', '3', '4', '5', '6', '7', '8', '9']

## zip

	>>> list(zip([4, 5, 6], [5,6,7]))
	[(4, 5), (5, 6), (6, 7)]

## reduce
If initial is present, it is placed before the items of the sequence in the calculation

    reduce(function, sequence[, initial]) -> value
    reduce(lambda x, y: x+y, [1, 2, 3, 4, 5]) calculates
    ((((1+2)+3)+4)+5).

比方说对一个序列求和，就可以用reduce实现：

	>>> from functools import reduce
	>>> def add(x, y):
	...     return x + y
	...
	>>> reduce(add, [1, 3, 5, 7, 9])

我来利用map+reduce 实现 str2int的函数就是：

	from functools import reduce

	def str2int(s):
		def fn(x, y):
			return x * 10 + y
		def char2num(s):
			return {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}[s]
		return reduce(fn, map(char2num, s))

还可以用lambda函数进一步简化成：

	from functools import reduce
	def char2num(s):
		return {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}[s]

	def str2int(s):
		return reduce(lambda x, y: x * 10 + y, map(char2num, s))

## filter
filter 返回的也是惰性Iterator

	list(filter(lambda x:x%2==0, [1,2,3]));
	>>> list(filter(lambda x:x%2==0, range(10)))
	>>> list(filter(lambda x:x%2==0, range(0,10)))
	[0, 2, 4, 6, 8]

例如获取100以内的奇数：

	filter(lambda n: (n%2) == 1, range(100))
	[i for i in range(100) if i%2 == 1]


### 用filter求prime素数
计算素数的一个方法是埃氏筛法， 用Python来实现这个算法，可以先构造一个从3开始的奇数序列：

	def _odd_iter():
		n = 1
		while True:
			n = n + 2
			yield n

最后，定义一个生成器，不断返回下一个素数：

	def primes():
		yield 2
		it = _odd_iter() # 初始序列
		while True:
			n = next(it) # 返回序列的第一个数
			yield n
			it = filter(lambda x:x%n>0, it) # 构造新序列

打印1000以内的素数:

	for n in primes():
		if n < 1000:
			print(n)
		else:
			break

### 双数生成器

	filter(lambda n:str(n)[::-1]==str(n), range(10,99))
    11,22,33,...

## all

    all(iterable, /)
        Return True if bool(x) is True for all values x in the iterable.

    If the iterable is empty, return True.

## any

	if needle.endswith('ly') or needle.endswith('ed') or
		needle.endswith('ing') or needle.endswith('ers'):
		print('Is valid')
	else:
		print('Invalid')

改成:

	if any([needle.endswith(e) for e in ('ly', 'ed', 'ing', 'ers')]):
		print('Is valid')
	else:
		print('Invalid')

Syntax:

	any([ expression(e) for e in (....)])
	any([True, False, False]);//True

列表解析:

	[ expression(e) for e in (....)])
	[ expression(e) for e in (....) if <condition>])


# sorted
ython内置的sorted()函数就可以对list进行排序：

	>>> sorted([36, 5, -12, 9, -21])
	[-21, -12, 5, 9, 36]

此外，sorted()函数也是一个高阶函数，它还可以接收一个key函数来实现自定义的排序，例如按绝对值大小排序：

	>>> sorted([36, 5, -12, 9, -21], key=abs)
	[5, 9, -12, -21, 36]

现忽略大小写的排序：

	>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower)
	['about', 'bob', 'Credit', 'Zoo']

要进行反向排序，不必改动key函数，可以传入第三个参数reverse=True：

	>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reverse=True)
	['Zoo', 'Credit', 'bob', 'about']

# decorator, 装饰器
由于函数也是一个对象，而且函数对象可以被赋值给变量，所以，通过变量也能调用该函数。

	>>> def now():
	...     print('2015-3-25')
	...
	>>> f = now
	>>> f()
	2015-3-25

函数对象有一个__name__属性，可以拿到函数的名字：

	>>> now.__name__
	'now'
	>>> f.__name__
	'now'

现在，假设我们要增强now()函数的功能，比如，在函数调用前后自动打印日志，但又不希望修改now()函数的定义，这种在代码运行期间动态增加功能的方式，称之为“装饰器”（Decorator）。

本质上，decorator就是一个返回函数的高阶函数。所以，我们要定义一个能打印日志的decorator，可以定义如下：

	def log(func):
		def wrapper(*args, **kw):
			print('call %s():' % func.__name__)
			return func(*args, **kw)
		return wrapper

我们要借助Python的@语法，把decorator置于函数的定义处：

	@log
	def now():
		print('2015-3-25')

调用now()函数，不仅会运行now()函数本身，还会在运行now()函数前打印一行日志：

	>>> now()
	call now():
	2015-3-25

如果decorator本身需要传入参数，那就需要编写一个返回decorator的高阶函数，写出来会更复杂。比如，要自定义log的文本：

	def log(text):
		def decorator(func):
			def wrapper(*args, **kw):
				print('%s %s():' % (text, func.__name__))
				return func(*args, **kw)
			return wrapper
		return decorator

这个3层嵌套的 decorator 用法如下：

	@log('execute')
	def now():
		print('2015-3-25')

	log('excute')(now)
	print(now.__name__)

## __name__
经过decorator装饰之后的函数，它们的__name__已经从原来的'now'变成了'wrapper'：

	>>> now.__name__
	'wrapper'

因为返回的那个wrapper()函数名字就是'wrapper'，所以，需要把原始函数的__name__等属性复制到wrapper()函数中，否则，有些依赖函数签名的代码执行就会出错。

不需要编写`wrapper.__name__ = func.__name__`这样的代码，Python内置的`functools.wraps`就是干这个事的，所以，一个完整的decorator的写法如下：

	import functools

	def log(func):
		@functools.wraps(func)
		def wrapper(*args, **kw):
			print('call %s():' % func.__name__)
			return func(*args, **kw)
		return wrapper

或者针对带参数的decorator：

	import functools

	def log(text):
		def decorator(func):
			@functools.wraps(func)
			def wrapper(*args, **kw):
				print('%s %s():' % (text, func.__name__))
				return func(*args, **kw)
			return wrapper
		return decorator

# Partial function,偏函数
用于定制函数

	def int2(x, base=2):
		return int(x, base)

functools.partial就是帮助我们创建一个偏函数的，不需要我们自己定义int2()，可以直接使用下面的代码创建一个新的函数int2：

	>>> import functools
	>>> int2 = functools.partial(int, base=2)
	>>> int2('1000000')
	64
	>>> int2('1010101')
	85

最后，创建偏函数时，实际上可以接收函数对象、*args和**kw这3个参数，当传入：

	max2 = functools.partial(max, 10)

实际上会把10作为*args的一部分自动加到左边，也就是：

	max2(5, 6, 7)

相当于：

	args = (10, 5, 6, 7)
	max(*args)
