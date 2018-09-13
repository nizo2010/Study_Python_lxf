# Chapter 函数的参数

## 默认参数

```Python
def add_end(L = []):
    L.append('END')
    retrun L
  
>>> add_end()
['END']
>>> add_end()
['END', 'END']
>>> add_end()
['END', 'END', 'END']
```

解释：
Python函数在定义的时候，默认参数L的值就被计算出来了，即 []。因为默认参数 L 也是一个变量，它指向对象 []。每次调用该函数，如果改变了 L 的内容，则下次调用时，默认参数的内容也就改变了，不再是函数定义时的 [] 了。

***定义默认参数要牢记一点，默认参数必须指向不可变对象！***

```Python
def add_end(L = None):
    if L is None:
        L = []
    L.append('END')
    return L
    
>>> add_end()
['END']
>>> add_end()
['END']
>>> add_end()
['END']
```


## 可变参数

```Python
def func(*args):
    pass
````
定义可变参数，仅仅在参数前加一个 * 号，则调用该函数时，可以传入任意个参数，包括0个参数。（这些可变参数在函数调用时，自动组装为一个tuple）


## 关键字参数

```Python
def func(**kwargs):
    pass
```
定义关键字参数，在参数前加两个 ** 号，允许传入0个或者人一个含有参数名的参数。（这些关键字参数在函数内部自动组装成一个dict）

```Python
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum

>>> num = [1, 2, 3]
>>> calc(num[0], num[1], num[2])
14
>>> calc(*num)    # *num表示把num这个list的所有元素作为可变参数传递进去
14
