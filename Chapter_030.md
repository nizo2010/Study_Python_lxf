# Chapter_030   实例属性和类属性

## 给实例绑定属性的方法

给实例绑定属性的方法

1、通过实例变量绑定

2、通过self变量绑定

```python
class Student(object):
    def __init__(self, name):
        self.name = name          # 通过self变量绑定属性
        
s = Student('Bob')
s.score = 90          # 通过实例变量绑定
```

## 类属性

Student类若要绑定一个属性，可以在class中直接定义属性，归Student类所有

```python
class Student(object):
    name = 'Student'
```


## 测试上述类

```python
