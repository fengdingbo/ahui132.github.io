---
layout: page
title:
category: blog
description:
---
# Preface

pythone 一切皆对象

# Class and Object

	class MyStuff(object):
		name = 'hilo'
		print(name); # hilo

		def __init__(self):
			self.tangerine = "And now a thousand years between"

		def apple(self):
			print "I AM CLASSY APPLES!"

	obj = MyStuff();
	print(MyStuff.name); # hilo
	print(obj.name);     # hilo

## Inheritance

	class Parent(object):

		def altered(self):
			print "PARENT altered()"

	class Child(Parent):

		def altered(self):
			print "CHILD, BEFORE PARENT altered()"
			super().altered()

	Child().altered()

super inherits init

	class Child(Parent):
		def __init__(self, stuff):
			self.stuff = stuff
			super().__init__()

## __dict__ 属性的dict
属性分为：

1. 类属性(class attribute)
1. 对象属性(object attribute)。


	class bird(object):
		feather = True

	class chicken(bird):
		fly = False
		def __init__(self, age):
			self.age = age

	summer = chicken(2)

	print(bird.__dict__)
	print(chicken.__dict__)
	print(summer.__dict__)

output:

	{'__dict__': <attribute '__dict__' of 'bird' objects>, '__module__': '__main__', '__weakref__': <attribute '__weakref__' of 'bird' objects>, 'feather': True, '__doc__': None}
	{'fly': False, '__module__': '__main__', '__doc__': None, '__init__': <function __init__ at 0x2b91db476d70>}
	{'age': 2}

属性访问时，是层层遍历的: `summer|chicken|bird|object`, 所以:

	>>> print(summer.age)
	2
	>>> print(summer.fly)
	False
	>>> print(summer.feather)
	True
	>>> print(chicken.fly)
	False
	>>> print(chicken.feather)
	True

## vars: return dict
Return the `__dict__` attribute  for a module, class, instance, or *locals()*

	vars([object])

# attribute
方法也是属性

