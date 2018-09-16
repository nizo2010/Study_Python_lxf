# Chapter_028   继承和多态

1、 当定义一个class时，可以从某个现有的类继承，新定义的class称为子类（Subclass），而被继承的class称为基类、父类或超类（Base class、Super class）

2、 继承的子类可以获得父类的全部功能

3、 多态：父类和子类都存在相同的方法，子类的方法可以覆盖父类的同名方法

举例：
```python
class Animal(object):
    def run(self):
        print('Animal is running')
        
class Tortoise(Animal):
    def run(self):
        print('Tortoise is running slowly')
        
def run_twice(animal):
    animal.run()
    animal.run()
    
>>> run_twice(Animal())
Animal is running
Animal is running
>>> run_twice(Tortoise())
Tortoise is running slowly
Tortoise is running slowly
```

可以发现，新增一个Animal的子类，不必对 run_twice() 做任何修改

多态的好处时，当传入Dog、Cat、Tortoise...时，我们只需要接收Animal类型就可以了，因为Dog、Cat、Tortoise...都是Animal类型，按照Animal类或其子类，就会自动调用实际类型的run()方法。

4、 对于一个变量，我们只需知道它是Animal类型，无需确切知道它的子类型，就可以放心调用run()方法，而具体调用的run()方法是作用在Animal、Dog、Cat还是Tortoise对象上，由运行时该对象的确切类型决定。

这就是多态真正的威力：

调用方只管调用不管细节，当新增一个Animal的子类时，只要确保run()方法编写，不管原来的代码时如何调用的。

5、 “开闭”原则

对扩展开放：允许新增Animal子类
对修改封闭：不需要修改依赖Animal类型的run_twice()等函数

6、静态语言 vs 动态语言

1） 对于静态语言（如Java）来说，如果需要传入Animal类型，则传入的对象必须是Animal类型或者其子类型，否则无法调用run()方法

2） 对于Python这样的动态语言，则不需要一定传入的Animal类型，只需保证传入的对象是一个run()方法就可以，比如：

```python
class Timer(object):
    def run(self):
        print('start...')
```

3） 这就是动态语言的“鸭子类型”，它并不要求严格的继承体系，一个对象只要“看起来像鸭子，走起路来像鸭子“，那它就可以被看作是鸭子。

4） Python的”file-like object“就是一种鸭子类型。对真正的文件对象，它有一个read()方法，返回其内容。

但是许多对象只要有read()方法，都被视为“file-like object”，不一定
