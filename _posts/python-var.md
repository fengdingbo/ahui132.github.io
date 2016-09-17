---
layout: page
title:
category: blog
description:
---
# Preface

# const

	from inspect import currentframe, getframeinfo
	__name__ == '__main__'; # module name
		在模块 “foo.bar.my_module” 中调用 logger.getLogger(__name__) 等价于调用logger.getLogger(“foo.bar.my_module”)
	__file__ == 'path_to_current_file'
	frameinfo = getframeinfo(currentframe())
	print frameinfo.filename, frameinfo.lineno


# variable
To check the existence of a local variable:

	if 'myVar' in locals():
	  # myVar exists.

To check the existence of a global variable:

	if 'myVar' in globals():
	  # myVar exists.

## reference
list, tuple, dict 都是引用型的，无论是赋值，还是func 传值, 还是线程`threading.Thread(target=run_thread, args=(list,))`

# Data Type
数据类型

- String
- List( Array)
- None

Example

	type(range(1)) is list
	type(9.0) is float

Convert data type:

	int('07')
	float(9)
	str(9)

## type

	>>> type(fn)==types.FunctionType
	True
	>>> type(className); types.type
	>>> type(obj); class className
	>>> type(abs)==types.BuiltinFunctionType
	True
	>>> type(lambda x: x)==types.LambdaType
	True
	>>> type((x for x in range(10)))==types.GeneratorType
	True

## isinstance
也可用来判断数据类型

	isinstance('abc', str); # True
	isinstance(b'abc', bytes); # True
	isinstance(1, int); # True
	isinstance(1, (int, str)); # True
	isinstance('abc', Iterable); # True
	isinstance([1, 2, 3], (list, tuple))

	issubclass(obj.__class__, (list, tuple))
	issubclass(list, (list, tuple)); # true

# boolean
False

	if {} or None or []

True

	if not {}

# set

	myset = {'x', 1, 'y', 2, 2,100}
	>>> x = set('spam')
	>>> y = set(['h','a','m'])

	>>> x, y
	(set(['a', 'p', 's', 'm']), set(['a', 'h', 'm']))

## add set

	s.add(x)
	s.update([x1,x2])

## issubset(t)

	s.issubset(t)
	s <= t
	测试是否 s 中的每一个元素都在 t 中

	s.issuperset(t)
	s >= t
	测试是否 t 中的每一个元素都在 s 中

## oper set

	s.union(t)
	s | t
	返回一个新的 set 包含 s 和 t 中的每一个元素

	s.intersection(t)
	s & t
	返回一个新的 set 包含 s 和 t 中的公共元素

	s.difference(t)
	s - t
	返回一个新的 set 包含 s 中有但是 t 中没有的元素

	s.symmetric_difference(t)
	s ^ t
	返回一个新的 set 包含 s 和 t 中不重复的元素

	s.copy()
	返回 set “s”的一个浅复制

### multi operator

	s.update(t)
	s |= t
	返回增加了 set “t”中元素后的 set “s”

	s.intersection_update(t)
	s &= t
	....

## loop set

	if x in set
	for x in set
	len(set)

# Dict

	dic = {'x': 1, 'y': 2, 2:100}
	del dict['x']
	dict.setdefault(1, 123)

## get value

    dic.get(key)
    dic[key]

## define dict

	dic = dict(key1=1)
	dic = {'key1':1, 1:123}

	dic[1]=123
	dic.setdefault(1, 123)

## merge dict
python >=3.5

	z = {**x, **y}

or

	def merge_dicts(*dict_args):
	    result = {}
	    for dictionary in dict_args:
	        result.update(dictionary)
	    return result
	merge_dicts(x,y)

## defaultdict
dict subclass that calls a factory function to supply missing values。

	from collections import defaultdict, namedtuple
	d = collections.defaultdict(list)
	d['miss_key'] = 'hi'
	d['list_key'].append(1);


## has_key

	if key in dict:

    ## for obj only
	if hasattr(obj, 'attribute'):
		# obj.attr_name exists.

## dict to object

	class Dict2Obj(object):
		def __init__(self, initial_data):
			for key in initial_data:
				setattr(self, key, initial_data[key])

## access dict

	dict['x']
	dict.get('x', 'not found')
	dict.items()
		[('x', 1), ('y', 2)]

## dict keys and values

	dict.keys();//keys list
	dict.values();//values list

delete key

	dict.pop(key)

## foreach dict

	for k in dict:
		print "%s : %d" % (k,dict[k])

	for k,v in dict.items():
		print "%s : %d" % (k,v)

	>>>for k,v in {'k1':'v1','k2':'v2'}.items(): print k,v;
	...
	k2 v2
	k1 v1
	>>> for v in {'k1':'v1','k2':'v2'}.items(): print v;
	...
	('k2', 'v2')
	('k1', 'v1')

### dict iterms vs

	//list
	dict.items()
	//gnenerator object itemview
	dict.items()

用six : Six is a Python 2 and 3 compatibility library.

	from six import iteritems
	for k,v in iteritems(dict):


