# singleton
http://stackoverflow.com/questions/6760685/creating-a-singleton-in-python

## via decorator

    def singleton(cls):
        instance = cls()
        instance.__call__ = lambda: instance
        return instance

Sample use

    @singleton
    class Highlander:
        x = 100
        # Of course you can have any attributes or methods you like.


    highlander = Highlander()
    another_highlander = Highlander()
    assert id(highlander) == id(another_highlander)

如果你希望只在需要的时候创建类的实例对象也有别的方法：

    def singleton(cls, *args, **kw):
        instances = {}
        def _singleton():
            if cls not in instances:
                instances[cls] = cls(*args, **kw)
            return instances[cls]
        return _singleton

    @singleton
    class MyClass(object):
        a = 1
        def __init__(self, x=0):
            self.x = x

    one = MyClass()
    two = MyClass()

    assert id(one) == id(two)

## via baseclass

    class Singleton(object):
      _instance = None
      def __new__(class_, *args, **kwargs):
        if not isinstance(class_._instance, class_):
            class_._instance = object.__new__(class_, *args, **kwargs)
        return class_._instance

    class MyClass(Singleton, BaseClass):
      pass

## via metaclass

    class Singleton(type):
        def __init__(cls, name, bases, dict):
            super(Singleton, cls).__init__(name, bases, dict)
            cls._instance = None
        def __call__(cls, *args, **kw):
            if cls._instance is None:
                cls._instance = super(Singleton, cls).__call__(*args, **kw)
            return cls._instance

    class MyClass(object):
        __metaclass__ = Singleton

    one = MyClass()
    two = MyClass()
