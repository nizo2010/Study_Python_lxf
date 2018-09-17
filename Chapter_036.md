# Chapter_036   使用元类

> 动态语言和静态语言最大的不同，就是函数和类的定义，不是编译时定义的，而是运行时动态创建的

---

## type()

type()，查看一个类型或变量的类型

假设有hello.py模块

```python
# hello.py
class Hello(object):
    def hello(self, name = 'world'):
        print('Hello, %s' % name)
        
```

载入模块

```python
from hello import Hello     # 载入hello模块，依次执行该模块的所有语句，直接结果就是动态创建出一个Hello的class对象

h = Hello()
h.hello()
# 返回'Hello, world'
print(type(Hello))
# Hello是一个class，它的类型是type()，返回<class 'type'>
print(type(h))
# h是一个实例，它的类型是class Hello，返回<class 'hello.Hello'>
```

---

由此引出，class的定义是运行时动态创建的，创建class的方法就是使用type()函数

> **type()函数既可以返回一个对象的类型，又可以创建出新的类型**

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

def fn(self, name='world'):         # 先定义函数
    print('Hello, %s.' % name)

Hello = type('Hello', (object,), dict(hello=fn)) # 创建Hello class

h = Hello()
print('call h.hello():')
h.hello()
print('type(Hello) =', type(Hello))
print('type(h) =', type(h))
```

传入的三个参数：

*1、 class的名称，字符串'Hello'

*2、 继承的父类集合，因为是集合，所以用(object, )   \<-----注意逗号

*3、 class的方法名称与函数绑定，使用dict的方式绑定，这里把函数fn绑定到方法名hello上

执行结果为

```python
call h.hello()
Hello, world.
type(Hello) =  <class 'type'>
type(h) = <class 'type '__main__.Hello'>
```

> 通过type()函数创建出来的类和直接写class是完全一样的，因为Python解释器遇到class定义时，仅仅是扫描一下class定义的语法，然后调用type()函数筹建处class。


## metaclass：元类

先定义metaclass，就可以创建类，最后创建实例。

metaclass允许创建类或者修改类，可以把类看作是metaclass创建出来的“实例”。

