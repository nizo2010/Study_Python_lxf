# Chapter_038   调试

## print()

第一种调试方法，使用print()函数打印变量的值

缺点就是程序中有大量的print()函数，影响可读性，后续删除工作量大


## 断言 Assert

凡是用print()函数来辅助查看的地方，都可以用断言来替代

```python
def foo(s):
    n = int(s)
    assert n != 0, 'n is zero!'
    return 10 / n
    
def main():
    foo('0')
    
main()
```

执行结果为

```python
Traceback (most recent call last):
  ...
AssertionError: n is zero!
```

这里assert的意思是：表达式 n != 0 应该是True，如果断言失败，assert抛出AssertionError，并带有assert语句后的描述字符串。


## logging

还可以使用logging来代替print()，logging不会抛出错误，可以输出到文件

```python
import logging

s ='0'
n = int(s)
logging.warning('n = %d' % s)
print(10 / n)

运行结果为：
WARNING:root:n = 0
Traceback (most recent call last):
  File "err.py", line 7, in <module>
    print(10 / n)
ZeroDivisionError: division by zero
```

logging允许指定记录信息的级别：  debug ---\> info ---\> warning ---\> error ---\> critical

```python
import logging

logging.basicConfig(level = logging.INFO)

s = '0'
n = int(s)
logging.info('n = %d' % n)
print(10 / n)

运行结果为：
INFO:root:n = 0
Traceback (most recent call last):
  File "err.py", line 8, in <module>
    print(10 / n)
ZeroDivisionError: division by zero
```

当指定level为高级别时，低于其级别的logging就不起作用了（比如指定level = ERROR，则DEBUG、INFO、WARNING级别不记录）


## pdb

启动Python解释器的pdb调试器，让程序可以以单步方式运行，该可以随时查看运行状态

```python
# err.py
s = '0'
n = int(s)
print(10 / n)
```

命令行运行：

```python
c:\python3 -m pdb err.py     # 以参数-m pdb启动
> c:\err.py(1)<module>()
-> s = '0'                  # 启动后，pdb定位到下一步要执行的代码  s = '0'
(Pdb) l                     # 输入命令l来查看代码
  1  -> s = '0'
  2     n = int(s)
  3     print(10 / n)
[EOF]
(Pdb) n                     # 输入命令n可以单步执行代码
> c:\err.py(2)<module>()
-> n = int(s)
(Pdb) n
> c:\err.py(3)<module>()
-> print(10 / n)
(Pdb) n
ZeroDivisionError: division by zero
> c:\err.py(3)<module>()
-> print(10 / n)
(Pdb) p s                   # 任何时候都可以输入命令 “p 变量名” 来查看变量的值
'0'
(Pdb) p n
0
(Pdb) q                     # 输入命令q，结束调试，退出程序
```


## pdb.set_trace()

假设代码工程很大，代码量有很多行，单步执行就很吃力了。如果不想单步执行，可以在可能出错的地方放一个pdb.set_trace()设置一个断点。

```python
#error.py

import pdb
s = '0'
n = int(s)
pdb.set_trace()
print(10 / n)
```

运行代码，程序会自动在pdb.set_trace()出暂停并进入pdb调试环境中：

1） 可以用命令p查看变量值

2） 可以用命令c继续运行

```python
c:\python error.py
> c:\error.py(6)<module>()
-> print(10 / n)
(Pdb) p n
0
(Pdb) p s
'0'
(Pdb) n
ZeroDivisionError: division by zero
> c:\error.py(6)<module>()
-> print(10 / n)
(Pdb) c
Traceback (most recent call last):
  File "error.py", line 6, in <module>
    print(10 / n)
ZeroDivisionError: division by zero
```


## 链接

上一节 [Chapter_037 错误处理](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_037.md "Chapter_037 错误处理")

下一节 [Chapter_039 单元测试](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_039.md "Chapter_039 单元测试")
