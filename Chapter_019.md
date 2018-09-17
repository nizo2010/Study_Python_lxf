# Chapter_019   匿名函数 lambda函数

```python
def f(x):
    return x * x
    
lambda x: x* x
```

关键字 lambda 表示匿名函数，冒号前面的 x 表示函数参数。

匿名函数有个限制，就是只能有一个表达式，不用写return，返回值就是该表达式的结果。

匿名函数有个好处，因为函数没有名字，不必担心函数名冲突。此外，匿名函数也是一个函数对象，也可以把匿名函数赋值给一个变量，再利用变量来调用该函数：

```python
>>> f = lambda x: x * x
>>> f
<function <lambda> at 0x000000000310C0D0>
>>> f(5)
25
```

同样，也可以把匿名函数作为返回值返回，比如：
```python
def build():
    return lambda x, y: x * x _ y + y

>>> f = build()
>>> f
<function build.<locals>.<lambda> at 0x000000000310C0D0>
>>> f(3, 4)
25
```

## 求奇数列表

```python
def is_odd(n):
    return n % 2 == 1
    
>>> L = list(filter(is_odd, range(1, 20)))
>>> L
[1, 3, 5, 7, 9, 11, 13, 15, 17, 19]

>>> L = list(filter(lambda x: x % 2 == 1, range(20)))
>>> L
[1, 3, 5, 7, 9, 11, 13, 15, 17, 19]
```

## 链接

上一节 [Chapter_018 返回函数](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_018.md "Chapter_018 返回函数")

下一节 [Chapter_020 装饰器](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_020.md "Chapter_020 装饰器")
