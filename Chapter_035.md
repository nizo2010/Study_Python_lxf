# Chapter_035   使用枚举类

```python
from enum import Enum

Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))
```

Month的枚举类，介意直接使用Month.Jan来引用一个常量，或者枚举它的的所有成员：

```python
for name, member in Month.__members__.items():
    print(name, '--->', member, ',', member.value)
    
Jan ---> Month.Jan , 1
Feb ---> Month.Feb , 2
Mar ---> Month.Mar , 3
Apr ---> Month.Apr , 4
May ---> Month.May , 5
Jun ---> Month.Jun , 6
Jul ---> Month.Jul , 7
Aug ---> Month.Aug , 8
Sep ---> Month.Sep , 9
Oct ---> Month.Oct , 10
Nov ---> Month.Nov , 11
Dec ---> Month.Dec , 12
```

value属性则是自动赋给成员的int常量，默认从1开始计算。

---

```python
from enum import Enum, unique

@unique         # @unique装饰器可以帮助检查保证没有重复值
class Weekday(Enum):
    Sun = 0
    Mon = 1
    Tue = 2
    Wed = 3
    Thu = 4
    Fri = 5
    Sat = 6
    
>>> day1 = Weekday.Mon
>>> print(day1)
Weekday.Mon
>>> print(Weekday.Tue)
Weekday.Tue
>>> print(Weekday['Tue'])
Weekday.Tue
>>> print(Weekday.Tue.value)
2
>>> print(Weekday(1))
Weekday.Mon
>>> Weekday(7)
Traceback (most recent call last):
  ...
ValueError: 7 is not a valid Weekday
>>> for name, member in Weekday.__members__.items():
...     print(name, '=>', member)
...
Sun => Weekday.Sun
Mon => Weekday.Mon
Tue => Weekday.Tue
Wed => Weekday.Wed
Thu => Weekday.Thu
Fri => Weekday.Fri
Sat => Weekday.Sat
```

---

```python
from enum import Enum, unique

@unique
class Gender(Enum):
    Male, Female = 0, 1

class Student(object):
    def __init__(self, name, gender):
        self.name, self.gender = name, gender

>>> bart = Student('Bart', Gender.Male)
>>> bart.gender == Gender.Male
True
>>> bart.gender
<Gender.Male: 0>
>>> print(bart.gender)
Gender.Male
>>> bart.gender.value
0
>>> print(bart.gender.value)
0
```
