# Chapter_039 单元测试

## unittest模块

假设我们编写了一个mydict.py文件，定义了一个Dict类。代码如下：

```python
# mydict.py
class Dict(dict):

    def __init__(self, **kw):
        super().__init__(**kw)

    def __getattr__(self, key):
        try:
            return self[key]
        except KeyError:
            raise AttributeError(r"'Dict' object has no attribute '%s'" % key)

    def __setattr__(self, key, value):
        self[key] = value
```

编写单元测试，引入Python自带的unittest模块，编写mydict_test.py，代码如下：

```python
# mydict_test.py
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import unittest

from mydict import Dict

class TestDict(unittest.TestCase):      # 测试类TestDict，从unittest.TestCase类继承

    def test_init(self):                # 以test开头的方法才是测试方法；不以test开头的方法，测试时不执行
		d = Dict(a=1, b='test')
		self.assertEqual(d.a, 1)        # Python的unittest.TestCase内置的条件判断，assertEqual判断结果是否相等，assertTrue判断结果是否为True
		self.assertEqual(d.b, 'test')
		self.assertTrue(isinstance(d, dict))
	
	def test_key(self):
		d = Dict()
		d['key'] = 'value'
		self.assertEqual(d.key, 'value')            

	def test_attr(self):
		d = Dict()
		d.key = 'value'
		self.assertTrue('key' in d)
		self.assertEqual(d['key'], 'value')

	def test_keyerror(self):
		d = Dict()
		with self.assertRaises(KeyError):       # 期望抛出指定类型Error
			value = d['empty']                  # 通过d['empty']访问不存在的key，抛出KeyError

	def test_attrerror(self):
		d = Dict()
		with self.assertRaises(AttributeError):
			value = d.empty                     # 通过d.empty访问不存在的attr，抛出AttributeError

```


## 运行单元测试

1） 在mydict_test.py中加入：

```python
if __name__ == '__main__':
    unittest.main()
```

此时可以直接运行，结果：

```python
.....
----------------------------------------------------------------------
Ran 5 tests in 0.001s

OK
```

2）  pytyhon -m unittest mydict_test.py

```python
C:\>python -m unittest mydict_test.p

.....
----------------------------------------------------------------------
Ran 5 tests in 0.001s

OK
```

## setUp 与 tearDown

两种特殊的setUP和tearDown方法，分别在每调用一个以test开头的测试方法前后分别指向（例如测试时需要启动一个数据库，可以在setUP()方法中连接数据库，tearDown(）方法中关闭数据库，此时就可以在每个测试方法中省略重复的代码）

修改mydict_test.py代码：

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import unittest

from mydict import Dict

class TestDict(unittest.TestCase):

	def setUp(self):
		print('setUp...')
		
	def tearDown(self):
		print('tearDown...')
		
	def test_init(self):
		d = Dict(a=1, b='test')
		self.assertEqual(d.a, 1)
		self.assertEqual(d.b, 'test')
		self.assertTrue(isinstance(d, dict))
	
	def test_key(self):
		d = Dict()
		d['key'] = 'value'
		self.assertEqual(d.key, 'value')

	def test_attr(self):
		d = Dict()
		d.key = 'value'
		self.assertTrue('key' in d)
		self.assertEqual(d['key'], 'value')

	def test_keyerror(self):
		d = Dict()
		with self.assertRaises(KeyError):
			value = d['empty']

	def test_attrerror(self):
		d = Dict()
		with self.assertRaises(AttributeError):
			value = d.empty

if __name__ == '__main__':
	unittest.main()
```

运行结果：

```python
setUp...
tearDown...
.setUp...
tearDown...
.setUp...
tearDown...
.setUp...
tearDown...
.setUp...
tearDown...
.
----------------------------------------------------------------------
Ran 5 tests in 0.002s

OK
```


## 链接

上一节 [Chapter_038 调试](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_038.md "Chapter_038 调试")

下一节 [Chapter_040 文档测试](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_039.md "Chapter_040 文档测试")
