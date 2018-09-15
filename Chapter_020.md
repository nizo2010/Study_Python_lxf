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
  
接下来，假设想要增强上述的 now() 函数的功能，比如在函数调用前后自动打印日志，但是又不希望修改 now() 函数的定义，这种在代码云下期间动态增加功能的方式，就是**“装饰器”**。
  
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

