# Preface
http://nvie.com/posts/iterators-vs-generators/

## Iterable, 迭代器,
str-dict 都是Iterable(同时有__iter__)
Iterable 可以生成Iterator:Iterator = iter(Iterable)
An iterable is *any object*, not necessarily a data structure, that can *return an iterator via iter*(with the purpose of returning all of its elements)

> NOTE:
*Often*, for pragmatic reasons, iterable classes will implement both `__iter__() and __next__()` in the same class, and have `__iter__()` return self, which makes the class both *an iterable and its own iterator*. It is perfectly fine to return a different object as the iterator, though. While `dict, str...`  only has `__iter__()`

> Iterable: type(obj)->__iter__(), 是否真的返回 Iterator, 不考虑
> Iterator: type(obj)->__iter__(), type(obj)->__next__() 同时定义,

	import collections
	class fib:
	    def __init__(self):
	        self.prev = 0
	        self.curr = 1
	    def __iter__(self):
	        return self

	    def __next__(self):
	        value = self.curr
	        self.curr += self.prev
	        self.prev = value
	        return value

	f=fib()

	## TypeError: 'type' object has no __iter__/__next__
	print(isinstance(fib, collections.Iterable));
	print(isinstance(fib, collections.Iterator));

	## TypeError: 'fib' object has __iter__/__next__
	print(isinstance(f, collections.Iterable));
	print(isinstance(f, collections.Iterator));
	print(next(f))
	print(next(f))
	print(next(f))
	print(next(f))
	print(next(f))
	for x in f: print(x);quit()

## Iterator
> Not all Iterator is generate from Iterable, but also `class <type>`
Any object that has a `__next__()` and `__iter__()` method is therefore an iterator(support `for and next statement`)
There are countless examples of iterators. All of the itertools functions return iterators. Some produce infinite sequences:

	>>> from itertools import count
	>>> counter = count(start=13)
	>>> next(counter)
	13
	>>> next(counter)
	14

Some produce infinite sequences from finite sequences:

	>>> from itertools import cycle
	>>> colors = cycle(['red', 'white', 'blue'])
	>>> next(colors)
	'red'
	>>> next(colors)
	'white'
	>>> next(colors)
	'blue'
	>>> next(colors)
	'red'

Some produce finite sequences from infinite sequences:

	>>> from itertools import islice
	>>> colors = cycle(['red', 'white', 'blue'])  # infinite
	>>> limited = islice(colors, 0, 4)            # finite
	>>> for x in limited:                         # so safe to use for-loop on
	...     print(x)
	red
	white
	blue
	red


## generator, 生成器
 A generator is a special kind of iterator—the elegant kind: 使用yield 代替了复杂的iter(), next()

- container: an object is a container when it can be asked whether it contains a certain element

You can perform such membership tests on lists, sets, or tuples alike:

	>>> assert 1 in [1, 2, 3]      # lists
	>>> assert 4 not in [1, 2, 3]
	>>> assert 1 in {1, 2, 3}      # sets
	>>> assert 4 not in {1, 2, 3}
	>>> assert 1 in (1, 2, 3)      # tuples
	>>> assert 4 not in (1, 2, 3)

Dict membership will check the keys:

	>>> d = {1: 'foo', 2: 'bar', 3: 'qux'}
	>>> assert 1 in d
	>>> assert 4 not in d
	>>> assert 'foo' not in d  # 'foo' is not a _key_ in the dict

Finally you can ask a string if it "contains" a substring:

	>>> s = 'foobar'
	>>> assert 'b' in s
	>>> assert 'x' not in s
	>>> assert 'foo' in s  # a string "contains" all its substrings

> Not all containers are necessarily iterable. An example of this is a Bloom filter. Probabilistic data structures like this can be asked whether they contain a *certain element*, but they are *unable to return their individual elements*.



## Iterable

	>>> horses = [1, 2, 3, 4]
	>>> races = itertools.permutations(horses)
	>>> print(races)
	<itertools.permutations object at 0xb754f1dc>
	>>> print(list(itertools.permutations(horses)))
	[(1, 2, 3, 4),
	 (1, 2, 4, 3),

# yield
it require for the `first send()` to be `None`
> You can't send() a value the first time because the generator did not execute until the point where you have the yield statement, so there is nothing to do with the value.(没有停在yield 语句)

## send next close
1. r = c.send(n); # 向generator yield 传值, 并启动generator 执行
1. r = next(c); # r = c.send(None)
2. c.close()

	def cc():
		n = 1
		while True:
			n += 1;
			if n>=4: return 'done'
			yield n

	c = cc()
	print(next(c))
	print(next(c))
	print(next(c))

next(c) 等价于c.send(None)

	2
	3
	Traceback (most recent call last):
	  File "a.py", line 11, in <module>
	    print(next(c))
	StopIteration: done


# generator, 生成器
通过列表生成式，直接创建一个大列表, 会受到内存限制。我们可以使用generator. 只需要将list `[]` 改成`()`

	g = (x * x for x in range(10))
	>>> next(g)
	0
	>>> next(g)
	1
	>>> next(g)
	4
	>> [for i in g]

list generator:

	>>> print(i for i in [1,2])
	<generator object <genexpr> at 0x10c7dd888>
	>>> ''.join(str(i) for i in [1,2])
	'12'
	>>> ''.join([str(i) for i in [1,2]])
	'12'

斐波拉契数列用列表生成式写不出来，但是，用函数把它打印出来却很容易：

	def fib(max):
		n, a, b = 0, 0, 1
		while n < max:
			print(b)
			a, b = b, a + b
			n = n + 1
		return 'done'

上面的函数可以输出斐波那契数列的前N个数, 改成generator 就是：

	def fib(max):
		n, a, b = 0, 0, 1
		while n < max:
			yield b
			a, b = b, a + b
			n = n + 1
		return 'done'
