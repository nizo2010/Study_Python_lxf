# Chapter_008 切片

trim() 函数，不调用 str 的 strip() 函数，实现去掉 str 的首尾空格：
```Python
def trim(s):
    if len(s) == 0:
        return s
    elif s[0] == ' ':   # 若 str 第一个字符为空格，则返回第2个字符后的切片
        return trim(s[1:])
    elif s[-1] == ' ':  # 若 str 最后一个字符为空格，则返回直到倒数第2个字符的切片
        return trim(s[:-1])
    return s
    
>>> print('"' + trim('   Hello world!   ') + '"')
"Hello world!"
```


## 链接

上一节 [Chapter_007 递归函数](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_007.md "Chapter_007 递归函数")

下一节 [Chapter_009 迭代](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_009.md "Chapter_009 迭代")
