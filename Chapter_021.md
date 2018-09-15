# Chapter_021   偏函数

## functools.partial

将一个函数的某些参数给固定住（设置默认值），返回一个新的函数。

假设， int() 函数可以将字符串转换成整数，参数 base 可以做N进制转换， base 默认为10。

那么，做二进制转换有两种方式。

第一种方法：

```python
int(x, base = 2)    # 该方法比较繁琐，每次都需要指定 base ＝ 2
```

第二种方法，定义一个函数int2

```python
def int2(x, base = 2):
    return int(x, base)
```

后一种方式也可以使用偏函数实现：
```python
import functools

int2 = functools.partial(int, base = 2)
```

## 创建偏函数，实际可以接收函数对象、 \*args、 \*\*kw 三个参数

```python
int2 = functools.partial(int, base = 2)
```

相当于

```python
kw = {'base': 2}
int('10010' **kw)
```

```python
max2 = functools.partial(max, 10)
```

实际上，会把 10 作为 \*args 的一部分加入，即

```python
max2(5, 6, 7)
```

相当于

```python
args = (5, 6, 7, 10)
max(*args)
```
