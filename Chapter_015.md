# Chapter_015   map/reduce

## map()

map() 函数接收2个参数，第一个参数是函数名，第二个参数是Iterable(可迭代对象)。
map() 函数将传入的函数依次作用于可迭代对象的每一个元素，并把结果作为新的可迭代对象Iterable返回。
```python
def f(x):
    return x * x
    
>>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> list(r)
[1, 4, 9, 16, 25, 36, 49, 64, 91]
```


## reduce（）

reduce() 函数也接收2个参数，第一个参数是函数名，第二个参数是Iterable(可迭代对象）。
reduce() 函数的第一个参数的函数必须接收2个参数，该函数作用于可迭代对象中，并将结果和可迭代对象的下一个元素做累积计算。
```python
reduce(f, [x1, x2, x3, x4])
```
相当于
```python
f(f(f(x1, x2), x3), x4)
```

通过几个例子说明一下reduce()函数。
```python
# 求和序列
from functools import reduce

def add(x, y):
    retrun x + y
    
reduce(add, [1, 3, 5, 7, 9])            # 返回结果 25
```

```python
# 将序列[1, 3, 5, 7, 9]转换成整数13579
from functools import reduce

def fn(x, y):
    return x * 10 + y
    
reduce(fn, [1, 3, 5, 7, 9])             # 返回结果 13579
```


## map()和reduce()将str转换为int的函数
```python
from functools import reduce

def fn(x, y):
    return x * 10 + y
    
def char2num(s):
    digits = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}
    return digits[s]
    
reduce(fn, map(char2num, '13579'))      # 返回结果 13579
```

整理成一个str2int函数
```python
from functools import reduce

DIGITs = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}
def str2int(s):
    def fn(x, y):
        return x * 10 + y
    
    def char2num(s):
        return DIGITs[s]
        
    return reduce(fn, map(char2num, s))

str2int('13579')        # 返回结果 13579
```

使用lambda函数进一步简化：
```python
from functools import reduce

DIGITs = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}
def char2num(s):
    return DIGITs[s]
    
def str2int(s):
    return reduce(lamdba x, y: x * 10 + y, map(char2num, s))
        
str2int('13579')        # 返回结果 13579
```

## 课后作业
str2float()函数，将字符串'123.456'转换成浮点数123.456。
```python
from functools import reduce

DIGITs = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}
def char2num(s):
    return DIGITs[s]

def str2float(s):
    n = len(s)                      # 字符串长度
    index = s.index('.')            # 字符串中小数点的索引位置
    s = s[:index] + s[index + 1:]   # 去掉小数点后重新拼接字符串
    result = reduce(lambda x, y: x * 10 + y, map(char2num, s))      #将字符串转换成整数
    return result / pow(10, (n - 1 - index))    #根据小数点位置，除以10的幂次方
    
str2float('123.456')        # 返回123.456
str2float('0.12345')        # 返回0.12345
```


## 链接

上一节 [Chapter_014 高阶函数（High-order fuction）](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_014.md "Chapter_014 高阶函数（High-order fuction）")

下一节 [Chapter_016 filter()](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_016.md "Chapter_016 filter()")
