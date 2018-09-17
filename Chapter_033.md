# Chapter_033   多重继承

## 多重继承的顺序

```python
class C(A, B):
    pass
```

假设父类A和B都有一个相同的方法run，而子类C没有run方法，则子类C的实例c，调用c.run()会使用父类A的run方法


## 链接

上一节 [Chapter_032 使用@property](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_032.md "Chapter_032 使用@property")

下一节 [Chapter_034 定制类](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_034.md "Chapter_034 定制类")
