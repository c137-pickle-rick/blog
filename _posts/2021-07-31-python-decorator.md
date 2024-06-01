---
title: "python装饰器(简单装饰器、叠加装饰器、防止被装饰函数更名、参数化装饰器)"
categories:
  - Python
tags:
  - decorator
  - demo
last_modified_at: 2021-07-31T22:46:03+08:00
---
  
装饰器是可调用的对象，其参数是另一个函数。一般情况下，装饰器会对被装饰的函数做一些处理，然后将它返回，或者将其替换成另一个函数或可调用对象。

装饰器有两大特性，一是能把被装饰的函数替换成其他函数；二是装饰器在加载模块时立即执行。

##### 先来看一个不用装饰器的例子：

```python
# 以一个函数为传入参数
def decorate(func):
    def inner():
        print("running inner()")
        res = func()
        return res
    # 返回内部定义的函数，并适时调用它
    return inner


def test():
    print("running test()")
    return "test() return"


if __name__ == '__main__':
    f = decorate(test)
    print(f.__name__)
    r = f()
    print(r)
```

这段代码的输出结果为：

> inner
running inner()
running test()
test() return

##### 使用装饰器来改写这段代码：

```python
# 以一个函数为传入参数
def decorate(func):
    def inner():
        print("running inner()")
        res = func()
        return res

    return inner


@decorate
def test():
    print("running test()")
    return "test() return"


if __name__ == '__main__':
    print(test.__name__)  # 打印出来不是test，而是inner，被装饰器装饰后已经不是原函数了(后面会说到让被装饰的函数名称不变)
    r = test()
    print(r)
```

这段代码输出结果，和上一段是一样的：

> inner
running inner()
running test()
test() return

##### 使用functools.wrap来防止被装饰的函数更名：

```python
import functools


# 以一个函数为传入参数
def decorate(func):
    # 使用这个装饰器把func的属性赋值到inner中，这样原函数名字就会保留下来了，具体为__name__和__doc__属性
    @functools.wraps(func)
    # 支持原函数func传递的各种参数
    def inner(*args, **kwargs):
        print("running inner()")
        res = func(*args, **kwargs)
        return res

    return inner


@decorate
def test(content):
    print(content)
    return "test() return"


if __name__ == '__main__':
    print(test.__name__)  # 结果为test
    r = test(content=10)    # 正常传参数调用
    print(r)
```

输出结果为：

> test
running inner()
10
test() return

##### 多个装饰器装饰同一个函数：

把@d1和@d2两个装饰器按顺序应用到函数f上，相当于f=d1(d2(f))，调用顺序为，最下面的装饰器最先被调用，即：

```python
@d1
@d2
def f():
    pass
```

等同于:

```python
def f():
    pass

f=d1(d2(f))
```

##### 如何给装饰器传参数

如何给装饰函数的装饰器传递参数呢？实现方式为：在之前定义的装饰器函数外再包一层，即创建一个装饰器工厂函数，在装饰时调用该工厂函数，传入相应定义的参数，返回一个装饰器函数，该装饰器函数的入参是一个函数，跟之前就一样了。具体看例子：

```python
import functools


def decorate_factory(arg):
    # 以一个函数为传入参数
    def decorate(func):
        # 使用这个装饰器把func的属性赋值到inner中，这样原函数名字就会保留下来了，具体为__name__和__doc__属性
        @functools.wraps(func)
        # 支持原函数func传递的各种参数
        def inner(*args, **kwargs):
            print("running inner()")
            res = func(*args, **kwargs)
            # 装饰器工厂函数传入的参数和func的返回结果结合后再返回
            return res + " " + arg

        return inner
    return decorate


@decorate_factory(arg="decorate_factory")  # 此处调用了decorate_factory函数，并传入定义好的相应参数，该工厂函数返回了一个装饰器函数，这个装饰器函数正好应用在test函数上
def test(content):
    print(content)
    return "test() return"


if __name__ == '__main__':
    print(test.__name__)  # 结果为test
    r = test(content=10)    # 正常传参数调用
    print(r)
```

代码输出结果为：

> test
running inner()
10
test() return decorate_factory