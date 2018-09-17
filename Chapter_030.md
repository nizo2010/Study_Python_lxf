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
>>> s = Student()
>>> s.name                  # 1. 先查找实例是否有name属性；2. 如果实例没有name属性，再查找Student类name属性
'Student'
>>> Student.name
'Student'
>>> s.name = 'nizo'         # 给实例s绑定name属性
>>> s.name                  # 实例属性优先级高于类属性
'nizo'
>>> Student.name
'Student'
>>> del s.name              # 删除实例的name属性
>>> s.name                  # 实例的name属性删除了，类属性还在
'Student'
```


## 课后作业

统计学生人数，给Student类增加一个类属性，每创建一个实例，该属性自动增加

```python
class Student(object):
    count = 0
    def __init__(self, name):
        self.name = name
        Student.count += 1

if __name__ == '__main__':
    if Student.count != 0:
        print('fail!')
    else:
        bart = Student('Bart')
        if Student.count != 1:
            print('fail!')
        else:
            lisa = Student('Lisa')
            if Student.count != 2:
                print('fail!')
            else:
                print('Student:', Student.count)
                print('success!')
```


## 小结

1、 实例属性属于各个实例所有，互不干扰

2、 类属性属于类所有，所有属性共享一个属性

3、 不要对实例属性和类属性使用相同的名字，否则将产生难以发现的错误


## 链接

上一节 [Chapter_029 获取对象信息](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_029.md "Chapter_029 获取对象信息")

下一节 [Chapter_031 使用“__slots__”](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_031.md "Chapter_031 使用“__slots__”")
