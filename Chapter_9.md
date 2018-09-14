# 迭代

1、只要是可迭代对象（list、tuple、dict和字符串等），都可以使用 for 循环来迭代


2、默认情况下， dict迭代的是key
```Python
for key in dict1:
    pass
```

如果迭代value
```python
for value in dict1.values():
    pass
```

如果同时迭代key和value
```python
for key, value in dict.items():
    pass
```


3、判断对象是否可迭代
```python
>>>from collections import Iterable

>>>isinstance('abc', Iterable)
True
```

4、对list、tuple实现下标循环
```python
for i, value in enumerate(['A', 'B', 'C']):
    print(i, ":", value)
    
0 : A
1 : B
2 : C
```


使用迭代查找一个list中最小值和最大值，并返回一个tuple（不使用min和max函数）
```python
def findMinAndMax(L):
    min = None
    max = None      # 若L为空列表，则返回(None, None)
    for i in L:
        if min == None or min > i:    # 当L中只有一个元素，命中min = None，则min为唯一元素
            min = i
        if max == None or max < i:    # 当L中只有一个元素，命中max = None，则max为唯一元素
            max = i
    return (min, max)
    
>>> L1 = []
>>> print(findMinAndMax(L1))
(None, None)
>>> L2 = [0]
>>> print(findMinAndMax(L2))
(0, 0)
>>> L3 = [1, 3, 5, 7, 9]
>>> print(findMinAndMax(L3))
(1, 9)
>>> L4 = [8, 6, 4, 2, 0]
>>> print(findMinAndMax(L4))
(0, 8)
>>> L5 = [2, 5, 8, 4, 1, 7]
>>> print(findMinAndMax(L5))
(1, 8)
```
