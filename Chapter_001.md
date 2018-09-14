# Chapter 数据类型和变量

## 空值

空值是Python里一个特殊的值，用None表示。None不能理解为0，因为0是有意义的。而None是一个特殊的空值。


## 变量
理解变量在计算机内存的表示非常重要。
当 a = 'ABC'，
Python干了两件事：
1、在内存中创建了一个 'ABC' 的字符串；
2、在内存中创建了一个名为 a 的变量，并把它指向 'ABC'。

而如果是如下代码，那么变量 b 的内容是什么？
```Python
a = 'ABC'
b = a
a = 'XYZ'
print(b)
```
运行一下，代码结果是不是打印 'ABC' 呢？

分析一下：
a = 'ABC'，此时内存中创建一个 'ABC' 字符串，再创建一个变量 a ，将 a 指向 'ABC' ;
b = a，此时创建一个变量 b ，将 b 也指向 'ABC';
a = 'XYZ'，此时将变量 a 重新指向 'XYZ' ，而变量 b 仍然指向原来的 'ABC'。


## 字符编码
### utf-8
可变长编码，将一个Unicode字符根据不同的数字大小编码城1-6个字节。
常用英文字母被编码成1个字节，汉字通常是3个字节，只有很生僻的字符编码成4-6个字节。

```Python
encode('utf-8')
encode('ascii')
```
将 str 字符串(unicode)编码成 utf-8/ascii 字节

```Python
decode('utf-8')
decode('ascii')
```
将 utf-8/ascii 字节解码为 str 字符串(unicode)


## 格式化
2种格式化方式：
```Python
'Hi, %s, you are a good %s!' % ('Nizo2010', 'boy')
#输出为'Hi, Nizo2010, you are a good boy!'
```

format：
```Python
'Hello, {0}, you have {1:.2f}m tall.'.format('Nizo2010', 1.785)
#输出为'Hello, Nizo2010, you have 1.78m tall.'
```

