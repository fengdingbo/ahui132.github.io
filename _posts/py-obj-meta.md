# type vs object

type 即代表class 本身, 他继承: type-object,

    >>> print(type(object));
    <class 'type'>

作为object, baseclass 是type

    >>> object.__class__.__mro__
    (<class 'type'>, <class 'object'>)
    >>> type.__class__.__mro__
    (<class 'type'>, <class 'object'>)

作为type, most base type is object

    >>> type.__mro__
    (<class 'type'>, <class 'object'>)
    >>> object.__mro__
    (<class 'object'>,)

object 即代表对象, 也代表most base type

    >>> issubclass(type, object)
    True
    >>> issubclass(object, type)
    False
    >>> isinstance(type, object)
    True
    >>> isinstance( object, type)
    True

self is an object, class is type

    class Base(object):
        def __init__(self, val):
            self.val = val

        @classmethod
        def make_obj(cls, val):
            return cls(val+1)

    class Derived(Base):
        def __init__(self, val):
            # In this super call, the second argument "self" is an object. <object Derived>
            # The result acts like an object of the Base class.
            super(Derived, self).__init__(val+2)

        @classmethod
        def make_obj(cls, val):
            # In this super call, the second argument "cls" is a type. <class Derived>
            # The result acts like the Base class itself.
            return super(Derived, cls).make_obj(val)

Test output:

    >>> b1 = Base(0)
    >>> b1.val
    0
    >>> b2 = Base.make_obj(0)
    >>> b2.val
    1
    >>> d1 = Derived(0)
    >>> d1.val
    2
    >>> d2 = Derived.make_obj(0)
    >>> d2.val
    3

# new init

    class ListMetaclass(type):
        def __init__(*args, **kw):
            print(isinstance(args[0], ListMetaclass))
        def __new__(cls, name, bases, attrs):


关于类相关的static method/var执行顺序:

    define:
        decorator
        static property
        metaclass: 因为涉及到__new__, 一般继继承type.__new__, 或者自己定义__new__
            __new__(ListMetaclass/Singleton, name, bases, attrs)
            __init__(Myclass, name, bases, attrs) (if return of new(cls) is instance of cls)
                Myclass.__class__ == ListMetaclass/Singleton, type, object
    instance MyClass():
        metaclass:
            __call__(cls, *args, **kw)
        non-metaclass: super(Singleton, cls).__call__(*args)
            __new__(cls, *args, **kw)
            __init__(*args, **kw) (if return of new(cls) is instance of cls)

## __new__
> https://docs.python.org/3/reference/datamodel.html#object.__new__

__new__ 的第一个参数是这个类:

1. 如果定义新式类时没有重新定义`__new__()`时，Python默认是调用该类的直接父类的`__new__()`方法来构造该类的实例，
3. 不可以在`Foo.__new__`中 再调用`Foo.__new`, 会死循环, 必须是父类, 或者其它类
4. cls 是要实例化的类, 只决定实例化的名字
5. If `__new__()` does not return an instance of cls, then the new instance’s `__init__()` method will not be invoked.

本质如下

    class.__new__(cls[, ...])
        cls, 代表要实例化类, class 只负责提供__new__ 而且:
            1. cls must be subtype of class;
            2. 2. cls.__new__ and class.__new__ 形参必须相同
    Foo(*args, **kw) 会调用 __call
        obj = c_class.__new__(cls, *args, **kw)
        if instanceof(obj, c_class):
            obj.__init__(obj, *args, **kw)
        return obj;

```
class inch(float):
    def __new__(cls, arg=0.0):
        print(cls)
        obj = float.__new__(cls, arg*0.0254); inch is subtype of float
        print(type(obj), obj)
        return obj
    def __init__(self, a):
        print('init:', a);

i=inch(2)
print(i)
----------
output:

    <class '__main__.inch'>
    <class '__main__.inch'> 0.0508
    init: 2
    0.0508

instance procedure:

    obj = cls.__new__(cls, *arg, **kw):
    obj.init(*arg, **kw)
```

new 将Foo实例, 替换成Stranger 实例

    class Foo(object):
        def __init__(self, *args, **kwargs):
            print('Foo init')
        def __new__(cls, *args, **kwargs):
            print(cls)
            obj=object.__new__(Stranger, *args, **kwargs);
            # obj.init(*args, **kwargs)
            print(type(obj))
            return obj
    class Stranger(object):
        def __init__(self, *args, **kwargs):
            print('Stranger init')
    foo = Foo()

output:

    <class '__main__.Foo'> # cls 指的是Foo类
    <class '__main__.Stranger'> # obj 指的是Stranger的 实例


