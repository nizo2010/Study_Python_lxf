# Chapter_054 模块Base64

Base64是一种用64个字符来表示任意二进制数据的方法。

不知道大家有没有这样的经历，使用记事本或者Notepad++等工具打开一个exe、jpg、pdf等后缀的文件时，会显示一大堆的乱码，这是因为这些二进制文件包含很多无法显示和打印的字符。

所以，如果要让Notepad++这样的文本处理软件能处理二进制数据，就需要一个二进制到字符串的转换方法。

Base64是一种最常见的二进制编码方法。

Base64的原理很简单，首先准备一个包含64个字符的数组，数组包含A-B（26个）、a-b（26个）、0-9（10个）和+、/字符。（实际上还有一个作为垫字的"="，共65个字符）。

```python
['A', 'B', 'C', ..., 'a', 'b', 'c', ..., '0', '1', '2', ..., '+', '/']
```

转换方式分为四步：

- 第一步，将每三个字节作为一组，一共是24个二进制位。

- 第二步，将这24个二进制位分为四组，每个组有6个二进制位。

- 第三步，在每组前面加两个00，扩展成32个二进制位，即四个字节。

- 第四步，根据下表，得到扩展后的每隔字节对应的符号，这就是Base64的编码值。

|序号 | 编码 |序号 | 编码 |序号 | 编码 |序号 | 编码 |
| ---- | ----- | ---- | ---- | ---- | ----- | ---- | ---- |
| 0 | A | 16 | Q | 32 | g | 48 | w |
| 1 | B | 17 | R | 33 | h | 49 | x |
| 2 | C | 18 | S | 34 | i | 50 | y |
| 3 | D | 19 | T | 35 | j | 51 | z |
| 4 | E | 20 | U | 36 | k | 52 | 0 |
| 5 | F | 21 | V | 37 | l | 53 | 1 |
| 6 | G | 22 | W | 38 | m | 54 | 2 |
| 7 | H | 23 | X | 39 | n | 55 | 3 |
| 8 | I | 24 | Y | 40 | o | 56 | 4 |
| 9 | J | 25 | Z | 41 | p | 57 | 5 |
| 10 | K | 26 | a | 42 | q | 58 | 6 |
| 11 | L | 27 | b | 43 | r | 59 | 7 |
| 12 | M | 28 | c | 44 | s | 60 | 8 |
| 13 | N | 29 | d | 45 | t | 61 | 9 |
| 14 | O | 30 | e | 46 | u | 62 | + |
| 15 | P | 31 | f | 47 | v | 63 | / |

因此，Base64将三个字节转化成四个字节，因此Base64编码后的文本，会比原文本大出三分之一。

那这么做的好处是什么呢？

1、所有的二进制文件，都可以因此转化为可打印的文本编码，进而使用文本处理软件进行编辑；

2、能够对文本进行简单的加密。

## 示例1：演示英文单词Man如何转化成Base64编码

- 第一步,"M"、"a"、"n"的ASCII值分别为77、97、110，对应的二进制值为01001101、01100001、01101110,，将它们连成一个24位的二进制字符串为010011010110000101101110。

- 第二步，将这个24位的二进制字符串分成4组，每组6个二进制位：010011、010110、000101、101110。

- 第三步，在每组前面加两个00，扩展成32个二进制位，即四个字节：00010011、00010110、00000101、00101110。它们的十进制分别为19、22、5、46。

- 第四步，根据Base64的编码表，得到每个值对应的Base64编码，即T、W、F、u。

因此，Man的Base64编码就是TWFu。


## 字节数不足三个字节的处理方式

> 1、 两个字节的情况：将这两个字节的一共16个二进制，按照规则，转成三组，分别是6、6、4个二进制位，最后一组除了前面加两个00之外，后面也要加两个00。这样得到一个三位的Base64编码，再在末尾补上一个"="号，组成四个Base64编码、””
> 
> 比如，"Ma"这个字符串是两个字节，分别对应的ASCII值为77和97，对应的二进制值为01001101和01100001，组合起来就是0100110101100001（16位），分成三组为010011、010110、0001（分别为6个、6个和4个二进制位），按照规则添加两个00（其中最后一组在末尾也要添加两个00），得到00010011、00010110和00000100，分别对应的十进制值为19、22和4，按照Base64编码表，得到的编码分别为T、W、E，最后再补上一个"="号，因此"Ma"的Base64编码就是"TWE="。

> 2、 一个字节的情况：将这一个字节的一共8个二进制位，按照规则，转成两组，分别是6、2个二进制位，最后一组除了前面加两个00之外，后面再加四个00。这样得到一个两位的Base64编码，再在末尾补上两个"="号。
>
> 比如，"M"这个字母是一个字节，对应的ASCII值为77，二进制值为01001101，分成两组为010011、01，按照规则添加两个00（其中最后一组在末尾也要添加四个00），得到00010011、00010000，分别对应的十进制值为19和16，按照Base64编码表，得到的编码分别为T、Q，最后再补上两个"="号，因此"M"的Base64编码就是"TQ=="。

