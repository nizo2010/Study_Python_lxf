# Chapter_022   模块（Module）

1、在Python中，一个 .py 文件就是一个模块（Module）。

2、如果不同的人编写的模块名相同而引起冲突，怎么办？

Python引入按目录来组织模块的方法，称为包（Package）。

3、每一个包目录下都必须有一个 \_\_init\_\_.py 文件，这个文件可以是一个空文件。

> mycompany
>
>     |______ __init__.py
>
>     |______ abc.py
>
>     |______ xyz.py

abc.py 模块的名字是 mycompany.abc

xyz.py 模块的名字是 mycompany.xyz

\_\_init\_\_.py 本身是一个模块，它的模块名为 mycompany



## 链接

上一节 [Chapter_021 偏函数](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_021.md "Chapter_021 偏函数")

下一节 [Chapter_023 使用模块](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_023.md "Chapter_023 使用模块")