## type
动态语言和静态语言最大的不同，就是函数和类的定义，不是编译时定义的，而是运行时动态创建的。

	class Hello(object):
		def hello(self, name='world'):
			print('Hello, %s.' % name)

用type 创建类(like `lambda`), `class Xxx...`来定义类 其实是调用`type()函数`

	>>> def fn(self, name='world'): # 先定义函数
	...     print('Hello, %s.' % name)
	...
	>>> Hello = type('Hello', (object,), dict(hello=fn)) # 创建Hello class
            new_class = type.__new__(cls, 'class_name<优化级小于qualname>', (object,), {'__qualname__': 'custom_class_name'})
                qualname 只是别名, 不影响: isinstance(new_class, cls) == true
	>>> h = Hello()
	>>> h.hello()
	Hello, world.
	>>> print(type(Hello))
	<class 'type'>
	>>> print(type(h))
	<class '__main__.Hello'>


## metaclass 元类
metaclass允许你创建类或者修改类: 先定义metaclass，就可以创建/修改类，最后创建实例。

### metaclass conflict
you muse use `Singleton(type)`

1. type.__new__(cls) is safe than object.__new__,
2. so cls Singleton must be subclass of type


    class Singleton(object):
        def __init__(cls, name, bases, dict):
            print('init: cls',cls);
            super(Singleton, cls).__init__(name, bases, dict)
            cls._instance = None

        def __new__(cls, name, bases, attrs):
            print('------Singleton: __new__', cls, bases, attrs)
            n_cls = type.__new__(cls, name, bases, attrs)
            n_cls._instance = None
            return n_cls
        def __init__(self, *args):
            print({'init_init:':args})
        def __call__(cls, *args, **kw):
            print('call: cls',cls, args);
            if cls._instance is None:
                cls._instance = super(Singleton, cls).__call__('abc', (object,), {})
            return cls._instance

    print('------define MyClass--------')
    class MyClass(type, metaclass=Singleton):
        def __init__(self, *args):
            print({'init_init:':args})
        def __new__(cls, name, bases, attrs):
            print('------instance: __new__', cls, bases, attrs)
            n_cls = type.__new__(cls, name, bases, attrs)
            return n_cls

    print('------MyClass()--------')
    a=MyClass(22)
    print('\n\n------MyClass2()--------')
    b=MyClass(2)

    class MyClass(type, metaclass=Singleton):
    TypeError: metaclass conflict: the metaclass of a derived class must be a (non-strict) subclass of the metaclasses of all its bases

http://www.phyast.pitt.edu/~micheles/python/metatype.html

### metalclass.new
我们先看一个简单的例子，这个metaclass可以给我们自定义的MyList增加一个add方法：

```
class ListMetaclass(type):
    def __new__(cls, name, bases, attrs):
        print('cls:\t', cls);
        print('name:\t',  name);
        print('bases:\t', bases);
        print('attrs:\t', attrs);
        attrs['add'] = lambda self, value: self.append(value)
        new_cls = type.__new__(cls, name, bases, attrs)
        print(new_cls)
        print('---------')
        return new_cls;

print('---define MyList---')
class MyList(list, metaclass=ListMetaclass):
    print('--execute static')
    a=1
    _b=2
    def _p(self):
        print('test');

print(MyList.__dict__.keys())
print('\n---L=MyList()---')
L=MyList()
L.add(1)
print(L); # [1]
print(type(L)); # class MyList
```

__new__()方法接收到的参数依次是：

    ---define MyList---
    --execute static
    cls:	 <class '__main__.ListMetaclass'> 没啥用, 不过必须是subtype of xxx
    name:	 MyList
    bases:	 (<class 'list'>,)
    attrs:	 {'__module__': '__main__', '__qualname__': 'MyList', 'a': 1, '_p': <function MyList._p at 0x1037fc8c8>, '_b': 2}
    <class '__main__.MyList'>
    ---------
    dict_keys(['__dict__', '__module__', 'add', 'a', '_b', '__weakref__', '__doc__', '_p'])

    ---L=MyList()---
    [1]
    <class '__main__.MyList'>

##  __del__, 垃圾回收
`__del__` 就是析构器。它不实现语句 del x (不会翻译为 x.__del__() )。

1. 它定义的是当一个对象进行垃圾回收时候的行为。
2. 当一个对象在删除的时需要更多的清洁工作的时候此方法会很有用，比如套接字对象或者是文件对象。
3. 注意，如果解释器退出的时候对象还存存在，就不能保证 __del__ 能够被执行

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
