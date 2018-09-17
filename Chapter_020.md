# Chapter_020 装饰器


函数也是一个对象，而且函数对象可以赋值给变量，通过变量也能调用该函数。

假如有如下这个函数：

```python
import time

def now():
    print(time.asctime(time.localtime(time.time())))
    
>>> f = now
>>> f()
Sat Sep 15 16:19:33 2018
```
  
其中函数有一个 \_\_name\_\_ 属性，可以获取函数的名字
```python
>>> now.__name__
'now'
>>> f.__name__
'now'
```
  
接下来，假设想要增强上述的 now() 函数的功能，比如在函数调用前后自动打印日志，但是又不希望修改 now() 函数的定义，这种在代码云下期间动态增加功能的方式，就是 **“装饰器”** 。
  
本质上，装饰器（decorator）就是一个返回函数的高阶函数。
  
```python
def log(func):
    def wrapper(*args. **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return warpper
```      

上面的 log() 函数就是一个decorator，接收一个函数 func 作为参数，并返回一个函数 wrapper ，借助Python的 @ 语法，将decorator置于函数的定义处：

```python
import time

@log
def now():
    print(time.asctime(time.localtime(time.time())))
    
>>> now()       # 调用 now() ，不仅会运行 now() 函数本身，还会在运行前打印日志
call now():
Sat Sep 15 16:24:17 2018
```

将 @ 置于 now() 函数的定义处，相当于执行了语句

```python
now = log(now)
```

由于 log() 是一个decorator，返回一个函数，所以原来的 now() 函数依然存在，只是现在同名的 now 变量指向了新的函数，于是调用 now() 将执行新函数，即在 log() 函数中返回的 wrapper() 函数，wrapper() 函数的参数定位 (\*args, \*\*kw)，则 wrapper() 函数可以接收任意参数的调用，在 wrapper() 函数内，首先打印日志，再紧接着调用原始函数。

若decorator本身需要传入参数，那就需要编写一个返回decorator的高阶函数

```python
import time

def log(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator
    
@log('execute')
def now():
    print(time.asctime(time.localtime(time.time())))
    
>>> now()
execute now():
Sat Sep 15 16:30:19 2018
```

和2层嵌套的decorator相比，3层嵌套的效果是：

```python
now = log('execute')(now)
```

首先执行 log('execute') ，返回的是 decorator 函数，再调用返回的 decorator 函数，参数是 now 函数，返回值最终是 wrapper 函数。因此 now 变量最终指向 wrapper() 函数，所以它的 \_\_name\_\_ 属性从原来的'now'变成了'wrapper'。那么需要把原始函数的 \_\_name\_\_ 等属性复制到 wrapper() 函数中，否则某些依赖函数签名的代码执行就会出错。

但是，一定不要编写 wrapper.\_\_name\_\_ = func.\_\_name\_\_ 这样的代码，因为Python内置的 functools.wraps 就是做这个工作的。

上述2个例子完整的decorator代码：

```python
import time, functools

def log(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return warpper
    
@log
def now():
    print(time.asctime(time.localtime(time.time())))

>>> now()
call now():
Sat Sep 15 16:51:42 2018
```

或者针对带参数的decorator：

```python
import functools

def log(text):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator

@log('execute')
def now():
    print(time.asctime(time.localtime(time.time())))
    
>>> now()
execute now():
Sat Sep 15 17:00:21 2018
```

只需要在定义 wrapper() 的前面加上 @functools.wraps(func) 即可。




## 参考

如果不使用 functools.wraps

```python
def logged(func):
	def with_logging(*args, **kwargs):
		"""do something"""
		print(func.__name__, "was called")
		return func(*args, **kwargs)
	return with_logging
	
@logged
def f(x):
	"""do some math"""
	print(x * x)
	
if __name__ == '__main__':
	g = logged(f)
	print(g.__name__)
	print(g.__doc__)

###
# 返回结果为
with_logging
do something

```

而 functools.wraps 则可以将原函数对象的指定属性复制给包装函数对象，属性包括 \_\_module\_\_ ， \_\_name\_\_ ， \_\_doc\_\_ 等。

```python
import functools

def logged(func):
	@functools.wraps(func)
	def with_logging(*args, **kwargs):
		"""do something"""
		print(func.__name__, "was called")
		return func(*args, **kwargs)
	return with_logging
	
@logged
def f(x):
	"""do some math"""
	print(x * x)
	
if __name__ == '__main__':
	g = logged(f)
	print(g.__name__)
	print(g.__doc__)
	
###
# 返回结果为
f
do some math

```


## 课后作业

设计一个decorator，它作用于任何函数上，并打印该函数的执行时间

```python
import functools, time

def metric(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start_time = time.time()        # 函数运行前的时间戳
        ret = func(*args, **kwargs)     # 执行函数，得到返回值赋值给ret
        end_time = time.time()          # 函数运行后的时间戳
        ntime = str(end_time - start_time)      # 计算执行函数运行的时间
        print('%s executed in %s ms...' % (func.__name__, ntime))
        return ret
    return wrapper
    
@metric
def fast(x, y):
    time.sleep(0.0012)
    return x + y
    
@metric
def slow(x, y, z):
    time.sleep(0.1234)
    return x * y * z
    
if __name__ == '__main__':
    f = fast
    s = slow
    print(f(10, 10))
    print(s(10, 10, 10))
    
###
# 返回结果
fast executed in 0.0029299259185791016 ms...
20
slow executed in 0.12402820587158203 ms...
1000
```


## 链接

上一节 [Chapter_019 匿名函数 lambda函数](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_019.md "Chapter_019 匿名函数 lambda函数")

下一节 [Chapter_021 偏函数](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_021.md "Chapter_021 偏函数")
