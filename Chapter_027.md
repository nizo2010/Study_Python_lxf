# Chapter_027 访问限制

1、 对于上面的Student类，如果在属性name和score前加上双下划线“\_\_”，则内部属性就不能被外部访问累，成为了私有变量（private）：

```python
class Student(object):
    def __init__(self, name, score):
        self.__name = name
        self._-score = score
        
>>> bart = Student('Bart Simpson', 59)
>>> bart.__name
AttributeError: 'Student' object has no attribute '__name'
```

2、 但是如果外部还是想要访问这些私有属性怎么办？ ===> 使用方法

```python
class Student(object):
    def __init__(self, name, score):
        self.__name = name
        self.__score = score
        
    def get_name(self):
        return self.__name
        
    def get_score(self):
        return self.__score
        
>>> bart = Student('Bart Simpson', 59)
>>> bart.get_score()
59
```

3、 还有一种方法可以在外部访问带有双下划线“\_\_”前缀的私有变量。

即： \_类名\_\_私有变量名

如： bart.\_Student\_\_name

返回Bart Simpson

4、 如果在外部设置\_\_name变量，实际上该\_\_name变量与类内部的\_\_name变量**不是**同一个变量。

因为类内部的\_\_name变量已经被Python解释器自动改为\_Student\_\_name，而外部的操作相当于新增了一个\_\_name变量。

```python
>>> bart = Student('Bart Simpson', 59)
>>> bart.get_name()
'Bart Simpson'
>>> bart.__name = 'New name'
>>> bart.__name
'New name'
>>> bart._Student__name
'Bart Simpson'
>>> bart.get_name()
'Bart Simpson'
```


## 链接

上一节 [Chapter_026 类和实例](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_026.md "Chapter_026 类和实例")

下一节 [Chapter_028 继承和多态](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_028.md "Chapter_028 继承和多态")
