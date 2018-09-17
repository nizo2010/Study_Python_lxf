# Chapter_011   生成器

## 生成器
将列表生成式中的 [] 替换成 ()，就变成了一个生成器
```python
L = [x * x for x in range(10)]
# L: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
g = (x * x for x in range(10))
# g: generator object
```


## next()
使用 next() 获取生成器的下一个返回值，直到最后一个返回值后抛出 StopIteration 异常


## for循环
常用的是使用 for 来循环迭代生成器，而不是使用next()


## yield
生成器定义的另一种方法： 函数定义中包含 yield 关键字
```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
    
>>> f = fib(6)    # 这里 f 就是一个生成器
>>> next(f)
1
>>> next(f)
1
>>> next(f)
2
>>> next(f)
3
>>> next(f)
5
>>> next(f)
8
>>> next(f)
Traceback (most recent call last):
...
StopIteration: done

>>> f = fib(8)
>>> for i in f:
...     print(i)
...
1
1
2
3
5
8
13
21
```

## 函数与生成器的区别

| 函数 | 生成器 |
| ------ | ------ |
| 函数是顺序执行，遇到 return 语句或最后一行函数语句就返回。 | 变成生成器的函数，每次调用 next() 时执行，遇到 yield 语句返回，再次执行时从上次返回的 yield 语句继续执行 |


## 杨辉三角问题

                    1
                   / \
                  1   1
                 / \ / \
                1   2   1
               / \ / \ / \
              1   3   3   1
             / \ / \ / \ / \
            1   4   6   4   1
           / \ / \ / \ / \ / \
          1   5  10  10   5   1
         / \ / \ / \ / \ / \ / \
        .........................


```python
def triangles():
    L = [1]
    while True:
        yield L
        L = [1] + [L[i] + L[i + 1] for i in range(len(L) -  1)] + [1]
        
>>> t =triangles()
>>> next(t)
[1]
>>> next(t)
[1, 1]
>>> next(t)
[1, 2, 1]
>>> next(t)
[1, 3, 3, 1]
>>> next(t)
[1, 4, 6, 4, 1]
```


## 链接

上一节 [Chapter_010 列表生成式](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_010.md "Chapter_010 列表生成式")

下一节 [Chapter_012 迭代器](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_012.md "Chapter_012 迭代器")

