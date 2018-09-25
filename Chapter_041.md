# 文件读写


## 读文件

Python内置的open()函数，传入文件名和标识符：

```python
>>> f = open('/User/michael/test.txt', 'r')      # 前一个参数表示文件的路径和文件名，后一个参数r表示read-only只读
>>> type(f)
<type 'file'>         # 文件对象
>>> f
<open file '/User/michael/test.txt', mode 'r' at 0x7f9e2745bc90>
```

倘若文件不存在，open()函数就会抛出一个IOErrpr的错误，并且给出错误码和详细的信息说明文件不存在

```python
>>> f = open('/User/michael/test.txt', 'r')
Traceback (most recent call last):
  ...
IOError: [Errno 2] No such file or directory: '/User/michael/test.txt'
```

而如果成功打开文件后，可以通过调用read()方法一次性读取文件的全部内容，而Python是将这些内容督导内存，用一个str对象表示：

```python
>>> f.read()
'Hello, world!\n'
```

注意，最后一步都是调用close()方法关闭文件。（文件使用完毕后必须关闭，因为文件对象会占用操作系统的资源，并且操作系统同一时刻能打开的文件数量也是有限的）

```python
>>> f.close()
>>> type(f)
<type 'file'>         # 文件对象
>>> f
<closed file '/User/michael/test.txt', mode 'r' at 0x7f9e2745bc90>
```

## with语句

由于文件读写时都有可能产生IOError，一旦出现错误，最后的f.close()就不会调用。

因此为了保证无论是否出错都能正确关闭文件，有两种方法


### try ... finally

```python
try:
    f = open('/path/to/file' ,'r')
    print(f.read())
finally:
    if f:
        f.cloes()
```

上面的方案有一个缺点就是每次打开文件都要写一遍try ... finally，太繁琐。


### with语句

Python引入了with语句来自动调用close()方法

```python
with open('/path/ti/file' ,'r') as f:
    print(f.read())
```

这个与之前的try ... finally是等价的，但是代码更加简洁，并且不必调用f.close()方法。


## read() readline() readlines()

调用read()会一次性读取文件的全部内容，如果文件非常大（可能撑爆内存），就不能使用了。

此时可以反复调用read(size)方法，表示每次最多读取size个字节的内容。

此外，readline()可以每次读取一行内容。

readlines()一次读取所有内容并以liser的方式按行返回内容。

1、如果文件很小，read()一次性读取最方便；

2、如果不能确定文件大小，反复调用read(size)比较保险；

3、如果是配置文件，调用readlines()最方便。

```python
for line in f.readlines():
    print(line.strip())   # strip()主要用来删除每行末尾的换行符'\n'
```


## file-like Object

像open()函数返回的这种有个read()方法的对象，在Python中统称为file-like Object。除了file以外，还可以是内存的字节流，网络流，自定义流等。

file-like Object不要求从特定的类继承，只要写个read()方法就行。

StringIO就是在内存中创建的file-like Object，常用作临时缓存。


## 二进制文件

以上说的默认都是读取文本文件，并且都是UTF-8编码的文本文件。要读取二进制文件，比如图片、适配等等，用'rb'模式打开文件

```python
>>> f = open('/zhangning/test.jpg', 'rb')
>>> f.read()
'\xff\xd8\xff\xe0\x00\x10JFIF\x00\x01\x01\x01\x00`\...\xff\xd9'     # 十六进制表示的字节
```


## 字符编码

要读取非UTF-8编码的文本文件，需要给open()函数传入encoding参数，例如，读取GBK编码的文件：

```python
>>> f = open('/Users/michael/gbk.txt', 'r', encoding='gbk')
>>> f.read()
'测试'
```

如果是一些编码不规范的文件，可能会遇到UnicodeDecideError，因为文本文件中可能夹杂了一些非法编码的字符。这种情况下，open()函数还接收一个errors参数，表示如果遇到编码错误后如何处理。

最简单的方式是直接忽略：

```python
>>> f = open('/Users/michael/gbk.txt', 'r', encoding='gbk', errors='ignore')
```


## 写文件

写文件和读文件是一样的，只是在调用open()函数时，传入标识符'w'或'wb'表示写文本文件或二进制文件：

```python
>>> f = open('/Users/michael/test.txt', 'w')
>>> f.write('Hello, world!')
>>> f.close()
```

可以反复调用write()方法来写入文件，但是最终务必要调用f.close()来关闭文件。

（当写文件的时候，操作系统往往不会立刻将数据写入磁盘，而是放到内存中缓存起来，空闲的时候再慢慢写入。只有调用f.close()方法时，操作系统才保证把没有写入的数据全部写入磁盘。）

如果没有调用close()的后果就是数据可能只写了一部分到磁盘，而其他的未写入数据就丢失了。所以，使用with语句来保险：

```python
with open('/Users/michael/test.txt', 'w') as f:
    f.write('Hello, world!')
```

1、要写入特定编码的文本文件，可以向open()函数传入encoding参数，将字符串自动转换成指定编码；

2、以'w'模式写入文件时，如果文件已存在，会直接覆盖（相当于删除原文件后重新创建一个同名的新文件）；

3、以'wa'模式写入文件时，追加方式(append)


## 链接

上一节 [Chapter_040 文档测试](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_040.md "Chapter_040 文档测试")

下一节 [Chapter_042 StringIO和BytesIO](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_042.md "Chapter_041 StringIO和BytesIO")
