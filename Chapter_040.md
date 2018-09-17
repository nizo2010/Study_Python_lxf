# Chapter_040   文档测试

## doctest模块

doctest模块，可以直接提取主时钟的代码并执行测试

1） doctest严格按照Python交互式命令行的输入和输出来判断测试结果是否正确

2） 只有测试一次结果时，可以用“...”来表示中间的描述性输出

```python
# mydict2.py
class Dict(dict):
    '''
    Simple dict but also support access as x.y style.

    >>> d1 = Dict()
    >>> d1['x'] = 100
    >>> d1.x
    100
    >>> d1.y = 200
    >>> d1['y']
    200
    >>> d2 = Dict(a=1, b=2, c='3')
    >>> d2.c
    '3'
    >>> d2['empty']
    Traceback (most recent call last):
        ...
    KeyError: 'empty'
    >>> d2.empty
    Traceback (most recent call last):
        ...
    AttributeError: 'Dict' object has no attribute 'empty'
    '''
    def __init__(self, **kw):
        super(Dict, self).__init__(**kw)

    def __getattr__(self, key):
        try:
            return self[key]
        except KeyError:
            raise AttributeError(r"'Dict' object has no attribute '%s'" % key)

    def __setattr__(self, key, value):
        self[key] = value

if __name__=='__main__':
    import doctest
    doctest.testmod()
```

运行代码，如果没有输出则说明文档测试是正确的。

如果注释掉代码中的某一段（比如\_\_getattr\_\_或\_\_setattr\_\_），运行就会报错。

```python
class Dict(dict):
	'''
	Simple dict but also support access as x.y style.
	
	>>> d1 = Dict()
	>>> d1['x'] = 100
	>>> d1.x
	100
	>>> d1.y = 200
	>>> d1['y']
	200
	>>> d2 = Dict(a=1, b=2, c='3')
	>>> d2.c
	'3'
	>>> d2['empty']
	Traceback (most recent call last):
	...
	KeyError: 'empty'
	>>> d2.empty
	Traceback (most recent call last):
	...
	AttributeError: 'Dict' object has no attribute 'empty'
	'''
	
	def __init__(self, **kw):
		super(Dict, self).__init__(**kw)
		
	def __getattr__(self, key):
		try:
			return self[key]
		except KeyError:
			raise AttributeError(r"'Dict' object has no attribute '%s'" % key)
		
if __name__ == '__main__':
	import doctest
	doctest.testmod()
```

运行结果：

```python
**********************************************************************
File "mydict_doctest.py", line 10, in __main__.Dict
Failed example:
    d1['y']
Exception raised:
    Traceback (most recent call last):
      File "C:\Python\Python36\lib\doctest.py", line 1330, in __run
        compileflags, 1), test.globs)
      File "<doctest __main__.Dict[4]>", line 1, in <module>
        d1['y']
    KeyError: 'y'
**********************************************************************
1 items had failures:
   1 of   9 in __main__.Dict
***Test Failed*** 1 failures.
```


## 链接

上一节 [Chapter_039 单元测试](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_039.md "Chapter_039 单元测试")

下一节 [Chapter_041 文件读写](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_041.md "Chapter_041 文件读写")
