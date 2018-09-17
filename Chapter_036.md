# Chapter_036   使用元类

> 动态语言和静态语言最大的不同，就是函数和类的定义，不是编译时定义的，而是运行时动态创建的

---

## type()

type()，查看一个类型或变量的类型

假设有hello.py模块

```python
# hello.py
class Hello(object):
    def hello(self, name = 'world'):
        print('Hello, %s' % name)
        
```

载入模块

```python
from hello import Hello     # 载入hello模块，依次执行该模块的所有语句，直接结果就是动态创建出一个Hello的class对象

h = Hello()
h.hello()
# 返回'Hello, world'
print(type(Hello))
# Hello是一个class，它的类型是type()，返回<class 'type'>
print(type(h))
# h是一个实例，它的类型是class Hello，返回<class 'hello.Hello'>
```

---

由此引出，class的定义是运行时动态创建的，创建class的方法就是使用type()函数

> **type()函数既可以返回一个对象的类型，又可以创建出新的类型**

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

def fn(self, name='world'):         # 先定义函数
    print('Hello, %s.' % name)

Hello = type('Hello', (object,), dict(hello=fn)) # 创建Hello class

h = Hello()
print('call h.hello():')
h.hello()
print('type(Hello) =', type(Hello))
print('type(h) =', type(h))
```

传入的三个参数：

*1、 class的名称，字符串'Hello'*

*2、 继承的父类集合，因为是集合，所以用(object, )   \<-----注意逗号*

*3、 class的方法名称与函数绑定，使用dict的方式绑定，这里把函数fn绑定到方法名hello上*

执行结果为

```python
call h.hello()
Hello, world.
type(Hello) =  <class 'type'>
type(h) = <class 'type '__main__.Hello'>
```

> 通过type()函数创建出来的类和直接写class是完全一样的，因为Python解释器遇到class定义时，仅仅是扫描一下class定义的语法，然后调用type()函数筹建处class。


## metaclass：元类

先定义metaclass，就可以创建类，最后创建实例。

metaclass允许创建类或者修改类，可以把类看作是metaclass创建出来的“实例”。

```python
class ListMetaclass(type):              # 定义metaclass类，类名以Metaclass结尾，名称为ListMetaclass，必须从'type'类派生
    def __new__(cls, name, bases, attrs):
        attrs['add'] = lambda self, value: self.append(value)
        return rype.__new__(cls, name, bases, attrs)
        
class MyList(list, metaclass = ListMetacalss):          # 使用ListMetaclass定制类
    pass
```

当我们传入关键词参数metaclass时，它指示Python解释器在创建Mylist时，要通过ListMetaclass.\_\_new\_\_()来创建。在此，我们可以修改类的定义，比如，加上新的方法，然后，返回修改后的定义。

\_\_new\_\_()方法接收到的参数依次为：

1） 当前准备创建的类的对象；

2） 类的名字；

3） 类继承的父类集合；

4） 类的方法集合。

测试一下MyList是否可以调用add()方法：

```python
L = MyList()
L.add(1)
L
# 返回[1]
L.add(2)
L.add(3)
L.add('END')
L
# 返回[1, 2, 3, 'END']
```

而普通的list没有add()方法：

```python
>>> L2 = list()
>>> L2.add(1)
Traceback (most recent call last):
  ...
AttributeError: 'list' object has no attribute 'add'
```

但是动态修改有什么意义？直接在MyList定义中写上add()方法不是更简单吗？正常情况下，确实应该直接写。

---

## ORM

总会遇到需要通过metaclass修改类定义的。ORM就是一个典型的例子。

ORM：Object Relational Mapping，即对象-关系映射，就是把关系数据库的一行映射成一个对象，也就是一个类对应一个表，这样写代码更简单，不用直接操作SQL语句。

要编写一个ORM框架，所有的类定义只能动态定义，因为只有使用者才能根据表的结构定义出对应的类来。

尝试写一个ORM框架。

编写底层模块的第一步，就是先把调用接口写出来。比如使用者如果使用这个ORM框架，想定义一个User类来操作对应的数据库表User：

```python
Class User(Model):
    # 定义类的属性到列的映射
    id = IntegerField('id')
    name = StringField('username')
    email = StringField('email')
    password = StringField('password')
    
