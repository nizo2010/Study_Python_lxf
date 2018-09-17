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

如果一个类想被用于 for...in... 循环，类似于list或tuple那样，就必须实现一个 \_\_iter\_\_()方法，然后 Python 的 for 循环就会不断调用该迭代对象的 \_\_next\_\_() 方法拿到循环的下一个值，直到遇见 StopIteration 错误时退出循环。

```python
class Fib(object):
    def __init__(self):
        self.a, self.b = 0, 1       # 初始化两个计数器a和b
    def __iter__(self):
        return self                 # 实例本身就是迭代对象，故返回自己
    def __next__(self):
        self.a, self.b = self.b, self.a + self.b        # 计算下一个值
        if self.a > 100:
            raise StopIteration()
        return self.a

>>> for n in Fib():
...     print(n)
...
1
1
2
3
5
8
13
21
34
55
89
```

## \_\_getitem\_\_

\_\_getitem\_\_，返回按下标取出元素

Fib实例虽然能作用于for循环，看起来和list有点像，但是把它当做list来使用还是不行，比如取出第5个元素

```python
>>> Fib(5)
Traceback (most recent call last):
  ...
TypeError: 'Fib' object does not support indexing
```

要表现得像list那样按照下标取出元素，需要实现\_\_getitem\_\_()方法

```python
class Fib(object):
    def __getitem__(self, n):
        a, b = 1, 1
        for x in range(n):
            a, b = b, a + b
        return a

>>> Fib()[0]
1
>>> Fib()[1]
1
>>> Fib()[2]
2
>>> Fib()[10]
89
```

然而，list还有个神奇的切片方法

```python
>>> list(range(100))[5:10]
[5, 6, 7, 8, 9]
```

对于Fib却报错

```python
>>> Fib()[5:10]
Traceback (most recent call last):
  ...
TypeError: 'slice' object cannot be interpreted as an integer
```

因为\_\_getitem\_\_()传入的参数可能是一个int，也可能是一个切片对象slice，因此需要对参数进行判断。

```python
class Fib(object):
    def __getitem__(self, n):
        if isinstance(n, int):          # n是索引
            a, b = 1, 1
            for x in range(n):
                a, b = b, a + b
            return a
        
        if isinstance(n, slice):        # n是切片
            start = n.start
            stop = n.stop
            if start is None:
                start = 0
            a, b = 1, 1
            L = []
            for x in range(stop):
                if x >= start:
                    L.append(a)
                a, b = b, a + b
            return L
            
>>> Fib()[0:5]
[1, 1, 2, 3, 5]
>>> Fib()[:10]
[1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
```

> ***但是，仍然没有对step参数，以及负数切片进行处理。***

对应的还有

> ___\_\_setitem\_\_()方法，将对象视为list或dict来对集合赋值___
>
> ___\_\_delitem\_\_()方法，用于阿喊出某个元素___


## \_\_getattr\_\_

\_\_getattr\_\_()方法，获取属性

```python
class Student(object):
    def __init__(self):
        self.name = 'nizo'
    def __getattr__(self, attr):
        if attr == 'score':              # 属性score
            return 99
        if attr == 'age':                # 函数age
            return lambda: 25
        raise AttributeError('\'Student\' object has no attribute \'%s\'' % attr)
        
>>> s = Student()
>>> s.name
nizo
>>> s.score
99
>>> s.age()
25
>>> s.grade()
Traceback (most recent call last):
  ...
Attribute: 'Student' object has no attribute 'grade'
```


## \_\_call\_\_

\_\_call\_\_()方法，直接对实例本身进行调用

```python
class Student(object):
    def __init__(self, name):
        self.name = name
    def __call__(self):
        print('My name is %s.' * self.name)
        
>>> s = Student('nizo')
>>> s()             # 直接调用实例本身，调用了\_\_call\_\_()方法
My name is nizo.
```

能被调用的对象就是一个callable对象

```python
>>> callable(Student)
True
>>> callable(Student('nizo'))
True
>>> callable(max)
True
>>> callabnle([1, 2, 3])
False
>>> callable(None)
False
>>> callable('str')
False
```

## 例子：Chain

```python
class Chain(object):
    def __init__(self, path = ''):
        self._path = path
    def __getattr__(self, path):
        return chain('%s/%s' % (self._path, path))
    def __call__(self, path = ''):
        return Chain('%s/%s' % (self._path, path))
    def __str__(self):
        return self._path
    __repr__ = __str__
    
>>> Chain().status.user.timeline.list
/status/user/timeline/list
>>> Chain().user('nizo').repos
/user/nizo/repos
```

Chain().status.user.timeline.list

> 1） Chain是类名，对它进行“()”运算，即调用\_\_init\_\_(self)生成一个实例c1，c1 = Chain(path = '')；
> 
> 2） 对c1进行“.”运算，增加一个status属性，即调用\_\_getattr\_\_(self, status)返回实例c2，c2 = Chain(path = '\status')；
> 
> 3） 以此类推，增加user属性、timeline属性、list属性，最终返回的实例c = Chain(path = '\status\user\timeline\list')；
> 
> 4） 最后进行“回车键”运算，调用\_\_repr\_\_(self)返回实例c.\_path，即'\status\user\timeline\list'。

---

Chain().user('nizo').repos

> 1） Chain是类名，对它进行“()”运算，调用\_\_init\_\_(self)生成一个实例c1，c1 = Chain(path = '')；
> 
> 2） 对c1进行“.”运算，增加一个user属性，调用\_\_getattr\_\_(self, user)返回实例c2，c2 = Chain(path = '\user')；
> 
> 3） 对c2进行“('nizo')”运算，调用\_\_call\_\_(self, 'nizo')返回c3，c3 = Chain(path = '\user\nizo')；
> 
> 4） 对c3进行“.”运算，增加一个repos属性，调用\_\_getattr\_\_(self, repos)返回实例c4，c4 = Chain(path = '\user\nizo\repos')；
> 
> 5） 最后对c4进行“回车”运算，调用\_\_repr\_\_(self)返回实例c4.\_path，即'\user\nizo\repos'。


## 链接

上一节 [Chapter_033 多重继承](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_033.md "Chapter_033 多重继承")

下一节 [Chapter_035 使用枚举类](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_035.md "Chapter_035 使用枚举类")
