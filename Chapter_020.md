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
