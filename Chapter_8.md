## Chapter 切片

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
