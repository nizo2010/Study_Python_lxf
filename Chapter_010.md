# 列表生成式

```python
L = [function(n) for n in iter if condition(n)
```

对于iter中的每一个元素，先判断condition(n)为True，则进行function(n)运算并作为列表L中的元素。
