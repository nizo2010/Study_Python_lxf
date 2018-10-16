# Chapter_052 模块dtetime

datetime是Python处理*日期和时间*的标准库。


## 获取当前日期和时间

```python
>>> from datetime import datetime
>>> now = datetime.now()
>>> print(now)
2018-10-16 16:35:24.432095
>>> print(type(now))
<class 'datetime.datetime'>
```

注意到datetime是模块，datetime模块还包含一个datetime类，通过from datetime import datetime导入的才是datetime这个类。

如果仅导入datetime模块（import datetime），则必须引用全名datetime.datetime。

datetime.now()返回当前时间和日期，其类型是datetime类。


## 获取指定日期和时间

直接使用参数构造一个datetime实例。

```python
>>> from datetime import datetime
>>> dt = datetime(2018, 12, 24, 20, 30)
>>> print(dt)
2018-12-24 20:30:00
```


## datetime转换为timestamp

在计算机中，时间实际上是用数字表示的。按照规定将1970年1月1日 00:00:00 UTC+00:00时区的时刻称为epoch time，记为0（1970年以前的时间timestamp为负数），当前时间就是相对于epoch time的秒数，称为timestamp。

可以认为timestamp的值与时区毫无关系，因为timestamp一旦确定，其UTC时间就确定了，转换到任意时区的时间也是完全确定的，这就是为什么计算机存储的当前时间是以timestamp表示的，因为全球各地的计算机在任意时刻的timestamp都是完全相同的（假定时间已校准）。

把一个datetime类型转换为timestamp只需要简单地调用timestamp()方法：

```python
>>> from datetime import datetime
>>> dt = datetime(2018, 12, 24, 20, 30)
>>> dt.timestamp()
1545654600.0
```

> 注意Python的timestamp是一个浮点数，小数位表示毫秒数。
>
> 某些编程语言（如Java和JavaScript）的timestamp使用整数表示毫秒数，这种情况下只需要把timestamp除以1000就得到Python的浮点表示方法。


## timestamp转换为datetime

要把timestamp转换为datetime，使用datetime提供的fromtimestamp()方法：

```python
>>> from datetime import datetime
>>> t = 1545654600.0
>>> print(datetime.fromtimestamp(t))
2018-12-24 20:30:00
```

> 注意到timestamp是一个浮点数，它没有时区的概念，而datetime是有时区的。上述转换是在timestamp和本地时间做转换。

本地时间是指当前操作系统设定的时区。比如北京时间是东8区，则本地时间：

> 2018-12-24 20:30:00

实际上就是UTC+08:00时区的时间：

> 2018-12-24 20:30:00 UTC+08:00

而此时的格林威治标准时间与北京时间相差了8个小时，也就是UTC+00:00时区的时间应该是

> 2018-12-24 12:30:00 UTC+00:00

timestamp也可以直接被转换到UTC标准时区的时间，使用utcfromtimestamp()方法：

```python
>>> from datetime import datetime
>>> t = 1545654600.0
>>> print(datetime.fromtimestamp(t))
2018-12-24 20:30:00
>>> print(datetime.utcfromtimestamp(t))
2018-12-24 12:30:00
```


## str转换为datetime

