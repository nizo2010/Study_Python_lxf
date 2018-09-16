# Chapter_031   使用“\_\_slot\_\_”

## 给实例绑定属性和方法

```python
class Student(object):
    pass
    
s = Student()
s.name = 'nizo'               # 动态给实例绑定一个属性
print(s.name)
# 返回'nizo'

def set_age(self, age):       # 定义一个函数作为实例方法
    set.age = age
    
from types import MethodType

s.set_age = MethodType(set_age, s)    # 给实例绑定一个方法
s.set_age(25)
s.age
# 返回25
```

但是，给一个实例绑定的方法，对另一个实例是不起作用的。

```python
>>> s2 = Student()    # 创建新的实例
>>> s2.get_age(25)    # 尝试调用方法
Traceback (most recent call last):
...
AttributeError: 'Student' object has no attribute 'set_age'
```

若想要给所有实例都绑定方法，可以给类class绑定方法

```python
def set_score(self, score):
    self.score = score
    
Student.set_score = set_score
```

给class绑定方法后，所有实例均可调用：

```python
>>> s.set_score(100)
>>> s.score
100
>>> s2.set_score(99)
>>> s2.score
99
```

通常，set_score方法可直接定义在class中，但动态绑定允许在程序运行时动态地给class添加功能。

---

假设需要限制实例的属性，比如只允许对Student实例添加name和age属性。

只需要在定义class的时候，定义一个\_\_slot\_\_变量，来限制该class实例能添加的属性。

```python
class Student(object):
    __slot__ = ('name, 'age')
    
>>> s = Student()
>>> s.name = 'nizo'       # 给实例s绑定name属性
>>> s.age = 18            # 给实例s绑定age属性
>>> s.score = 100
# 报错
````

> **注意：**
>
> 使用\_\_slot\_\_时，只对当前类实例起作用，对继承的子类不起作用


```python
class HighSchoolStudent(Student):
    pass
    
h = HighSchoolStudent()
h.score = 99
h.score
# 返回99
```
