# Chapter_005   dict和set

## dict 字典

dict 字典，在其他的编程语言中也成为 map（映射），使用键-值对（key-value）存储

判断一个 key 是否在字典 d 中：
```Python
'nizo' in d       #返回布尔值True/False
d.get('nizo')     #存在键为'nizo'，则返回其值；不存在该键，则返回None
d.get('nizo', 1)  #存在键为'nizo'，则返回其值；不存在该键，则返回指定值
```

删除一个key：
```Python
d.pop(key)
```


## set 集合

set 集合，一组 key 的集合，但不存储 value （由于key的特殊性，其不能重复，因此 set 中没有重复的元素）。
常用来将列表或者元组中的重复元素删除。
```Python
s.add(key)    #添加元素到 set 中，可以重复添加（不会报错，也没有任何效果）
s.remove(key) #删除元素
s.pop()       #随机弹出一个元素
```

需要注意的是最后一个，为什么随机，因为**集合是无序的**，因此**字典也是无序的**。


## 链接

上一节 [Chapter_004 循环](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_004.md "Chapter_004 循环")

下一节 [Chapter_006 函数的参数](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_006.md "Chapter_006 函数的参数")