```python
>>> import base64
>>> base64.b64encode(b'Man')
b'TWFu'
>>> base64.b64encode(b'Ma')
b'TWE='
>>> base64.b64encode(b'M')
b'TQ=='
>>> base64.b64encode(b'binary\x00string')
b'YmluYXJ5AHN0cmluZw=='
>>> base64.b64decode(b'YmluYXJ5AHN0cmluZw==')
b'binary\x00string'
```

## 汉字转化为Base64编码

举例，汉字"严"如何转化成Base64编码？

注意，汉字本身可以有多种编码，比如gb2312、utf-8、gbk等，每一种编码的Base64对应值都不一样。（下面的例子都是以utf-8为例）

- 首先，"严"的utf-8编码是E4B8A5，对应的二进制就是三字节的"11100100 10111000 10100101"。

- 将这个24位的二进制字符串，按照规则分为四组"111001 001011 100010 100101"。

- 每组前加上两个00，转换成四组一共32位的二进制值"00111001 00001011 00100010 00100101"，对应十进制数为57、11、34、37，按照Base64的编码表分别为5、L、i、l。

因此，汉字"严"(utf-8编码)的Base64值就是5Lil。


## url safe

由于标准的Base64编码后可能出现字符'+'和'/'，在URL中就不能直接作为参数，所有又有一种"url safe"的base64编码，其实就是把字符'+'和'/'编程'-'和'_'

```python
>>> base64.b64encode(b'i\xb7\x1d\xfb\xef\xff')
b'abcd++//'
>>> base64.urlsafe_b64encode(b'i\xb7\x1d\xfb\xef\xff')
b'abcd--__'
>>> base64.b64decode(b'abcd++//')
b'i\xb7\x1d\xfb\xef\xff'
>>> base64.urlsafe_b64decode(b'abcd--__')
b'i\xb7\x1d\xfb\xef\xff'
```


## Base64用途

Base64是一种通过查表的编码方法，不能用于加密，即使使用自定义的编码表也不行。

Base64适用于小段内容的编码，比如数字证书签名、Cookie的内容等。

由于字符"="也可能出现在Base64编码中，但"="用在URL、Cookie里面会造成歧义，所以很多Base64编码后会把"="去掉：

```python
# *标准Base64:*
'abcd' -> 'YWJjZA=='
# *自动去掉=:*
'abcd' -> 'YWJjZA'
```

去掉"="后怎么解码呢？因为Base64是把三个字节（有时候是一个或两个）转换成四个字节，所以Base64编码的长度永远是4的倍数。

因此，如果Base64编码后的字符串长度不是4的倍数，则需要在末尾添加多个"="将长度补齐为4的倍数，就可以正常解码了。


## 课后练习

请写一个能处理去掉"="的base64编码函数：

```python
# -*- coding: utf-8 -*-
import base64
def safe_base64_decode(s):
    def get_decode_str(decodedata):
        if isinstance(decodedata, bytes):
            decodedata = decodedata.decode('utf-8')
        return decodedata

    def base64_decode(base64str):
        base64_lostlength = (4 - len(base64str) % 4) % 4
        base64str = str.ljust(base64str, len(base64str) + base64_lostlength, '=')
        return base64.b64decode(base64str)

    return base64_decode(get_decode_str(s))

print("safe_base64_decode(b'YWJjZA=='):", safe_base64_decode('YWJjZA=='))
print("safe_base64_decode(b'YWJjZA'):", safe_base64_decode('YWJjZA'))
print('ok')

#运行结果
C:\ProgramData\Anaconda3\python.exe F:/Python/xiao_xiang_python/test.py
safe_base64_decode(b'YWJjZA=='): b'abcd'
safe_base64_decode(b'YWJjZA'): b'abcd'
ok
```

## 其他

### 本地图片

```python
def convert_local_image():
    # 原始图片 ===> base64 编码
    with open('/path/to/alpha.png', 'r') as fin:
        image_data = fin.read()
        base64_data = base64.b64encode(image_data)
        
        fout = open('/path/to/base64_content_alpha.txt', 'w')
        fout.write(base64_data)
        fout.close()
        
    # base64 编码 ===> 原始图片
    with open(/path/to/base64_content_alpha.txt', 'r') as fin:
        base64_date = fin.read()
        ori_image_data = base64.b64decode(base64_data)
        
        fout = open('/path/to/beta.png', 'wb')
        fout.write(ori_image_data)
        fout.close()
```


### 网络图片

```python
import requests

def convert_web_image():
    url = 'https://raw.githubusercontent.com/reduxjs/redux/master/logo/logo.png'
    r = requests.get(url, stream = True)
    if r.status_code == 200:
        image_data = r.content
        base64_data = base64.b64encode(image_data)
        
        # 将图片的 base64 编码保存到本地文件
        with open('/path/to/base64_data.txt', 'w') as fout:
            fout.write(base64_data)
            
        # 下载图片到本地
        with open('/path/to/logo.png', 'wb') as fout:
            fout.write(image_data)
```


## 链接

上一节 [Chapter_053 模块collections](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_053.md "Chapter_051 模块collections")

下一节 [Chapter_055 模块struct](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_055.md "Chapter_053 模块struct")
