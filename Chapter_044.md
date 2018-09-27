# Chapter_044 序列化

程序运行过程中，所有变量都存放在内存中，比如一个dict：

```python
d = dict(name = 'nizo', age =18, score = 100)
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
>>> d = dict(name='nizo', age=18, score=100)
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
| ------ | ------|
| [] | list |
| ------ | ------|
| "string" | str |
| ------ | ------|
| 1234.56 | int或float |
| ------ | ------|
| true/false | True/False |
| ------ | ------|
| null | None |
