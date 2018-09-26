# Chapter_043 操作文件和目录


## os模块

```python
os.name           # 操作系统类型（Posix表示Linux/UNIX/MAC OS X系统；NT表示Windows）
os.uname()        # 系统信息（Windows环境下不提供该方法函数）
os.environ        # 系统环境变量
os.environ.get('PATH')    # 获取'PATH'这个环境变量的值
os.environ.get('x', 'default')    # 若没有'x'这个环境变量，返回指定值'default'
```


## 操作文件和目录 os.path模块

操作文件和目录的函数一部分在os模块中，一部分在os.path模块中，这里主要介绍一些os.path模块中的函数。

```python
os.path.abspath('.')        # 查看当前目录的绝对路径
os.path.mkdir('/User/mochael', 'testdir')   # 将多个路径组合返回（并不会创建目录，即使这些路径不存在，也能组合返回）
os.mkdir('/User/michael/testdir')           # 创建目录（即在/User/michael目录下创建testdir目录）
                                            # 只能一级一级创建目录，若/User下没有michael目录就会报错
os.rmdir('/User/michael/testdir')           # 删除目录（不能删除非空目录）
os.path.split('/User/michael/testdir/file.txt')     
# 返回一个二元元组，('路径名', '文件名')
('/User/michael/testdir', 'file.txt')

os.path.splitext('/User/michael/testdir/file.txt')
# 返回一个二元元组，('文件路径（含文件名）', '扩展名')
('/User/michael/testdir/file', '.txt')

os.rename('test.txt', 'test.py')        # 重命名文件
os.remove('test.py')                    # 删除文件
# 注意，os模块没有复位copy的方法
```


## 其他方法

```python
os.listdir(path)                   # 返回path下包含的文件或文件夹名字的列表
os.path.dirname(path)                   # 返回path目录名，即os.path.split(path)返回元组中的第一个元素
os.path.basename(path)                  # 返回path文件名，即os.path.split(path)返回元组中的第二个元素
os.path.commonprefix(list)              # 返回list中，所有path共有的最长的路径名
os.path.exists(path)                    # 若path存在，返回True；否则返回False
os.path.isabs(path)                     # 若path是绝对路径，返回True；否则返回False
os.path.isfile(path)                    # 若path是存在的文件，返回True
os.path.isdir(path)                     # 若path是存在的目录，返回True
os.path.normcase(path)                  # 若是Linux和Mac OS X平台，则原样返回path
                                        # 若是Windows平台，则将路径中所有字符变成小写，并将所有斜杠变成反斜杠
                                        # os.path.normcase('c:/TestDir/TesT1.py') ===> 'c:\\testdir\\test1.py'
os.path.normpath(path)                  # 规范化路径
                                        # os.path.normpath('c://windows\\system32\\../Temp/') ===> 'c:\\windows\\Temp'
os.path.splitdrive(path)                # 返回(drivername, fpath)
                                        # Linux/Mac OS X平台下没有该函数
                                        # Windows平台下， os.path.splitedrive('c:\\windows')  ===> ('c:', '\\windows')
os.path.getsize(path)                   # 返回path文件的大小（字节）
os.path.getatime(path)                  # 返回path所指向的文件或目录的最后存取时间
os.path.getmtime(path)                  # 返回path所指向的文件或目录的最后修改时间
```


## 例子

```python
### 列出当前目录下所有目录
[x for x in os.listdir('.') if os.path.isdir(x)]

### 列出当前目录下所有的.py文件
[x for x in os.listdir('.') if os.path.isfile(x) and os.path.splitext(x)[1] == '.py']
```


## 课后作业

1、利用os模块编写一个能实现dir -l输出的程序

```python
# dir.py
import os

with os.scandir('.') as it:
    for entry in it:
        print(entry.name.encode('utf-8'))

# 运行结果
$ python dir.py    # 返回当前路径下（dir.py所在路径）所有文件和目录名
```

2、编写一个程序，能在当前目录以及子目录下查找文件名包含指定字符串的文件，并打印绝对路径

```python
# serch_file.py
import os

def searchFile(path, key):
    with open('search_result.txt', 'w') as f:
        with os.scandir(path) as it:
            for entry in it:
                if entry.is_file() and key in entry.name:
                    f.write(str(entry.path+'\n'))
                elif entry.is_dir():
                    searchFile(entry.path, key)
dirpath = input(r'请输入需要搜索的路径:')
key = input(r'请输入需要搜索的关键字:')
searchFile(dirpath, key)
```


## 链接

上一节 [Chapter_042 StringIO和BytesIO](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_042.md "Chapter_042 StringIO和BytesIO")

下一节 [Chapter_044 序列化](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_044.md "Chapter_044 序列化")
