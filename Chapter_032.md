# Chapter_032   使用@property

1、 对实例进行绑定属性，会直接将属性暴露出来，虽然简单，但没办法检查参数合理性
```python
s = Student()
s.score = 9999      # 分数可以随意设置，显然不合理
```

---

2、 方法一：

在定义class中，通过一个set_score方法来设置成绩，get_score方法来获取成绩

```python
class Student(object):
    def set_score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')     # 抛出异常
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value
        
    def get_score(self):
        return self._score
        
>>> s = Student()
>>> s.set_score(60)
>>> s.get_score()
60
>>> s.set_score(9999)
Traceback (most recent call last):
  ...
ValueError: score must between 0 ~ 100!
>>> s.set_score('nizo')
Traceback (most recent call last):
  ...
ValueError: score must be an integer!
```

这样，对任意的Student实例进行操作，就不能不受限制的设置score了。

---

3、 Python内置的@property装饰器，可以将一个类方法变成属性调用。

```python
class Student(object):
    @property       # 将一个getter方法变成属性，只需要加上@property
    def score(self):
        return self._score
        
    @score.setter   # 创建一个@score.setter，将一个setter方法变成属性
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value
    
>>> s = Student()
>>> s.score = 60    # 实际转化为s.set_score(60)
>>> s.score         # 实际转化为s.get_score()
60
>>> s.score = 9999
Traceback (most recent call last):
  ...
ValueError: score must between 0 ~ 100!
```

这里@property，在对实例属性操作的时候，就知道该属性很可能不是直接暴露的，而是通过getter和setter方法来实现。

---

4、 如果定义setter和getter方法，那么这个属性就是可读写的，而只定义getter方法，不定义setter方法就是一个只读属性

```python
class Student(object):
    @property
    def biith(self):
        return self._brith
        
    @birth.setter
    def birth(self, value):
        self._birth = value
        
    @property
    def age(self):
        return 2018 - self._birth
```

上面的*birth*是**可读写**属性，而*age*就是一个**只读**属性。


## 链接

上一节 [Chapter_031 使用“\_\_slots\_\_”](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_031.md "Chapter_031 使用“__slots__”")

下一节 [Chapter_033 多重继承](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_033.md "Chapter_033 多重继承")
