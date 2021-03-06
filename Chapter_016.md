# Chapter_016 filter()

## filter()

filter() 函数也接收一个函数和一个序列，将传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素。

## filter()求素数

> 算法： 埃氏筛法
> 首先，列出从2开始的所有自然数，构造一个序列
>
> 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, ......
> 
> 取序列的第一个数2，它一定是素数，然后用2把序列的2的倍数筛掉
> 
> 3, ~~4~~, 5, ~~6~~, 7, ~~8~~, 9, ~~10~~, 11, ~~12~~, 13, ~~14~~, 15, ~~16~~, 17, ~~18~~, 19, ~~20~~, ......
> 
> 取新序列的第一个数3，它一定是素数，然后用3把序列的3的倍数筛掉
> 
> 5, ~~6~~, 7, ~~8~~, ~~9~~, ~~10~~, 11, ~~12~~, 13, ~~14~~, ~~15~~, ~~16~~, 17, ~~18~~, 19, ~~20~~, ......
>
> 取新序列的第一个数5，它一定是素数，然后用5把序列的5的倍数筛掉
> 
> 7, ~~8~~, ~~9~~, ~~10~~, 11, ~~12~~, 13, ~~14~~, ~~15~~, ~~16~~, 17, ~~18~~, 19, ~~20~~, ......
> 
> 不断筛选下去，最后就可以得到所有的素数

用Python来实现：
```python
# 首先构造一个从3开始的奇数序列，使用生成器
def _odd_iter():
    n = 1
    while True:
        n = n + 2
        yield n
        
# 定义一个筛选函数
def _not_divisible(n):
    return lambda x: x % n > 0
    
# 定义一个生成器
def primes():
    yield 2
    it = _odd_iter()    # 初始序列
    while True:
      n = next(it)      # 返回序列的第一个数
      yield n
      it filter(_notdivisible, it)   # 新序列
      
>>> p = primes()
>>> next(p)
2
>>> next(p)
3
>>> next(p)
5
>>> next(p)
7
>>> next(p)
11
>>> next(p)
13
>>> next(p)
17
>>> next(p)
19
>>> next(p)
23
>>> next(p)
29
```


## 课后作业
回数（从左向右读和从右向左读是一样的，比如12021,909等）
```python
def is_palindrome(n):
    return str(n) == str(n)[::-1]
    
output = filter(is_palindrome, range(1,200))
list(output)
```


## 链接

上一节 [Chapter_015 map/reduce](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_015.md "Chapter_015 map/reduce")

下一节 [Chapter_017 sorted()](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_017.md "Chapter_017 sorted()")
