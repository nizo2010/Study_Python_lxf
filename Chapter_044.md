# Chapter_044 序列化

程序运行过程中，所有变量都存放在内存中，比如一个dict：

```python
d = dict(name = 'nizo', age = 18, score = 100)
```

可以随时修改变量，比如讲name改成'Michael'，一旦程序结束，变量所占用的内存就被操作系统回收，如果没有把修改后的'Michael'存储到磁盘，下次运行程序，变量name又被初始化为'nizo'。


## 序列化与反序列化

把变量从内存中编程可存储或传输的过程，叫做序列化（pickling）。

序列化之后，就可以把序列化的内容写入磁盘，或者通过网络传输到别的机器上。

相反的，把变量内存从序列化的对象重新读到内存里，叫做反序列化（unpickling）。


## pickle模块

pickle.dumps()方法把任意对象序列化成一个bytes，然后可以将这个bytes写入文件。

```python
>>> import pickle
>>> d = dict(name='nizo', age= 18, score=100)
>>> pickle.dumps(d)
b'\x80\x03}q\x00(X\x04\x00\x00\x00nameq\x01X\x04\x00\x00\x00nizoq\x02X\x03\x00\x00\x00ageq\x03K\x12X\x05\x00\x00\x00scoreq\x04Kdu.'
>>> type(pickle.dumps(d))
<class 'bytes'>
```

pickle.dump()方法直接把对象序列化后写入一个file-like Object。

```python
>>> f = open('dump.txt', 'w')             # 文件打开方式为'w'
>>> pickle.dump(d, f)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: write() argument must be str, not bytes
>>> f.close()
>>> f = open('dump.txt', 'wb')            # 文件打开方式为'wb'
>>> pickle.dump(d, f)
>>> f.close()
```

注意，此时写入的dump.txt文件里是一团乱码，这些都是Python保存的对象内部信息。不能直接读取给使用者。


pickle.loads()：当我们要把对象从磁盘读到内存时，可以先把内容读到一个butes，然后用pickle.loads()方法反序列化对象。

```python
>>> d
{'name': 'nizo', 'age': 18, 'score': 100}
>>> pickle.loads(pickle.dumps(d))
{'name': 'nizo', 'age': 18, 'score': 100}
```

pickle.load()方法从一个file-like Object中直接反序列化出对象。

```python
>>> f = open('dump.txt', 'r')
>>> d = pickle.load(f)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
UnicodeDecodeError: 'gbk' codec can't decode byte 0x80 in position 0: illegal multibyte sequence
>>> f.close()
>>> f = open('dump.txt', 'rb')
>>> d = pickle.load(f)
>>> d
{'name': 'nizo', 'age': 18, 'score': 100}
>>> f.close()
```


## JSON

不同的编程语言之间如果想要传递对象，必须把对象序列化成标准格式，如XML或JSON。

JSON表示的对象是标准的JavaScript语言的对象，JSON和Python内置数据类型对应关系如下：

| JSON类型 | Python类型 |
| ------ | ------|
| {} | dict |
| [] | list |
| "string" | str |
| 1234.56 | int或float |
| true/false | True/False |
| null | None |


## JSON模块

JSON模块提供完善的Python对象到JSON格式的转换。

json.dumps()方法返回一个str，内容是标准的JSON。

```python
>>> import json
>>> d = dict(name='nizo', age= 18, score=100)
>>> d 
{'name': 'nizo', 'age': 18, 'score': 100}
>>> json.dumps(d)
'{"name": "nizo", "age": 18, "score": 100}'
>>> type(json.dumps(d))
<class 'str'>
```

类似的，dump()方法可以直接把JSON写入一个file-like Object。

```python
>>> f = open('dump.txt', 'w')       # 文件打开方式'w'，因为JSON格式是str，所以不需要添加'b'模式
>>> json.dump(d, f)                 # 此时打开dump.txt文件，内容是标准的JSON，与上例中的json.dumps(d)返回结果一样
>>> f.close()
```

同样地，要将JSON反序列为Python对象，用loads()或load()方法：

* loads()方法把JSON的字符串反序列化

* load()方法从file-like Object中读取字符串并饭序列化

```python
>>> d
{'name': 'nizo', 'age': 18, 'score': 100}
>>> json_str = json.dumps(d)
>>> json_str
'{"name": "nizo", "age": 18, "score": 100}'
>>> type(json_str)
<class 'str'>
>>> json.loads(json_str)
{'name': 'nizo', 'age': 18, 'score': 100}
>>> type(json.loads(json_str))
<class 'dict'>
>>>
>>> f = open('C:\dump.txt', 'r')
>>> d1 = json.load(f)
>>> d1
{'name': 'nizo', 'age': 18, 'score': 100}
>>> f.close()
```


## JSON进阶

### 类对象实例序列化为JSON

```python
import json

class Student(object):
	def __init__(self, name, age, score):
		self.name = name
		self.age = age
		self.score = score
		
def student2dict(std):
	return {
		'name': std.name,
		'age': std.age,
		'score': std.score
	}
	
s = Student('nizo', 18, 100)
print(json.dumps(s, default = student2dict))
### 执行结果
{"name": "nizo", "age": 18, "score": 100}
```

将任意class的实例变成dict：

```python
print(json.dumps(s, default = lambda obj: obj.__dict__))
```

> 通常class的实例都有一个\_\_dict\_\_属性，就是一个dict，原来存储变量，当然除了定义了\_\_slots\_\_属性以外的类实例）

### JSON反序列华为一个类对象实例

```python
import json

class Student(object):
	def __init__(self, name, age, score):
		self.name = name
		self.age = age
		self.score = score
		
def student2dict(std):
	return {
		'name': std.name,
		'age': std.age,
		'score': std.score
	}

def dict2student(d):
	return Student(d['name'], d['age'], d['score'])
	
s = Student('nizo', 18, 100)
json_str = json.dumps(s, default = student2dict)
print(json_str)                                         
d = json.loads(json_str, object_hook = dict2student)
print(d)

### 执行结果
{"name": "nizo", "age": 18, "score": 100}
<__main__.Student object at 0x000000000265E358>
```

loads()方法首先转换出一个dict，然后传入的object_hook函数负责把dict转换成Student实例。


### JSON和中文

```python
>>> import json
>>>
>>> obj = dict(name = '张宁', age = 18)
>>> print(json.dumps(obj))
{"name": "\u5f20\u5b81", "age": 18}
>>> print(json.dumps(obj, ensure_ascii = True))
{"name": "\u5f20\u5b81", "age": 18}
>>> print(json.dumps(obj, ensure_ascii = False))
{"name": "张宁", "age": 18}
```


## 链接

上一节 [Chapter_043 操作文件和目录](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_043.md "Chapter_043 操作文件和目录")

下一节 [Chapter_045 进程和线程](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_045.md "Chapter_045 进程和线程")
