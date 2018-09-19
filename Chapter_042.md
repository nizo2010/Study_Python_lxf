# StringIO和BytesIO

## StringIO





















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
