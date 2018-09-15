# Chapter_018 返回函数

## 函数作为返回值

```python
def lazy_sum(*args):
    def sum():
        ax = 0
        for n in args:
            ax = ax + n
        return ax
    return sum


>>> f = lazy_sum(1,3,5,7,9)
>>> f       # 返回的是一个sum()函数
<function lazt.sum.<locals>.sum ax 0x000000000310C048>
>>> f()     # 调用f()才是计算求和结果
25

```

函数lazy_sum()中又定义了函数sum()，并且内部函数sum()可以引用外部函数lazy_sum()的参数和局部变量，当lazy_sum()返回函数sum()时，相关参数和变量都保存在赶回的函数中，这种程序结构称为**“闭包（Closure）”**。