## dir list attribute

	>>> dir('ABC')
	['__add__', '__class__', '__contains__',...

## hasattr

	>>> hasattr(obj, 'y') # 有属性'y'吗？
	True
	>>> getattr(obj, 'y') # 获取属性'y'
	19
	>>> getattr(obj, 'y', 'default')

	 if hasattr(fp, 'read'):
        return readData(fp)

## __getattr__, __setattr__
`obj.attr` 是attr
`obj['key']` 是item

当访问不存在的attr 时触发`__getattr__`

	def __getattr__(self, attr):
		if attr=='score':
			return 99
		raise AttributeError('\'Student\' object has no attribute \'%s\'' % attr)

## __getitem__, __setitem__
for dict

## __call__
与php `__invoke()`一样，它是将对象变函数

	class Student(object):
		def __init__(self, name):
			self.name = name

		def __call__(self):
			print('My name is %s.' % self.name)

调用方式如下：

	>>> s = Student('Michael')
	>>> s() # self参数不要传入
	My name is Michael.

能被调用的对象就是一个Callable对象

	>>> callable(Student())
	True
	>>> callable(max)
	True

## 实例与类属性

	>>> class Student(object):
	...     name = 'Student'
	...
	>>> s = Student() # 创建实例s
	>>> print(s.name) # 打印name属性，因为实例并没有name属性，所以会继续查找class的name属性
	Student
	>>> print(Student.name) # 打印类的name属性
	Student

## 给实例绑定方法属性

	>>> def set_age(self, age): # 定义一个函数作为实例方法
	...     self.age = age
	...
	>>> from types import MethodType
	>>> s.set_age = MethodType(set_age, s) # 将方法set_age绑定到实例s
	>>> s.set_age(25) # 调用实例方法
	>>> s.age # 测试结果
	25

为了给所有实例都绑定方法，可以给class绑定方法：

	>>> def set_score(self, score):
	...     self.score = score
	...
	>>> Student.set_score = MethodType(set_score, Student)

## 使用__slots__ 限制添加属性
如果我们想要限制实例的属性怎么办？比如，只允许对Student实例添加name和age属性。

为了达到限制的目的，Python允许在定义class的时候，定义一个特殊的__slots__变量，来限制该class实例能添加的属性：

	class Student(object):
		__slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称

然后，我们试试：

	>>> s = Student() # 创建新的实例
	>>> s.age = 25 # 绑定属性'age'
	>>> s.score = 99 # 绑定属性'score'
	Traceback (most recent call last):
	  File "<stdin>", line 1, in <module>
	AttributeError: 'Student' object has no attribute 'score'

由于'score'没有被放到__slots__中，所以不能绑定score属性，试图绑定score将得到AttributeError的错误。

> 使用__slots__要注意，__slots__定义的属性仅对当前类实例起作用，对继承的子类是不起作用的

## @property 属性
Python内置的`@property`装饰器就是负责把一个方法变成属性调用的`Student().score=1`：

	class Student(object):

		@property
		def score(self):
			return self._score

		@score.setter
		def score(self, value):
			if not isinstance(value, int):
				raise ValueError('score must be an integer!')
			if value < 0 or value > 100:
				raise ValueError('score must between 0 ~ 100!')
			self._score = value

@property的实现比较复杂，我们先考察如何使用。把一个getter方法变成属性，只需要加上@property就可以了，此时，@property本身又创建了另一个装饰器@score.setter，负责把一个setter方法变成属性赋值. 如果没有`@score.setter`, 那就是只读属性`score` 了

## __str__
相当于js 的toString

	>>> class Student(object):
	...     def __init__(self, name):
	...         self.name = name
	...     def __str__(self):
	...         return 'Student object (name: %s)' % self.name
			__repr__ = __str__
	...
	>>> print(Student('Michael'))
	Student object (name: Michael)

`__str__()`返回用户看到的字符串(`print(obj)`)，
`__repr__()`返回程序开发者看到的字符串，也就是说，__repr__()是为调试服务的: 直接输入`obj`。

# item

## __iter__
如果一个类想被用于for ... in循环，类似list或tuple那样，就必须实现一个`__iter__()`方法，该方法返回一个迭代对象，
然后，Python的for循环就会不断调用该迭代对象的`__next__()`方法拿到循环的下一个值，直到遇到StopIteration错误时退出循环。

我们以斐波那契数列为例，写一个Fib类，可以作用于for循环：

	class Fib(object):
		def __init__(self):
			self.a, self.b = 0, 1 # 初始化两个计数器a，b

		def __iter__(self):
			return self # 实例本身就是迭代对象，故返回自己

		def __next__(self):
			self.a, self.b = self.b, self.a + self.b # 计算下一个值
			if self.a > 100000: # 退出循环的条件
				raise StopIteration();
			return self.a # 返回下一个值

## __getitem__
Fib实例虽然能作用于for循环，看起来和list有点像，但是，把它当成list来使用还是不行，比如，取第5个元素：

	>>> Fib()[5]
	Traceback (most recent call last):
	  File "<stdin>", line 1, in <module>
	TypeError: 'Fib' object does not support indexing

要表现得像list那样按照下标取出元素，需要实现__getitem__()方法：

	class Fib(object):
		def __getitem__(self, n):
			a, b = 1, 1
			for x in range(n):
				a, b = b, a + b
			return a

现在，就可以按下标访问数列的任意一项了：

	>>> f = Fib()
	>>> f[0]
	1

但是list有个神奇的切片方法：

	>>> list(range(100))[5:10]
	[5, 6, 7, 8, 9]

对于Fib却报错。原因是`__getitem__()`传入的参数可能是一个int，也可能是一个切片对象slice，所以要做判断：

	class Fib(object):
		def __getitem__(self, n):
			if isinstance(n, int): # n是索引
				a, b = 1, 1
				for x in range(n):
					a, b = b, a + b
				return a
			if isinstance(n, slice): # n是切片
				start = n.start
				stop = n.stop
				if start is None:
					start = 0
				a, b = 1, 1
				L = []
				for x in range(stop):
					if x >= start:
						L.append(a)
					a, b = b, a + b
				return L

> 与之对应的是__setitem__()方法，把对象视作list或dict来对集合赋值。最后，还有一个__delitem__()方法，用于删除某个元素。

# Multi paradigm，多范式
操作符其实是对象的方法,

	'abc' + 'xyz'
	'abc'.__add__('xyz')

	(1.8).__mul__(2.0)
	True.__or__(False)

Python的多范式依赖于Python对象中的特殊方法(special method, magic method):. Use `dir(1)` to list all magic method:

	> dir(1)
	 `__add__` `__init__`

内置函数也是映射magic method:

	len([1,2,3])
	[1,2,3].__len__()

list index

	li[3]
	li.__getitem__(3)

	li[3] = 0
	li.__setitem__(3, 0)
	{'a':1, 'b':2}.__delitem__('a')

function object

	class SampleMore(object):
    def __call__(self, a):
        return a + 5

	add = SampleMore()     # A function object
	print(add(2))          # Call function

# With, Context Manager, 上下文管理器

	f = open("new.txt", "w")
	print(f.closed)               # whether the file is open
	f.write("Hello World!")
	f.close()
	print(f.closed)

有了CM 后，当代码进入`with .. as f` 定义的环境时，调用`f.__enter()`, 离开时，自动调用`f.__exit__()` (f.close() 的多范式)

	with open("a.txt", "w") as f:
		print(f.closed)
		f.write("hello world!")

	print(f.closed)

可以查看到f 的magic method

	> dir(f)

# Enum(__members__)

	>>> from enum import Enum
	>>> Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))
	>>> for name, member in Month.__members__.items():
	...      print(name, '=>', member, ',', member.value)
	...
	Jan => Month.Jan , 1
	Feb => Month.Feb , 2

