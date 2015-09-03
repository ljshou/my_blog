title: Python 之 decorator 装饰器
date: 2014-12-07 15:48:45
categories: python
tags: python
---
python 在语法上对装饰模式进行了支持。
<!--more-->
##一、问题
有如下的一个函数，
```py
def sum(x, y):
    print "x+y:", x+y
```
我们需要在每次调用 sum 函数的时候打印日志，但是我们并不希望修改sum函数。

##二、解决方案
###Solution 1:
最 brute-force 的方法，定义一个包裹函数, 在这个包裹函数的内部调用sum 函数（**包裹函数和 sum 函数最好具有相同的参数，和返回类型**）
```py
def wrapper1(x, y):
    print "calling %s" % sum.__name__
    return sum(x, y)
wrapper1(1, 2)
```
所有之前需要调用sum函数的地方，改用新的包裹函数。这样可行，但是加入另外一个函数 func, 同样需要进行日志，我们就得重新写另外一个包裹函数。显然，有更好的方法。

###Solution 2:
上面提到，**包裹函数和被包裹函数最好具有相同的参数，和返回类型。**
如何能够只写一个包裹函数，它可以包裹任意接口的函数呢？
```py
def wrapper2(*args, **kw):
    print "calling %s" % func.__name__
    return sum(*args, **kw)
wrapper2(1, 2)
```
python中函数的参数非常灵活，(\*args, **kw)可以匹配任意类型的参数，关于这两种参数的解释，会另写一篇博文。

上述的包裹函数 wrapper2 已经可以接受任意的参数类型，并且返回值即为sum 的返回值。但是，其实还没有完全达到要求，wrapper2的内部调用了函数sum, 如果我们需要包裹另一个func函数，那么sum就必须修改。

###Solution 3:
我们容易想到把 sum 作为函数的参数传入，但是这使得函数的接口变了。
```py
def wrapper3(func, *args, **kw):
    print "calling %s" % func.__name__
    return func(*args, **kw)
wrapper3(sum, 1, 2)
```

###Solution 4:
另一种做法是: 在wrapper外面再包裹一层函数，仅仅用于传入函数 sum
```py
def log(func):
    def wrapper(*args, **kw):
        print "calling %s" % func.__name__
        return func(*args, **kw)
    return wrapper
#调用
log(sum)(1, 2)
```
大家可能还是不满意，能不能还是通过 sum(1, 2) 来调用包含 log 信息的版本呢？ 当然可以！
```py
sum = log(sum)
sum(1, 2)
```
python中的函数名相当于 C++ 中的指针，可以通过赋值，将 sum 指向 log(sum)

###Ultimate Solution:
终极版本：通过 python 的 **@语法**
```py
def log(func):
    def wrapper(*args, **kw):
        print "calling %s" % func.__name__
        return func(*args, **kw)
    return wrapper

@log
def sum(x, y):
    print "x+y:", x+y
sum(1, 2)

@log
def func(x):
    print x
func(1)
```
任意一个函数，需要打印log信息, 仅仅需要在函数的定义前加 **@log**，等价于 `sum = log(sum)`

##三、练习
请编写一个decorator，能在函数调用的前后打印出'begin call'和'end call'的日志。

```py
def log2(func):
    def wrapper(*args, **kw):
        print "begin call"
        res = func(*args, **kw)
        print "end call"
        return res
    return wrapper
```
