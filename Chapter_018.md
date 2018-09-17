# Chapter_018 返回函数

## 函数作为返回值

```python
def lazy_sum(*args):
    def sum():
        ax = 0
        for n in args:
            ax = ax + n
        return ax
    return sum


>>> f = lazy_sum(1, 3, 5, 7, 9)
>>> f       # 返回的是一个sum()函数
<function lazt.sum.<locals>.sum ax 0x000000000310C048>
>>> f()     # 调用f()才是计算求和结果
25

```

函数lazy_sum()中又定义了函数sum()，并且内部函数sum()可以引用外部函数lazy_sum()的参数和局部变量，当lazy_sum()返回函数sum()时，相关参数和变量都保存在赶回的函数中，这种程序结构称为***“闭包（Closure）”***。


**注意：当调用lazy_sum()时，每次调用都会返回一个新的函数，即使传入相同的参数**

```python
>>> f1 = lazy_sum(1, 3, 5, 7, 9)
>>> f1
<function lazt.sum.<locals>.sum ax 0x000000000310C0D0>
>>> f2 = lazy_sum(1, 3, 5, 7, 9)
>>> f2
<function lazt.sum.<locals>.sum ax 0x000000000310C158>
>>> f1 == f2
False
```


## 闭包
闭包的概念：返回的函数在其定义内部引用了局部变量args，所以当一个函数返回了一个函数后，其内部的局部变量还是被新函数引用。

另一个需要注意的地方是，返回的函数没有被立即执行，而是直到调用了f()才执行。

基于以上两点，引出如下问题：

```python
def count():
    fs = []
    for i in range(1, 4):
        def f():
            return i * i
        fs.append(f)
    return fs
    
>>> f1, f2, f3 = count()
>>> f1()
9
>>> f2()
9
>>> f3()
9
```

调用f1()、f2()、f3()的结果是9、9、9，而不是想要的1、4、9，为什么呢？

解释：

由于返回的函数引用了变量 i ，但它并未立即执行。等到三个函数都返回时，它们所引用的变量 i 已经变成了3，因此最终结果都是9。

> 返回闭包时牢记一点：
>
> ***返回函数不要引用任何循环变量，或者后续会发生变化的变量***

## 如果一定要引用循环变量怎么办？

方法就是再创建一个函数，用该函数的参数绑定循环变量当前的值，无论该循环变量耳后续如何更改，已绑定到函数的参数的值不变。

```python
def count():
    def f(n):
        def g():
            return n * n
        return g
    fs = []
    for i in range(1, 4):
        fs.append(f(i))
    return fs

>>> f1, f2, f3 = count()
>>> f1()
1
>>> f2()
4
>>> f3()
9
```


## 课后作业

闭包返回一个计数器函数，每次调用返回递增整数。

```python
# 解答1
def createCounter():
    n = [0]
    def counter():
        n[0] = n[0] + 1
        return n[0]
    return counter
    
>>> f1 = createCounter()
>>> f1()
1
>>> f1()
2
>>> f1(), f1(), f1()
(3, 4, 5)
```

```python
# 解答2
def createCounter():
    n = 0
    def counter():
        nonlocal n  
        n = n + 1
        return n
    return counter
    
>>> f2 = createCounter()
>>> f2()
1
>>> f2()
2
>> f2(), f2(), f2()
(3, 4, 5)
```


## 链接

上一节 [Chapter_017 sorted()](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_017.md "Chapter_017 sorted()")

下一节 [Chapter_019 匿名函数 lambda函数](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_019.md "Chapter_019 匿名函数 lambda函数")
