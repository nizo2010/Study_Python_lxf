# Chapter_037   错误处理

## try...except...finally...

try语句块顺序执行

1、 try语句没有错误发生，跳过except语句块 -----\> 执行finally语句块

2、 try语句块发生错误，跳过其余代码 -----\> 执行except语句块 -----\> 执行finally语句块

```python
try:
    print('try...')
    r = 10 / 0
    print('result:', r)
except ZeroDivisionError as e:
    print('except:', e)
finally:
    print('finally...')
print('END')

# 执行结果
try...
except: division by zero
finally...
END
```

如果将r = 10 / 0 改成 r = 10 / 2

```python
try:
    print('try...')
    r = 10 / 2
    print('result:', r)
except ZeroDivisionError as e:
    print('except:', e)
finally:
    print('finally...')
print('END')

# 执行结果
try...
result: 5.0
finally...
END
```


## 多个except语句块

except语句块可以有多个，每隔except按顺序执行，若匹配到就跳过剩余其他的except语句块。（即使剩余的except语句块中的异常更匹配产生的错误）


## try...except...else...finally...

这里esle语句块，当try语句块没有错误发生时，跳过except语句块，执行else语句块，最后执行finally语句块。

如果try语句块发生错误，则执行except语句块，跳过else语句块，最后执行finally语句块。

> **当try语句块发生错误，而在except语句块没有匹配到异常，则先执行finally语句块，然后抛出异常，程序退出。**


## 使用try...except...捕捉异常，可以跨越多层调用

比如main()调用bar()，bar()调用foo()，结果foo()出现异常，这时只要main()捕获到了，也可以处理。

```python
def foo(s):
    return 10 / int(s)
  
def bar(s):
    return foo(s) * 2
  
def main():
    try:
        bar('0')
    except Exception as e:
        print('Exception:', e)
    finally:
        print('finally...')

main()
# 执行结果
# Exception: division by zero
# finally...
```


## 调用栈

如果错误没有被捕获，它就会一直往上抛，最后被Python解释器捕获，打印一个错误信息，然后程序退出。

假设有如下的err.py文件：

```python
def foo(s):
    return 10 / int(s)
    
def bar(s):
    return foo(s) * 2
    
def main():
    bar('0')
    
```

我们在命令行运行该python程序

```python
$ python3 err.py
Traceback (most recent call last):
  File "name.py", line 10, in <module>
    main()
  File "name.py", line 8, in main
    bar('0')
  File "name.py", line 5, in bar
    return foo(s) * 2
  File "name.py", line 2, in foo
    return 10 / int(s)
ZeroDivisionError: division by zero
```

这里一步步往上抛出异常，最后在return 10 / int(s)处，抛出ZeroDivisionError。


## 记录错误

如果不捕获错误，可以让Python解释器来打印出错误堆栈，但程序也被退出了，如上所述。

如果既想捕获错误，又想让错误堆栈打印出来（将可以可以分析错误原因），同时程序还能继续执行下去。===\> **这里使用Python内置的logging模块，可以记录错误信息**

还是以上面的例子为例，只需要修改main()函数，并在程序开头导入模块语句 import logging

```
