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
