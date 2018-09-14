# Chapter_012   迭代器

## 可迭代对象

可以直接作用于 for 循环的数据类型：
1、集合数据类型：list、tuple、dict、set、str等
2、生成器generator，包括生成器和带 yield 关键字的生成器函数
以上统称为可迭代对象：Iterable


## 迭代器
可以使用 next() 函数调用并不断返回下一个值的对象称为迭代器：Iterator
因此，上面的集合数据类型就不是迭代器，因为它们都不能使用 next() 函数调用


## 为什么list、dict、str等数据类型不是Iterator？
因为Python的Iterator对象表示的是一个数据流，Iterator对象可以被 next() 函数调用并不断返回下一个数据，直到没有数据时抛出一个StopIteration错误。
可以把这个数据流看作是一个有序序列，但却不能提前知道序列的长度，只能不断通过 next() 函数实现按需计算下一个数据，所以Iterator是惰性的，只有在需要返回下一个数据时它才会计算。
Iterator甚至可以表示一个无限大的数据流，例如全体自然数，而使用 list 永远无法存储全体自然数。


## iter()
可以通过 iter() 函数，将list、str等可迭代对象 Iterable 变成一个迭代器 Iterator。
```python
>>> L = [1, 2, 3, 4]
>>> next(L)
Traceback (most recent call last):
...
TypeError: 'list' object is not an iterator
>>> g = iter(L)
>>> g
<list_iterator object at 0x00000000030912B0>
>>> next(g)
1
>>> next(g)
2
>>> next(g)
3
>>> next(g)
4
>>> next(g)
Traceback (most recent call last):
...
StopIteration
```
