# Chapter_014 高阶函数（High-order fuction）

1、f = abs
f 是变量，可以指向一个函数（函数可以赋值给变量）


2、传入函数
```python
def add(x, y, f):         # f 是一个函数名
    return f(x) + f (y)
```


***高阶函数，就是让函数的参数可以接收别的函数***


## 链接

上一节 [Chapter_013 函数式编程（Functional Programming）](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_013.md "Chapter_013 函数式编程（Functional Programming）")

下一节 [Chapter_015 map/reduce](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_015.md "Chapter_015 map/reduce")