如果需要更精确地控制枚举类型，可以从Enum派生出自定义类：
@unique装饰器可以帮助我们检查保证没有重复值。


	from enum import Enum, unique

	@unique
	class Weekday(Enum):
		Sun = 0 # Sun的value被设定为0
		Mon = 1
		Tue = 2

多种访问方法：

	>>> day1 = Weekday.Mon
	>>> print(day1)
	Weekday.Mon
	>>> print(Weekday.Tue)
	Weekday.Tue
	>>> print(Weekday['Tue'])
	Weekday.Tue
	>>> print(Weekday.Tue.value)
	2
	>>> print(Weekday(1))
	Weekday.Mon

# type()
动态语言和静态语言最大的不同，就是函数和类的定义，不是编译时定义的，而是运行时动态创建的。

	class Hello(object):
		def hello(self, name='world'):
			print('Hello, %s.' % name)

用type 创建类(like `lambda`), `class Xxx...`来定义类 其实是调用`type()函数`

	>>> def fn(self, name='world'): # 先定义函数
	...     print('Hello, %s.' % name)
	...
	>>> Hello = type('Hello', (object,), dict(hello=fn)) # 创建Hello class
	>>> h = Hello()
	>>> h.hello()
	Hello, world.
	>>> print(type(Hello))
	<class 'type'>
	>>> print(type(h))
	<class '__main__.Hello'>

# metaclass 元类
metaclass允许你创建类或者修改类。换句话说，你可以把类看成是metaclass创建出来的“实例”。

metaclass，直译为元类，简单的解释就是： 先定义metaclass，就可以创建类，最后创建实例。

我们先看一个简单的例子，这个metaclass可以给我们自定义的MyList增加一个add方法：

	class ListMetaclass(type):
		def __new__(cls, name, bases, attrs):
			attrs['add'] = lambda self, value: self.append(value)
			return type.__new__(cls, name, bases, attrs)

__new__()方法接收到的参数依次是：

	当前准备创建的类的对象；
	类的名字；
	类继承的父类集合；
	类属性

有了ListMetaclass，我们在定义类的时候还要指示使用ListMetaclass来定制类，传入关键字参数metaclass：

	class MyList(list, metaclass=ListMetaclass):
		pass

测试一下MyList是否可以调用add()方法：

	>>> L = MyList()
	>>> L.add(1)
	>> L
	[1]

# MRO, Method Resolution Order
http://python-history.blogspot.com/2010/06/method-resolution-order.html
http://hanjianwei.com/2013/07/25/python-mro/

Python has three MRO:

1. classic class: DFS(Deep Search First) (<= 2.1)
2. new-style class: pre-computed `__mro__`when a class was defined(DFS+*保留最后一个重复*):(2.2)
3. C3 Agrithm(>=2.3)

## new-style mro

	class A(object): pass
	class B(object): pass
	class X(A, B): pass
	class Y(B, A): pass
	class Z(X, Y): pass

