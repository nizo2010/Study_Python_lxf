# Chapter_006   函数的参数

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
```

```Python
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other': kw) 
    
>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
>>> person('Jack', 24, city = extra['city'], jpb = extra['job']
name: Jack  age: 24     other: {'city': 'Beijing', 'job:', 'Engineer'}
>>> person('Jack', 24, **extra)     # **extra表示把extra这个dict的所有key-value用关键字参数传入到函数的 **kw参数
name: Jack  age: 24     other: {'city': 'Beijing', 'job:', 'Engineer'}
```

（注意：
\*\*extra表示把 extra 这个 dict 的所有key-value用关键字传入到函数的 \*\*kw参数
kw获得的 dict 是 extra 的一份拷贝，对 kw 的改动不会影响到函数外的 extra
）


## 命名关键字参数

限制关键字参数的名字，与关键字参数 \*\*kw 不同，命名关键字参数只需要一个特殊的分隔符\*，\*后面的参数，被视为命名关键字参数。

```Python
def person(name, age, *, city, job):
    print(name, age, city, job)
    
>>> person('nizo', 24, 123456, city = 'Shanghai', job = 'Engineer')
Traceback (most recent call last):
...
TypeError: person() takes 2 positional arguments but 3 positional arguments ( and 2 keyword-only arguments) were given
>>> person('nizo', 24, city = 'Shanghai', job = 'Engineer')
nizo 24 Shanghai Engineer
```

如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不需要\*隔离了。
```Python
def person(name, age, *args, city, job):
    print(name, age, args, city, job)
    
>>> person('nizo', 24, 'Shanghai', 'Engineer')
Traceback (most recent call last):
...
TypeError: person() missing 2 required keyword-only arguments: 'city' and 'job'
>>> person('nizo', 24, city = 'Shanghai', job = 'Engineer')
nizo 24 () Shanghai Engineer
>>> person('nizo', 24, 'Chinese', 'male', city = 'Shanghai', job = 'Engineer')
nizo 24 ('Chinese, 'male') Shanghai Engineer
```

命名关键字参数也可以有缺省值：
```Python
def person(name, age, *, city = 'Shanghai', job):
    print(name, age, city, job)
    
>>> person('nizo', 24, job = 'Engineer')
nizo 24 Shanghai Engineer
```


## 参数组合

参数定义的顺序：
必选参数 ---> 默认参数 ---> 可变参数 ---> 命名关键字参数 ---> 关键字参数

```Python
def f1(a, b, c = 0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)
    
def f2(a, b, c = 0, *, d, ** kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
    
>>> f1(1, 2)
a = 1 b = 2 c = 0 args = () kw = {}
>>> f1(1, 2, c = 3)
a = 1 b = 2 c = 3 args = () kw = {}
>>> f1(1, 2, 3, 'a', 'b')
a = 1 b = 2 c = 3 args = ('a', 'b') kw = {}
>>> f1(1, 2, 3, 'a', 'b', x = 99)
a = 1 b = 2 c = 3 args = ('a', 'b') kw = {'x': 99}
>>> f2(1, 2, d = 99, ext = None)
a = 1 b = 2 c = 0 d = 99 kw = {'ext': None}

>>> args = (1, 2, 3, 4)
>>> kw = { 'd': 99, 'x': '#'}
>>> f1(*args, **kw)
a = 1 b = 2 c = 3, args = {4,} kw = {'d': 99, 'x': '#'}

>>> args = (1, 2, 3)
>>> kw = {'d': 88, 'x': '#'}
>>> f2(*args, **kw)
a = 1 b = 2 d = 3 d = 88 kw = {'x': '#'}
```


## 链接

上一节 [Chapter_005 dict和set](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_005.md "Chapter_005 dict和set")

下一节 [Chapter_007 递归函数](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_007.md "Chapter_007 递归函数")
