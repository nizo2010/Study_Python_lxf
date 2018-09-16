# Chapter_029   获取对象信息

## type()

type()判断对象类型

type模块中定义了除int、str等基本数据类型外的其他类型

```python
>>> import types
>>> dir(types)
['BuiltinFunctionType', 'BuiltinMethodType', 'CodeType', 'CoroutineType', 'DynamicClassAttribute', 'FrameType', 'FunctionType', 'GeneratorType', 'GetSetDescriptorType', 'LambdaType', 'MappingProxyType', 'MemberDescriptorType', 'MethodType', 'ModuleType', 'SimpleNamespace', 'TracebackType', '_GeneratorWrapper', '__all__', '__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', '_calculate_meta', '_collections_abc', '_functools', 'coroutine', 'new_class', 'prepare_class']
```


## isinstance()

isinstance()判断类型的实例

isinstance(object, type)， 其中type可以是多个类型组成的一个tuple，表示冬种类型中的一种

```python
>>> isinstance([1, 2, 3], (tuple, list))
True
>>> isinstance((1, 2, 3), (tuple, list))
True
```


## dir()

dir()，获得一个对象的所有属性和方法，以list形式返回

比如：

```python
>>> dir('BAC')
['__add__', '__class__', '__contains__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__gt__', '__hash__', '__init__', '__iter__', '__le__', '__len__', '__lt__', '__mod__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmod__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'capitalize', 'casefold', 'center', 'count', 'encode', 'endswith', 'expandtabs', 'find', 'format', 'format_map', 'index', 'isalnum', 'isalpha', 'isdecimal', 'isdigit', 'isidentifier', 'islower', 'isnumeric', 'isprintable', 'isspace', 'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip', 'maketrans', 'partition', 'replace', 'rfind', 'rindex', 'rjust', 'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines', 'startswith', 'strip', 'swapcase', 'title', 'translate', 'upper', 'zfill']
```

类似\_\_xxx\_\_的属性和方法有特殊用途，例如\_\_len\_\_()方法返回长度。

在Python中调用len()函数获取一个对象的长度，实际上在len()函数内部，它自动去调用该对象的\_\_len\_\_方法。

```python
>>> len('ABC')
3
>>> 'ABC'.__len__()
3
```

上面的两段代码等价。


## getattr(), setattr(), hasattr()

1） hasattr(a, 'x')

当对象 a 有属性 x 时，返回Tue；否则返回False

2） setattr(a, 'x', 'A')

无返回。给对象 a 的属性 x 赋值为 'A'；若对象 a 没有属性 x，则添加 x 并赋值为 'A'

3） getattr(a, 'x')

返回对象 a 的属性 x 的值；若没有属性 x ，则报错

4） getattr(a, 'x', 'A')

返回对象 a 的属性 x 的值；若没有属性