# 创建一个实例
u = User(id = 12345, name = 'nizo', email = 'test@orm.org', password = 'mypasswrd@123')
# 保存到数据库
u.save()
```

其中，父类Model和属性类型StringField、IntegerField是由ORM框架提供的，剩下的方法比如save()全部由metaclass自动完成。虽然metaclass的编写会比较复杂，但ORM的使用者用起来却异常简单。

现在我们按照上面的接口来实现该ORM。

1） 首先来定义Field类，它负责保存数据库表的字段名和字段类型：

```python
class Field(object):
    def __init__(self, name, column_type):
        self.name = name
        self.colume_type = culume_type
        
    def __str__(self):
        return '<%s: %s>' % (self.__class__.__name__, self.name)
        
```

2 ）在Field的基础上，进一步派生出各种类型的Field子类，比如StringField，IntegerField等：

```python
class StringField(Field):
    def __init__(self, name):
        super(StringField, self).__init__(name, 'varchar(100)')
        
class IntegerField(Field):
    def __init__(self, name):
        super(IntegerField, self).__init__(name, 'bigint')

```

3） 下一步，就是编写最复杂的ModelMetaclass：

```
class ModelMetaclass(type):
    def __new__(cls, name, bases, attrs):
        if name=='Model':
            return type.__new__(cls, name, bases, attrs)
        print('Found model: %s' % name)
        mappings = dict()
        for k, v in attrs.items():
            if isinstance(v, Field):
                print('Found mapping: %s ==> %s' % (k, v))
                mappings[k] = v
        for k in mappings.keys():
            attrs.pop(k)
        attrs['__mappings__'] = mappings                        # 保存属性和列的映射关系
        attrs['__table__'] = name                               # 假设表名和类名一致
        return type.__new__(cls, name, bases, attrs)
        
```

4） 以及基类Model：
```python
class Model(dict, metaclass=ModelMetaclass):
    def __init__(self, **kw):
        super(Model, self).__init__(**kw)

    def __getattr__(self, key):
        try:
            return self[key]
        except KeyError:
            raise AttributeError(r"'Model' object has no attribute '%s'" % key)

    def __setattr__(self, key, value):
        self[key] = value

    def save(self):
        fields = []
        params = []
        args = []
        for k, v in self.__mappings__.items():
            fields.append(v.name)
            params.append('?')
            args.append(getattr(self, k, None))
        sql = 'insert into %s (%s) values (%s)' % (self.__table__, ','.join(fields), ','.join(params))
        print('SQL: %s' % sql)
        print('ARGS: %s' % str(args))
        
```

当用户定义一个class User(Model)时，Python解释器首先在当前类User的定义中查找metaclass关键字，如果没有找到，就继续在父类Model中查找，本例中在父类Model中找到了，就使用Model中定义的metaclass的ModelMetaclass来创建User类。（**也就是说metaclass可以隐式地继承到子类，但子类自己却感觉不到**）

> 在ModelMetaclass中，一共做了几件事情：
>
>1. 排除掉Model类的修改；
>
>2. 在当前类（比如User）中查找定义的类的所有属性，如果找到一个Field属性，就把它保存到一个\_\_mappings\_\_的dict中，同时从类属性中删除该Field属性，否则容易造成运行时错误（实例的属性会覆盖类的同名属性）；
>
>3. 把表名保存到\_\_table\_\_中，这里简化为表名默认为类名。

在Model类中，就可以定义各种操作数据库的方法，比如save()，delete()，find()，update()等等。

这里实现了save()方法，把一个实例保存到数据库中。因为有表名，属性到字段的映射和属性值的集合，就可以构造出INSERT语句。

```python
u = User(id = 12345, name = 'nizo', email = 'test@orm.org', password = 'mypasswrd@123')
u.save()
```

输出如下：

```python
Found model: User
Found mapping: id ==> <IntegerField:id>
Found mapping: name ==> <StringField:name>
Found mapping: email ==> <StringField:email>
Found mapping: password ==> <StringField:password>
SQL: insert into User (password,email,username,id) values (?,?,?,?)
ARGS: [123456, 'nizo', 'test@orm.org', 'mypassword@123']
```


## 链接

上一节 [Chapter_035 使用枚举类](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_035.md "Chapter_035 使用枚举类")

下一节 [Chapter_037 错误处理](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_037.md "Chapter_037 错误处理")