### list and enumerate

	>>> for k,v in [['k1','v1'],['k2','v2']]: print k,v
	...
	k1 v1
	k2 v2
	>>> for k,v in [('k1','v1'),('k2','v2')]: print k,v
	...
	k1 v1
	k2 v2

	>>> for k,v in enumerate([['k1','v1'],['k2','v2']]): print k,v
	...
	0 ['k1', 'v1']
	1 ['k2', 'v2']

# set
set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。

要创建一个set，需要提供一个list作为输入集合：

	>>> s = set([1, 2, 3])
	>>> s
	{1, 2, 3}

注意，传入的参数[1, 2, 3]是一个list，而显示的{1, 2, 3}只是告诉你这个set内部有1，2，3这3个元素，显示的顺序也不表示set是有序的。。

重复元素在set中自动被过滤：

	>>> s = set([1, 1, 2, 2, 3, 3])
	>>> s
	{1, 2, 3}

通过add(key)方法可以添加元素到set中，可以重复添加，但不会有效果：

	>>> s.add(4)
	>>> s
	{1, 2, 3, 4}
	>>> s.add(4)
	>>> s
	{1, 2, 3, 4}

通过remove(key)方法可以删除元素：

	>>> s.remove(4)
	>>> s
	{1, 2, 3}

set可以看成数学意义上的无序和无重复元素的集合，因此，两个set可以做数学意义上的交集、并集等操作：

	>>> s1 = set([1, 2, 3])
	>>> s2 = set([2, 3, 4])
	>>> s1 & s2
	{2, 3}
	>>> s1 | s2
	{1, 2, 3, 4}

set和dict的唯一区别仅在于没有存储对应的value，但是，set的原理和dict一样

# list and tuple
因为tuple不可变，所以代码更安全。如果可能，能用tuple代替list就尽量用tuple。

	list = [1,2]
	list +=[3,]

	tuple = (1,2)
	tuple +=(3,)

	sorted(['a', 'z', 'g']).pop(-1); # last
	sorted(['a', 'z', 'g']).pop(); # last
	sorted('azg']).pop(0); # first

> For more details, refer to `pydoc list`

The difference between list and tuple:

	1. tuple can be dictionary identifier key
	{(1,2):1} ok
	{[1,2]:1} error

	2. Tuples are immutable, and usually contain an heterogeneous sequence ..., and list is sorted

	3. tuple can not be accessed by index, so do deleted
	tuple[0] error
	list[0]	ok

	del tuple[0] error

## list merge

	[1,2] + [2,3,]
	(1,) + (2,)

## list join

	list1 = [1, 2, 3]
	str1 = ''.join(str(e) for e in list1)

## list list

	>>> list([1,2,3])
	[1, 2, 3]
	>>> list({'a':1,'b':2,'c':3})
	['b', 'c', 'a']

## loop list

	for idx, val in enumerate(ints):
		print(idx, val)
	for idx, val in enumerate(ints, start=5):
		print(idx, val)

## in list

	if x in list
	if index < len(list)

## zip list

	>>> xl = [1,3,5]
	>>> yl = [9,12,13]
	>>> print zip(xl,yl)
	[(1, 9), (3, 12), (5, 13)]

## join and split

	','.join([1,2])
	'1,2'.split(',');
	'1,2,3'.split(',',1);# 只切割一次

## shuffle list

	import random
	list=[1,2,3]
	random.shuffle(list)

## Access List

	list[0]
	[1,2][0]
	[1,2][-1]

## slice list
exclude end

	list[start:end:step]
	list[0:3]
	list[:-1]
	list[::-1] # reverse
	list[::5]
	print list[0:10:2]

	len = len(list)
	list[0:len]

## in array

	>>> 0 in range(1)
	True
	>>> x in range(1, 10)
	>>> 'hilo' in 'hilojack'

## print

	print list
	print '%r' % list

## Common list Function


	.index(value, [start, [stop]])
	.count(value) -> integer -- return number of occurrences of value
	.reverse() -> reverse *IN PLACE*
	.sort(cmp=None, key=None, reverse=False) -- stable sort *IN PLACE*;

remove and insert(in place)

	.insert(index, object)
	.remove(value) -- remove first occurrence of value.

## bisect
用于插入有充数组

	import bisect
	l = [3,1,9]
	l.sort()
	bisect.insort(l, 2)

## range:

	>>> print range(4)
	[0, 1, 2, 3]
	>>> print range(2,4)
	[2, 3]

## pop,append(push)

	>>> list = [1,2]
	del list[1]
	>>> list.append(3)
	>>> list.pop(-1) # same as list.pop()
	3
	>>> list.pop(0)
	1

## unpack list

	>>> print argv
	[1, 2]
	>>> a,b = [1, 2]

	>>> print aa()
	(1, 2)
	>>> a,b = aa()

# Number

## math

	int('1')
	float('1')
	round(1.7333)
	range(1,10 [,step])

## random

	import random
	random.randint(3,8)

## math operator

	print x**2; # x^2

## isNumber
For int use this:

	>>> "1221323".isdigit()
	True

But for float we need some tricks ;-). Every float number has one point...

	>>> "12.34".isdigit()
	False
	>>> "12.34".replace('.','',1).isdigit()
	True
	>>> "12.3.4".replace('.','',1).isdigit()
	False

Also for negative numbers just add lstrip():

	>>> '-12'.lstrip('-')
	'12'
