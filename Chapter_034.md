# Chapter_034   定制类

## \_\_str\_\_

\_\_str\_\_，返回用户看到的字符串

```python
class Student(object):
    pass

>>> print(Student())
<__main__.Student object at 0x1013ddb00>
>>> Student()
<__main__.Student object at 0x1013ddac8>

class Student2(object):
    def __str__(self):
        return 'Student object'
        
>>> print(Student2())
Student object
>>> Student2()
<__main__.Student2 object at 0x1013dd9b0>
```

可以看出虽然使用print()语句返回的是我们定义的字符串，但是直接调用类返回的仍然不是我们所期望的。


## \_\_repr\_\_

\_\_repr\_\_， 返回程序开发者所看到的字符串，直接显示变量调用的是\_\_repr\_\_

```python
class Student(object):
    def __str__(self):
        return 'Student object'
    __repr__ = __str__
    
>>> print(Student())
Student object
>>> Student()
Student object
```

## \_\_iter\_\_

\_\_iter\_\_，返回一个迭代对象
