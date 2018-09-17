# Chapter_023 使用模块

首先看一下下面这个标准的模块形式， hello.py 模块

```python
#/usr/bin/env python3
# -*- coding:utf-8 -*-
''' a test module '''
__author__ = 'nizo'

import sys      # sys模块有一个argv变量，用list存储命令行的所有参数，且argv至少有有一个参数，即为该 .py 文件名

def test():
    args = sys.argv
    if len(args) == 1:
        print('Hello, world!')
    elif len(args) == 2:
        print('Hello, %s!' % args[1] )
    else:
        print('Too many arguments!')
        
if __name__ == '__main__':
    test()
```

首先说明一下 \_\_name\_\_ == '\_\_main\_\_' 这段代码：

如果使用命令行允许该模块，那么 \_\_name\_\_ 会被置为 \_\_main\_\_ ，因此这段代码执行结果为True，会执行 if 代码块的语句。

如果是在其他地方 import 该模块，那么 \_\_name\_\_ 不会被置为 \_\_main\_\_ ， if 代码块的语句将不会被执行。

我们使用命令行运行一下该模块

```python
$ python hello.py
Hello, world!
$ python hello.py nizo
Hello, nizo!
$ python hello.py nizo today
Too many arguments!
```


## 作用域

1、只在模块内部使用的函数和变量，通过前缀 \_ 实现。

2、类似 \_\_xxx\_\_  这样的变量是特殊变量，可以被直接引用，但是有特殊用途（比如 \_\_name\_\_ 等）

3、类似 \_x， \_\_x 这样的变量是非公开的（private），不应该被直接引用

（为什么不是“不能”直接被引用，而是“不应该”，因为Python并没有一种方法可以完全限制访问private变量或函数）

```python
def _private_1(name):
    return 'Hello, %s' % name
    
def _private_2(name):
    return 'Hi. %s' % name
    
def greeting(name):
    if len(name) > 3:
        return _private_1(name)
    else:
        return _private_2(name)
        
```

在该模块中公开greeting()函数，而把内部逻辑用private函数隐藏起来。

这样，调用greeting()函数不用关心内部的private函数细节。

这也是一种非常有用的代码封装和抽象的方法，即

> ***外部不需要引用的函数全部定义成private，只有外部需要引用的函数才定义为pubilc***


## 链接

上一节 [Chapter_022 模块（Module）](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_022.md "Chapter_022 模块（Module）")

下一节 [Chapter_024 安装第三方模块](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_024.md "Chapter_024 安装第三方模块")
