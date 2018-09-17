# Chapter_026   类和实例

先看一个例子，还是以Student类为例：

```python
class Student(object):
    pass
    
bart = Student()
bart
# 返回 <__main__.Student object ax 0xACBD....>     实例是有内存地址的 
Student
# 返回 <class '__main__.Student'>    类是没有内存地址的
```

## 给实例绑定变量

```python
bart.name = 'Bart Simpson'
bart.name
# 返回Bart Simpson
```

## \_\_init\_\_方法

\_\_init\_\_方法，可以在创建实例中，绑定必要的属性

```python
__init__(self, property1, property2, ...):
```

**注意：\_\_init\_\_方法的第一个参数永远是self**


## 数据封装

在上一章节的Student类中，每个实例拥有格子的name和score这些数据，那么访问这些数据没有必要从外部函数访问，可以在类的内部定义访问数据的函数，这样就把“数据”给封装起来累。

这些封装数据的函数是和Student类本身是关联起来的，称之为类的方法。

## 小结

1、 类是创建实例的模版，而实例则是一个一个具体的对象，各个实例拥有的数据相互独立，互不影响

2、 方法就是与实例绑定的函数，和普通函数不同，方法可以直接访问实例的数据

3、 通过在实例上调用方法，就直接操作对象内部的数据，但无需知道方法内部的实现细节

4、 和静态语言不同，Python 允许对实例变量绑定任何数据，也就是说对于两个实例变量，虽然它们是同一个类的不同实例，但拥有的变量名称都可能不同

```python
>>> bart = Student()
>>> lisa = Student()
>>> bart.age = 8
>>> bart.age
8
>>> lisa.age
Traceback (most recent call last):
...
AttributeError: 'Student' object has no attribute 'age'
```


## 链接

上一节 [Chapter_025 面向对象编程OOP（Object Oriented Programming）](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_025.md "Chapter_025 面向对象编程OOP（Object Oriented Programming）")

下一节 [Chapter_027 访问限制](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_027.md "Chapter_027 访问限制")
