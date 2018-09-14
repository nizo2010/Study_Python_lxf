# Chapter_017   sorted()

## sorted(obj, key, reverse)

排序函数

```python
sorted([36, 5, -12, 9, -21])
# 返回[-21, -12, 5, 9, 36]
```

key 是一个函数，可以实现自定义排序，比如 key = abs（按绝对值大小排序）

```python
sorted([36, 5, -12, 9, -21], key = abs)
# 返回[5, 9, -12, -21, 36]

sorted(['bob', 'about', 'Zoo', 'Credit'], key = str.lower)
# 返回['about', 'bob', 'Credit', 'Zoo']
```


reverse 默认为False，若 reverse = True，则表示反序
```python
sorted([36, 5, -12, 9, -21], key = abs, reverse = True)
# 返回[36, -21, -12, 9, 5]
```