Using the tentative new MRO algorithm, the MRO for these classes would be *Z, X, Y, B, A, object*. (Here 'object' is the universal base class.)
However, I didn't like the fact that *B and A were in reversed order*.

上述继承关系违反了线性化的「 单调性原则 」。Michele Simionato对单调性的定义为：
> A MRO is monotonic when the following is true: if C1 precedes C2 in the linearization of C, then C1 precedes C2 in the linearization of any subclass of C. Otherwise, the innocuous(无意的) operation of deriving(起源, 产生) a new class could change the resolution order of methods, potentially introducing very subtle(细微, 不易察觉的) bugs.

## C3
Python should adopt the C3 Linearization algorithm described in the paper "A Monotonic Superclass Linearization for Dylan" (K. Barrett, et al, presented at OOPSLA'96).

我们把类 C 的线性化（MRO）记为 L[C] = [C1, C2,…,CN]。其中 C1 称为 L[C] 的头，其余元素 [C2,…,CN] 称为尾。如果一个类 C 继承自基类 B1、B2、……、BN，那么我们可以根据以下两步计算出 L[C]：

	L[object] = [object]
	L[C(B1…BN)] = [C] + merge(L[B1]…L[BN], [B1,…,BN])

这里的关键在于 merge，其输入是一组列表，按照如下方式输出一个列表：

	检查第一个列表的头元素（如 L[B1] 的头），记作 H。
	若 H *出现* 在其它列表的*头部*(或者其它列表没有)则将其输出，并将其从所有列表中删除，然后回到步骤1；如果出现在其它列表的*非头部*, 则违反 Monotonic;
	重复上述步骤，直至列表为空

我们看看 C 的线性化结果：

	L[C] = [C] + merge(L[A], L[B], [A], [B])
	     = [C] + merge([A, X, Y, object], [B, Y, X, object], [A, B])
	     = [C, A] + merge([X, Y, object], [B, Y, X, object], [B])
	     = [C, A, B] + merge([X, Y, object], [Y, X, object])

X 是其它列表的非头部(乱序了), 无法构建继承关系

	class First(object):
	    def __init__(self):
	        print("first")

	class Second(First):
	    def __init__(self):
	        print("second")

	class Third(First, Second):
	    def __init__(self):
	        print("third")

	L(Third) = [Third] + merge([1, O], [2, 1, O], [1, 2])
	1 is non-head of `[1, 0]`
	2 is non-head of `[1, 2]`

You'll get:

	TypeError: Cannot create a consistent method resolution
	order (MRO) for bases Second, First

# super
https://www.zhihu.com/question/20040039

super 是用来*执行* 继承类的init 的

	super(A, self).__init__()
	super().__init__()
	def super(cls, inst):
	    mro = inst.__class__.mro()
	    return mro[mro.index(cls) + 1]; # return parent

在 MRO(Method Resolution Order) 中，见`__mro__`。

	class Base(object):
		def __init__(self, *args, **kwargs): pass;

	class A(Base):
		def __init__(self, *args, **kwargs):
			print("A")
			super().__init__(*args, **kwargs)

	class B(Base):
		def __init__(self, *args, **kwargs):
			print("B")
			print(self.__mro__); # E.__mro__
			super().__init__(*args, **kwargs)

	class C(A):
		def __init__(self, arg, *args, **kwargs):
			print("C","arg=",arg)
			super().__init__(arg, *args, **kwargs)

	class D(B):
		def __init__(self, arg, *args, **kwargs):
			print("D", "arg=",arg)
			super().__init__(arg, *args, **kwargs)

	class E(C,D):
		def __init__(self, arg, *args, **kwargs):
			print("E", "arg=",arg)
			super().__init__(arg, *args, **kwargs)

	print(E.__mro__)
	print("MRO:", [x.__name__ for x in E.__mro__])
	E(10)

result:

	(<class '__main__.E'>, <class '__main__.C'>, <class '__main__.A'>, <class '__main__.D'>, <class '__main__.B'>, <class '__main__.Base'>, <class 'object'>)

	MRO: ['E', 'C', 'A', 'D', 'B', 'Base', 'object']
	E arg= 10
	C arg= 10
	A
	D arg= 10
	B

# todo
> http://python.jobbole.com/82622/
