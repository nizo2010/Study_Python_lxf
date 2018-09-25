# StringIO和BytesIO

## StringIO 在内存中读写str

1、要把str写入StringIO，需要先创建一个StringIO，然后像写入文件一样写入

```python
>>> from io import StringIO
>>> f = StringIO()
>>> f.write('hello')
5               # 返回写入的字符串的长度
>>> f.write(' ')
1
>>> f.write('world!')
6
>>> print(f.getvalue())     # getvalue()方法由于获得写入后的str
hello world!
```


2、要读取StringIO，可以用一个str初始化StringIO，然后像读取文件一样读取

```python
>>> from io import StringIO
>>> f = StringIO('Hello!\nHi!\nGoodbye!')
>>> while True:
...     s = f.readline()
...     if s == '':
...         break
...     print(s.strip())
...
Hello!
Hi!
Goodbye!
```


## BytesIO 在内存中读写bytes

1、创建一个BytesIO，然后写入一些bytes：

```python
>>> from io import BytesIO
>>> f= BytesIO()
>>> f
<_io.BytesIO object at 0x0210DDB0>
>>> type(f)
<class '_io.BytesIO'>
>>> f.write('中文'.encode('utf-8'))           # 注意，写入的不是str，而是经过UTF-8编码的bytes
6
>>> f.getvalue()
b'\xe4\xb8\xad\xe6\x96\x87'
```

2、使用一个bytes初始化BytesIO，然后像读取文件一样读取

```python
>>> from io import BytesIO
>>> f = BytesIO(b'\xe4\xb8\xad\xe6\x96\x87')
>>> f.read()
b'\xe4\xb8\xad\xe6\x96\x87'
```


## tell()和seek()


### tell()方法：获取当前文件读写指针的位置

```python
>>> from io import StringIO
>>> s1 = StringIO()         # 初始化一个StringIO对象
>>> s1.tell()               # 返回0，指针在开头处
0
>>> s1.write('abc')         # 返回3，写入字符串的长度，此时指针在写入的字符后
3
>>> s1.tell()               # 返回3，表明指针的位置在第三个字符后
3
```


### seek()方法：

seek(offset, whence) 用于移动文件读写指针到指定的位置

* offset：偏移量，正数表示向后的字节数，负数表示向前的字节数

* whence：可选值，相对位置，默认为0，表示文件开头；1表示相对于指针当前的位置；2表示文件末尾

（注意：使用seek()方法时，若文件打开方式不包含'b'方式，whence只能是0，即只能从开头开始）

（但是使用BytesIO可以，因为BytesIO默认就是以'b'方式打开读写文件/IO流的）


## 部分示例

```python
from io import StringIO
from io import BytesIO
def outputstring():
    return 'string \nfrom \noutputstring \nfunction'

s = outputstring()

sio = StringIO()
print(sio.write(s))
print(sio.tell())
for i in sio.readlines():
    print(i.strip())

###
执行结果：
36
36
```

为什么没有打印出来想要的字符串？

因为当前读写指针是在文件的末尾，readlines()读取到的内容是空的。


```python
from io import StringIO
from io import BytesIO
def outputstring():
    return 'string \nfrom \noutputstring \nfunction'

s = outputstring()

sio = StringIO()
print(sio.write(s))
print(sio.tell())
sio.seek(0， 0）        # 将读写指针移到文件开头
for i in sio.readlines():
    print(i.strip())
    
###
执行结果：
36
36
0
string
from
outputstring
function
```

使用seek(0, 0)将读写指针移到文件开头，然后使用readlines()方法按行读取全部内容。


```python
from io import StringIO
from io import BytesIO
def outputstring():
    return 'string \nfrom \noutputstring \nfunction'

s = outputstring()

sio = StringIO()
print(sio.write(s))
print(sio.tell())
sio.seek(0, 0)
print(sio.tell())
for i in sio.readlines():
    print(i.strip())


print('**********************')

bio = BytesIO()
print(bio.write(s.encode('utf-8')))
print(bio.tell())
print(bio.seek(-36,1))               #返回0，即读写指针当前的位置
print(bio.tell())
for i in bio.readlines():
    print(i.strip())

###
运行结果：
36
36
0
b'string'
b'from'
b'outputstring'
b'function'
```
BytesIO天生就是以'b'方式打开读写数据的，因此BytesIO对象的seek()方法中的whence可以是1和2。


## 链接

上一节 [Chapter_041 文件读写](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_041.md "Chapter_041 文件读写")

下一节 [Chapter_043 操作文件和目录](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_043.md "Chapter_043 操作文件和目录")
