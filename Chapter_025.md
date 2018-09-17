# Chapter_025   面向对象编程OOP（Object Oriented Programming）

1、 OOP把对象作为程序的基本单元，一个对象包含了数据和操作数据的函数

2、面向对象的程序设计把计算机程序视为一组对象的集合，而每个对象都可以接收其他对象发来的消息，并处理这些消息，计算机程序的执行就是一系列消息在各个对象之间传递

3、举例：

1） 面向过程：（处理学生成绩表）
```python
std1 = {'name': 'Micheal', 'score': 98}
std2 = {'name': 'Bob', 'score': 81}
def print_score(std):
    print('%s: %s' % (std['name'], std['score']))
```

2） 面向对象：（首先思考的不是程序的执行流程，而是student这种数据类型应该被视为一个对象，其拥有name和score两个属性Property）
```python
class Student(object):
    def __init__(self, name, score):
        self.name = name
        selr.score = score
    def print_score(self):
        print('%s: %s' % (self.name, self.score))
        
bart = Student('Bart Simpson', 59)
lisa = Student('Lisa Simpson', 87)
bart.print_score()
# 返回Bart Simpson: 59
lisa.print_score()
# 返回Lisa Simpson: 87
```

给对象发消息实际上就是调用对象对应的关联函数，称之为对象的方法（Method）。

4、类（Class）和实例（Instance）

Class是一种抽象概念，，比如定义的Student，是指的学生这个概念

而Instance则是一个个具体的Student，比如 Bart Simpson 和 Lisa Simpson 是两个具体的Student

面向对象的思想是抽象出类Class，根据类创建出实例Instance


## 链接

上一节 [Chapter_024 安装第三方模块](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_024.md "Chapter_024 安装第三方模块")

下一节 [Chapter_026 类和实例](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_026.md "Chapter_026 类和实例")
