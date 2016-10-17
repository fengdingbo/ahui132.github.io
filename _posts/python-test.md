# pytest

## test case
文件名以及测试函数必须用`test_` 开头, 或者以`_test` 结尾

    # content of test_sample.py
    def func(x):
        return x + 1

    def test_answer():
        assert func(3) == 5

## run test

    py.test               # run all tests below current dir
    py.test test_mod.py   # run tests in module
    py.test somepath      # run all tests below somepath
    py.test -k stringexpr # only run tests with names that match the
                          # the "string expression", e.g. "MyClass and not method"
                          # will select TestMyClass.test_something
                          # but not TestMyClass.test_method_simple
    py.test test_mod.py::test_func # only run tests that match the "node ID",
                       # e.g "test_mod.py::test_func" will select
                                   # only test_func in test_mod.py

### output

    # html
    py.test --resultlog=path
    # xml
    py.test --junitxml=path

## multiple case
class 用`Test` 开头, 不要用`test` 开头, 并且不能有`__init__`

    # content of test_class.py
    class TestClass:
        def test_one(self):
            x = "this"
            assert 'h' in x

        def test_two(self):
            x = "hello"
            assert hasattr(x, 'check')


## help
```
py.test --version # shows where pytest was imported from
py.test --fixtures # show available builtin function arguments
py.test -h | --help # show help on command line and config file options
```
