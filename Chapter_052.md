# Chapter_052 模块datetime

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

正常情况下，用户输入的日期和时间是以字符串形式的，比如“2018-12-24 20:300:00”，要处理日期和时间，首先需要把str转换为datetime。转换方法是通过datetime.strptime()方法实现，同时还需要传入一个日期和时间的格式化字符串。

```python
>>> from datetime import datetime
>>> cday = datetime.strptime('2018-12-24 20:30:00', '%Y-%m-%d %H:%M:%S')
>>> print(cday)
2018-12-24 20:30:00
>>> print(type(cday))
<class 'datetime.datetime'>
```

字符串‘%Y-%m-%d %H:%M:%S’规定了日期和时间部分的格式。详细的说明可以参考Python文档。

> 注意转换后的datetime是没有时区信息的。


## datetime转换为str

相反的，如果已有datetime对象，要将其格式化为字符串后输出给用户，就需要转换为str，转换方法是通过strftime()方法实现的，相应地同时也需要一个日期和时间的格式化字符串：

```python
>>> from datetime import datetime
>>> now = datetime.now()
>>> print(now)
2018-10-16 17:15:04.970770
>>> print(now.strftime('%a, %b %d %H:%M:%S'))
Tue, Oct 16 17:15:04
```


## datetime加减

对日期和时间进行加减实际上就是把datetime往后或往前计算，得到新的datetime。加减可以直接使用 + 和 - 运算符，不过需要导入timedelta这个类。

```python
>>> from datetime import datetime,timedelta
>>> now = datetime.now()
>>> now
datetime.datetime(2018, 10, 16, 17, 18, 49, 141487)
>>> now + timedelta(hours=10)
datetime.datetime(2018, 10, 17, 3, 18, 49, 141487)
>>> now - timedelta(days=1)
datetime.datetime(2018, 10, 15, 17, 18, 49, 141487)
>>> now + timedelta(days=2,hours=8)
datetime.datetime(2018, 10, 19, 1, 18, 49, 141487)
```


## 本地时间转换为UTC时间

本地时间是指系统设定时区的时间，例如北京时间是UTC+8:00时区的时间，而UTC时间指UTC+0:00时区的时间。

一个datetime类型有一个时区属性tzinfo，但是默认为None，所以无法区分这个datetime到底是哪个时区，除非强行给datetime设置一个时区：

```python
>>> from datetime import datetime,timedelta,timezone
>>> tz_utc_8 = timezone(timedelta(hours=8)) # 创建时区UTC+08:00
>>> now = datetime.now()
>>> now
datetime.datetime(2018, 10, 16, 17, 24, 10, 35645)
>>> dt = now.replace(tzinfo=tz_utc_8) # 强制设置为UTC+08:00
>>> dt
datetime.datetime(2018, 10, 16, 17, 24, 10, 35645, tzinfo=datetime.timezone(datetime.timedelta(0, 28800)))
```

> 如果系统时区恰好是UTC+08:00，那么上述代码就是正确的，否则，不能强制设置位UTC+08:00时区。


## 时区转换

可以通过utcnow()获取当前的UTC时间，再转换为任意时区的时间：

```python
>>> from datetime import datetime,timedelta,timezone
>>> utc_dt = datetime.utcnow()    # 获取UTC时间
>>> print(utc_dt)
2018-10-16 09:30:10.811217
>>> utc_dt = datetime.utcnow().replace(tzinfo=timezone.utc)   # 获取UTC时间，并强制设置时区为UTC+00:00
>>> print(utc_dt)
2018-10-16 09:30:50.011941+00:00
>>> bj_dt = utc_dt.astimezone(timezone(timedelta(hours=8)))   # astimezone()将转换时区为北京时间
>>> print(bj_dt)
2018-10-16 17:30:50.011941+08:00
>>> tokyo_dt = utc_dt.astimezone(timezone(timedelta(hours=9)))  # astimezone()将转换时区为东京时间
>>> print(tokyo_dt)
2018-10-16 18:30:50.011941+09:00
>>> tokyo_dt2 = bj_dt.astimezone(timezone(timedelta(hours=9)))  # astimezone()将bj_dt转换时区为北京时间
>>> print(tokyo_dt2)
2018-10-16 18:30:50.011941+09:00
```

时区转换的关键在于，拿到一个datetime时，要获知其正确的时区，然后强制设置时区，作为基准时间。

利用带时区的datetime，通过astimezone()方法，可以转换到任意时区。

> 注：不是必须从UTC+0:00时区转换到其他时区，任何带时区的datetime都可以正确转换，例如上述bj_dt到tokyo_dt的转换。


## 小结

datetime表示的时间需要时区信息才能确定一个特定的时间，否则只能视为本地时间。

如果要存储datetime，最佳方法是将其转换为timestamp再存储，因为timestamp的值与时区完全无关。


## 课后练习

假设你获取了用户输入的日期和时间如2015-1-21 9:01:30，以及一个时区信息如UTC+5:00，均是str，请编写一个函数将其转换为timestamp：

```python
from datetime import datetime, timezone, timedelta

import re

def to_timestamp(dt_str, tz_str):
	dt = datetime.strptime(dt_str, '%Y-%m-%d %H:%M:%S')   # 字符串转换成datetime
	print(dt)
	m = re.match(r'^UTC([+-]\d+):00$', tz_str)            # 截取时区数字
	print(m.group(1))                                     # 获取匹配到([+-]\d+)的结果，即时区数字，比如+9，比如-7
	tz = timezone(timedelta(hours=int(m.group(1))))       # 创建时区
	print(tz)
	utc_dt = dt.replace(tzinfo=tz)
	print(utc_dt)
	print(utc_dt.timestamp())
	
to_timestamp('2015-6-1 08:10:30', 'UTC+7:00')
to_timestamp('2015-5-31 16:10:30', 'UTC-9:00')
	
###运行结果：
2015-06-01 08:10:30
+7
UTC+07:00
2015-06-01 08:10:30+07:00
1433121030.0
2015-05-31 16:10:30
-9
UTC-09:00
2015-05-31 16:10:30-09:00
1433121030.0
```


## 链接

上一节 [Chapter_051 正则表达式](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_051.md "Chapter_051 正则表达式")

下一节 [Chapter_053 模块collections](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_053.md "Chapter_053 模块collections")
