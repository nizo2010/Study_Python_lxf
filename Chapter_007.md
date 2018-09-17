# Chapter_007   递归函数

汉诺塔
```Python
def move(n, a, b, c):         #将 n 个盘子从 a 杆经过 b 杆，移动到 c 杆上
    if n == 1:
        print(a, '--->', c)
    else:
        mova(n - 1, a, c, b)  #将 n - 1 个盘子从 a 杆，经过 c 杆，移动到 b 杆上
        move(1, a, b, c)      #将最后一个盘子从 a 杆，经过 b 杆，移动到 c 杆上
        move(n - 1, b, a, c)  #将 n - 1 个盘子从 b 杆，经过 a 杆，移动到 c 杆上
        
>>> move(3, 'A', 'B', 'C')
A ---> C
A ---> B
C ---> B
A ---> C
B ---> A
B ---> C
A ---> C
```


## 链接

上一节 [Chapter_006 函数的参数](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_006.md "Chapter_006 函数的参数")

下一节 [Chapter_008 切片](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_008.md "Chapter_008 切片")
